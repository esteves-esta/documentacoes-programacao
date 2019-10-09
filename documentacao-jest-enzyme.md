# Documentação sobre Testes em Componentes

## Índice

* Diferenças entre Jest e Enzyme
* Jest
 - Configuração no package.json
 - Principais métodos
 - Exemplo de Uso Real
* Enzyme
 - Arquivo de configuração
 - Principais métodos
 - Exemplo de Uso Real
* Solução de erros
* Referências

## Diferenças entre Jest e Enzyme

### Jest

O Jest possui o `.toJSON()` que cria um arquivo que descreve todo o layout do componente em um arquivo json, como um histórico. E utilizando o método `.toMatchSnapshot()` ao testar ele compara o teste com o arquivo criado anteriormente.

Ao utilizar o snapshot sempre quando houver alterações intencionais de mudança de layout é necessário atualizar o snapshot do elemento. Utiliza o comando `test:update` configurado no package.json = `"test:update": "jest --verbose --coverage --updateSnapshot"`

### Enzyme

Já o Enzyme possui o `shallow()`

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

Se utilizar a biblioteca `styled-components` também é possivel utilizar a biblioteca `jest-styled-components` que auxilia testar se está sendo usada regras corretas de estilo (css).

### Principais métodos

### Exemplo de Uso Real

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

### Exemplo de Uso Real

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
- [Live do Rocketseat sobre Jest com React](https://www.youtube.com/watch?v=aK-wHbSD3Ws)
- [The user limit on the total number of inotify watches was reached #163](https://github.com/facebook/watchman/issues/163)
- [User limit of inotify watches reached on Ubuntu 16.04](https://askubuntu.com/questions/770374/user-limit-of-inotify-watches-reached-on-ubuntu-16-04)
- 
