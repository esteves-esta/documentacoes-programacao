## REDUX E SAGA
> As informações presentes nesse documento são uma forma de documentar meu entendimento sobre esse assunto. Abaixo estão algumas referências utilizadas.

### Referências

- [titulo](url)

### Redux


#### `redux`
O redux é uma forma de facilitar o fluxo de dados em uma aplicação, atráves dele pode-se criar um único local de entrada e saída de dados 
assim se criando um única **fonte de verdade** que é a **store** da aplicação.

#### `store`
A store então nada mais é que um "local" onde se armazena todos os dados importantes da aplicação, que são os nossos **states**.

```
// src/store/index.tsx

import {createStore} from 'redux';
import rootReducer from './ducks';

const store = createStore(
  rootReducer
);

```

```
// src/store/ducks/index.tsx

import {combineReducers} from 'redux';

import getModal from './Modal';

const rootReducer = combineReducers({
  getModal
});

export default rootReducer;

```

#### `state`
Um state é um conjunto de dados que são utilizados em muitos lugares de sua aplicação, e podem ser acessados por um hook do redux `useSelector(state => state.NomeDoReducer)`.
O `nomeDoReducer` é o nome dado ao reducer na função `combineReducers()`.
Como dito antes os dados só podem ser alterado de um local, entao para realizar essa ação é utilizado um **action** que chama um **reducer**.

```
// src/store/ducks/Modal/index.tsx

interface State {
  visible: boolean,
  message: string
}

export const INITIAL_STATE: State = {
  visible: false,
  message: undefined,
};

```


#### `action`
O action é um objeto que possui um tipo que comunica o que se deseja fazer com o state. ex: se quer receber os dados ou altera-los. 
Além do tipo pode ser enviado um sedundo atributo com dados a serem enviados para o state.

Um action pode ser chamado atráves do hook do redux `useDispatch({type: string, payload?: any}): Action`.

```
// src/store/ducks/Modal/index.tsx

export enum Types {
  SHOW_MODAL = 'MODAL/SHOW_MODAL',
  HIDE_MODAL = 'MODAL/HIDE_MODAL',
};

export interface Action {
  type: Types;
  message?: string
}

```

#### `reducer`
Um reducer nada mais é que uma função que recebe dois paramêtros `reducer(state, action)`, o primeiro parâmetro sendo o state atual e o segundo a action(ação) que deve 
ser realizada nesse state.
O reducer é de forma simples uma função que possui um switch case que verifica o tipo de ação recebida no action e realizada alguma alteração no state.

```
// src/store/ducks/Modal/index.tsx

export default function getModal(
  state: State = INITIAL_STATE, action: Action
): State {
  switch (action.type) {
    case Types.SHOW_MODAL: {
      return { visible: true, message: action.message };
    }
    case Types.HIDE_MODAL: {
      return { ...state, visible: false };
    }
    default:
      return state;
  }
}

```

### SAGA

#### `saga`
O saga é o local onde se realização ação asincronas na aplicação e lida com a distribuição dos dados asincronos para a **store**.
O saga é uma função generator `function*`, esse tipo de função se utiliza do `yield` para parar a execução em um ponto até receber o resultado asincrono.

#### `rootSaga`
Com o saga temos um saga raiz onde podemos conectar um tipo de ação (action) a uma função saga especifíca para um **state**.
Nesse saga raiz pode-se determinar se um saga será acionado em cada chamada de uma ação `takeEvery()` ou se será acionado somente uma unica vez com o `takeLatest()`.

```
// src/store/sagas/index.tsx

import {all, takeLatest} from 'redux-saga/effects';
import {Types as ModalTypes} from '~/store/ducks/Modal';
import {getModal} from './Modal';

export default function* rootSaga() {
  return yield all([
    takeLatest(ModalTypes.SHOW_MODAL, getModal),
  ]);
}
```

#### `stateSaga`
Temos então um saga para cada state onde pode ser realizado, por exemplo, uma chamada a uma api `yield call(api, params)`.

A resposta da api pode ser incluída no state chamada uma **action** pela função `yield put({type: string, payload?: any})` que funciona como o `useDispatch` 
onde pode ser chamada um tipo de ação e os dados que devem ser mandados ao state.

```
// src/store/sagas/Modal/index.tsx

import {call, put} from 'redux-saga/effects';
import api from '~/services/api';
import {Types as ModalTypes, Action} from '~/store/ducks/Modal';
import { AxiosResponse, AxiosError } from 'axios';

export function* sagaModal() {
  try {
    type Response = AxiosResponse<IResponse<string>>;
    const response: Response = yield call(api.get, '/modal/status');

    yield put<Action>({
      type: ModalTypes.SHOW_MODAL,
      data: response.data,
    });
  } catch (error) {
    const axiosError = error as AxiosError<IResponse>;
    const erroMensagem = error.message || axiosError.response.data.message;

    yield put<Action>({
      type: ModalTypes.HIDE_MODAL,
      error: erroMensagem
    });
  }
}

interface IResponse<T = null> {
  value: T,
  result: boolean,
  message: string,
}


```

## Testes

### Redux

```
// __tests__/ducks/Modal.test.tsx

import reducer, {
  INITIAL_STATE,
  Types,
} from '~/store/ducks/Modal';

describe('Modal Reducer', () => {
  it('if not passed an action to throw error', () => {
    expect(() => {
      reducer(undefined, undefined)
    }).toThrow();
  });

  it('must return the SHOW_MODAL', () => {
    expect(reducer(undefined, {type: Types.SHOW_MODAL, message: "hello world"})).toEqual({
      visible: true,
      message: "hello world"
    });
  });

  it('must return the HIDE_MODAL', () => {
    expect(
      reducer(undefined, {type: Types.HIDE_MODAL})
      ).toEqual({
        visible: false,
        message: undefined
    });
  });
});

```

### Saga

```
// __tests__/sagas/Modal.test.tsx
import sagaHelper from 'redux-saga-testing';
import {call, put} from 'redux-saga/effects';
import api from '~/services/api';
import { Types, Action } from '~/store/ducks/Modal';
import {sagaModal} from '~/store/sagas/Modal';

const actionShow = (message?: string): Action => ({type: Types.SHOW_MODAL, message });
const actionHide = (): Action => ({type: Types.HIDE_MODAL });

api.get = jest.fn();

describe('sagaModal', () => {
  describe('test sucessul return from api', () => {
    
    // ao inves de usar o it do jest estamos
    // criando o nosso proprio it atraves da biblioteca redux-saga-testing
    const it = sagaHelper(sagaModal());

    // mockando o resultado do axios
    const mockSucessResponse = {
      data: "hello world"
    };

    it('should call api with correct URI', sagaResult => {
      expect(sagaResult).toEqual(call(api.get, '/modal/status'));

      return mockSucessResponse;
    });

    it('should return with sucess and send SHOW_MODAL', sagaResult => {

      expect(sagaResult).toEqual(put(actionShow(mockSucessResponse.data)));
    });

    it('should end saga', sagaResult => {
      expect(sagaResult).toBeUndefined();
    });
  });

  describe('test error return from api', () => {
    
    const it = sagaHelper(sagaModal());

    // mockando o resultado do axios
    const mockErrorResponse = "found status 500";

    it('should call api with correct URI', sagaResult => {
      expect(sagaResult).toEqual(call(api.post, '/modal/status'));

      return new Error(mockErrorResponse);
    });

    it('should return with error and send action HIDE_MODAL', sagaResult => {
      expect(sagaResult).toEqual(put(actionError(mockErrorResponse)));
    });

    it('should end saga', sagaResult => {
      expect(sagaResult).toBeUndefined();
    });
  });

});

```
