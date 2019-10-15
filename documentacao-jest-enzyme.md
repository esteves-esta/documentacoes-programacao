# Documentação sobre Testes em Componentes

## Índice

* Diferenças entre Jest e Enzyme
* Jest
 - Configuração no package.json
 - Principais métodos
* Enzyme
 - Arquivo de configuração
 - Principais métodos
	- Shallow
	- Render
	- Mount
	-  Diferenças entre it e test
* React Native Testing Library
 - Exemplo de uso
* Solução de erros
* Referências

## Diferenças entre Jest e Enzyme

O Jest foi criado pelo Facebook e já vem incluso nos projetos React Native. Jest possui ferramentas para 'imitar'(mock) funções e faze asserções.

O Enzyme é feito pelo Airbnb é usado para renderizar componentes tendo elementos que permitem renderizar só o componente pai e não seus filhos além de poder acessar as *props* e simular eventos e states dos componentes. 

Além disso é necessário uma configuração específica para usa-lo em React Native, além de que ele precisa do Jest ou alguma outra biblioteca de testes. Também há o fato do Airbnb não estar mais usando React Native em seu próprio aplicativo o que pode influenciar o foco do Enzyme para o React e não para o React Native


---

## Jest

### Configuração no package.json

Após a instalação pode-se adicionar uma configuração mais específica conforme seu projeto, no código abaixo temos, por exemplo:

- a conexão do jest com o enzyme linkando o arquivo de configuração do Enzyme em `setupFilesAfterEnv`;
- a configuração qual o caminho dos testes em `testMatch`
- o caminho que queremos que a pasta de cobertura fique em `coverageDirectory`;
- e quais arquivos devem ou não ser incluídos na cobertura em `collectCoverageFrom`;
- outras configurações se encontram nesse link [da documentação](https://jestjs.io/docs/en/configuration.html#collectcoveragefrom-array).

``` "jest": {
    "snapshotSerializers": ["enzyme-to-json/serializer"],
    "preset": "@testing-library/react-native",
    "setupFilesAfterEnv": [
      "@testing-library/jest-native/extend-expect",
      "<rootDir>/enzyme.config.js"
    ],
    "testMatch": [
      "**/__tests__/**/*.test.js",
      "**/__tests__/**/**/*.test.js"
    ],
    "collectCoverageFrom": [
      "src/**/*.{js,jsx}",
      "!src/service/api.js",
      "!**/node_modules/**",
      "!**/android/**",
      "!**/ios/**",
      "!**/metro-cache/**"
    ],
    "coverageDirectory": "__tests__/coverage",
    "moduleNameMapper": {
      "^~/(.*)": "<rootDir>/./$1"
    }
 }
```

### Principais métodos

O Jest possui o `.toJSON()` que cria um arquivo que descreve todo o layout do componente em um arquivo json, como um histórico. E utilizando o método `.toMatchSnapshot()` ao testar ele compara o teste com o arquivo criado anteriormente.

Ao utilizar o snapshot sempre quando houver alterações intencionais de mudança de layout é necessário atualizar o snapshot do elemento. Utiliza o comando `test:update` configurado no package.json = `"test:update": "jest --verbose --coverage --updateSnapshot"`.

Se utilizar a biblioteca **styled-components** também é possivel utilizar a biblioteca **jest-styled-components** que auxilia testar se está sendo usada regras corretas de estilo (css).

O **jest-styled-components** utiliza o snapshot e métodos para verificar as regras de css como o método`.toHaveStyleRule('padding', '0')`;


### Exemplo de Uso Real

O exemplo abaixo é de um testes feito em um componente criado com **styled-componets** que utiliza o **jest** e o **jest-styled-components**.

``` import 'react-native';
import React from 'react';
import renderer from 'react-test-renderer';
import { colors } from '~/styles';
import { ProgressBar } from '~/globalComponentStyle/ProgressBar/styles';
import 'jest-styled-components/native';

describe('ProgressBar', () => {
    it('Renders with correct color and width', () => {
      const tree = renderer.create(
        <ProgressBar
          color={colors.grayDark} 
          valueReached={100}
        />
      ).toJSON();

      expect(tree).toMatchSnapshot();
      expect(tree).toHaveStyleRule('background-color', colors.grayDark);
      expect(tree).toHaveStyleRule('width', '100%');
    });
  });
```

---

## Enzyme

### Arquivo de configuração

Após realizar a instalação com o comando abaixo do enzyme e do adaptador (necessário somente no react-native): 
`npm install --save-dev enzyme enzyme-adapter-react-16`

Após instalar a biblioteca, e se for utilizar o Jest instalar a dependência `jest-enzyme`, é necessário criar um arquivo de configuração `enzyme.config.js`. Neste arquivo há o seguinte código:

``` import Enzyme from 'enzyme';
    import Adapter from 'enzyme-adapter-react-16';

    Enzyme.configure({ adapter: new Adapter() });
```

### Principais métodos

Enzyme é uma biblioteca de utilitarios, com ele é possivel testar a saida dos seus componentes, tanto separadamente quando junto ao seus filhos. Basimente ele manipula a API do jquery, para conseguir uma interação com a DOM  e assim, manipula-lá. Há três métodos de testes que esta API fornece, lembrando que no React Native suporta mais o shallow rendering do que o mount pois o React Native não possui o DOM (Document Object Model) usado no HTML e XML. É possivel utilizar o mount com o **JSDOM** porém é necessário adicionar outras bibliotecas para seu uso.

#### Shallow rendering

Testa seu componente como unidade, e observa seu comportamento sem a interferencia de um componente filho. 

Exemplo:
	
```import {shallow} from 'enzyme';
import button from './Components/button';

describe('<Button/>', ()=>{
 it('Rendered component', ()=>{
	const testeComponent= shallow(<button/>);
	expect(testeComponent).tomatchSnapshot();
 });
});
```
	
* O Shallow simples chama: Constructor e render;
* Shallow + setProps chama : componentWillReceiveProps, shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate;
* Shallow + unmount chama componentWillUnmount;

#### Mount 

Ele funciona diferente do shallow, podemos dizer que ele é um teste de integração entre o elemento e seus filhos, com ele podemos saber se os filhos e o pai se comportam como o esperado. É mais utilizável quando precisamos testar componentes direto com a API DOM pois assim conseguimos analisar seu comportamento todo geral na DOM, algo muito  interessante é que assim ele tem acesso ao ciclo de vida do componente.

* Simple Mount chama: os métodos constructor, render, componentDidMount;
* Mount + setProps chama: ComponentWillReceiveProps, shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate.
* Mount + unmount chama: componentWillUnmount;

#### Render

Ele renderiza tudo, todos os filhos sem chamar o método render;

Há métodos que não são necessários e que são bem conhecidos:

- `describe('instruction', fn)`;
É um bloco que agrupa vários casos de testes. Mesmo podendo usa-lo para vários testes diferentes, uma boa opção é usa-lo para separar os testes em bloco que tenham um contexto semelhante;

Exemplo :


```
const user = {
	name:'Geovanna',
	idade:18,
} 

describe('testando valores', ()=>{
	test('se isso fizer essa coisa', ()=>{
		expect(user.name).toEqual('Geovanna');
	})
	test('se isso fizer essa coisa',()=>{
		expect(user.idade).toEqual(18);
	})
})
```

#### Diferenças entre It e test:

Basicamente não há muitas diferenças entre eles, o que destaca é o modo de leitura. No exercicio acima eu coloquei como se lê um código com `test`: "se isso fizer essa coisa...", com o `it` se lê : "deveria fazer essa coisa".


## React Native Testing Library

É uma biblioteca que já vem ao iniciar um projeto react-native que auxilia nos testes juntamente com jest.

> `npm install --save-dev @testing-library/react-native`

Essa biblioteca permite testar elementos específicos dentro do seu componente atráves da procura dele por seu texto, o valor de seu input, com labels de acessibilidade e por uma id de teste, a `testID`. 

Exemplo: `const { getByTestId, getByText, queryByTestId, baseElement } = render(<Example />);`

Entre suas funcionalidades estão:
- possibilidade de 'clicar', `fireEvent()` em um botão/componente;
- modificar o texto de um input;
- utilizar o `wait` junto do `await` para testar eventos assincrônos;


### Exemplo de Uso

```
import React from 'react';
import { fireEvent, render, wait } from '@testing-library/react-native';

test('examples of some things', async () => {
  const { getByTestId, getByText, queryByTestId, baseElement } = render(<Example />);
  const famousWomanInHistory = 'Ada Lovelace';

  const input = getByTestId('input');
  fireEvent.changeText(input, famousWomanInHistory);

  const button = getByText('Print Username');
  fireEvent.press(button);

  await wait(() => expect(queryByTestId('printed-username')).toBeTruthy());

  expect(getByTestId('printed-username').props.children).toBe(famousWomanInHistory);
  expect(baseElement).toMatchSnapshot();
});
```
fonte: [RNTL](https://www.native-testing-library.com/docs/example)

---

## Solução de erros

Caso utilize o comando `npm test` no terminal e não for retornado nada é possivel que seja erro no watchman (se estiver em ambiente Linux). Algumas soluções possíveis:

- 1 (aumenta numero de inotify e guarda permanentemente no arquivo)
`echo 999999 | sudo tee -a /proc/sys/fs/inotify/max_user_watches && echo 999999 | sudo tee -a /proc/sys/fs/inotify/max_queued_events && echo 999999 | sudo tee -a /proc/sys/fs/inotify/max_user_instances && watchman shutdown-server && sudo sysctl -p`

- 2
`watchman shutdown-server`

- 3
`watchman watch-del-all`
---

## Referências

- [Testing with Jest and Enzyme in React ](https://blog.usejournal.com/testing-with-jest-and-enzyme-in-react-part-1-162ce7466128)

- [What is the best React testing platform other than Jest?](https://www.quora.com/What-is-the-best-React-testing-platform-other-than-Jest)

- [How to test React Components](https://www.robinwieruch.de/react-testing-tutorial)

- [How to set up Jest and Enzyme to test React Native apps/](https://www.freecodecamp.org/news/setting-up-jest-enzyme-for-testing-react-native-40393ca04145/)

- [Choosing a React Native Component Testing Library: Enzyme vs. React Native Testing Library](https://codingitwrong.com/2019/08/07/enzyme-vs-react-native-testing-library.html)

- [Sunsetting React Native](https://medium.com/airbnb-engineering/sunsetting-react-native-1868ba28e30a)

- [Live do Rocketseat sobre Jest com React](https://www.youtube.com/watch?v=aK-wHbSD3Ws)

- [The user limit on the total number of inotify watches was reached #163](https://github.com/facebook/watchman/issues/163)

- [User limit of inotify watches reached on Ubuntu 16.04](https://askubuntu.com/questions/770374/user-limit-of-inotify-watches-reached-on-ubuntu-16-04)

