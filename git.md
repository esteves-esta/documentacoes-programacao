# GIT

## Índice
- [Comandos gerais](#comandos-gerais)
- [GitFLow](#gitflow)
- [Extensões no Visual Studio Code](#extensões-no-visual-studio-code)
- [Referências](#referências)

---

## Comandos gerais

- Verificar se há commits novos sem adiciona-los ainda a seu repositório local:
`git fetch`

- Remover as alterações feitas em um arquivo:
`git checkout -- NOME-DO-ARQUIVO`

- Procurar todos os arquivos que contém determinada palavra
`git grep palavra`

### Verificar commits e alterações realizados

- Mostra todos os commit em uma linha:
`git log --oneline`

- Mostrar commits de forma resumida agrupada por autor do commit
`git shortlog`

- Visualizar com detalhes o que foi mudado no ultimo commit
`git show`

- Visualizar somente os commits/pulls/merges dados no reu repositório local
`git reflog`

- Visualizar arquivos alterados e ainda não commitados e quantidade de alterações
`git diff --stat`


### Resolver erros ao dar commit
- Quando há um commit no repositório local porém não foi dado pull antes e existens commits no repositório remoto: `git pull --rebase` (esse comando altera a 'cabeça' do seu repositório local). Se ocorrer conflito, utilizar o vscode para solucionar e realizar o merge, depois adicionar as alteções `git add .` e utilizar `git rebase --continue` e depois o `git push`.

- Reverter o commit que foi dado e já foi dado **git push**:
`git revert COMMIT-ID`

- Remove ultimo commit que não foi dado **git push** e mantem alterações dos arquivos:
`git reset --soft HEAD@{1}`

- Mudar mensagem do commit que ainda não foi dado o `git push`
`git commit --amend --only -m 'mensagens nova'`

- Adicionar mudanças ao ultimo commit que ainda não foi dado o `git push`
`git commit --amend -C HEAD`

- Se deu commit com nome/email errado 
`git commit --amend --no-edit --author "NOME CERTO <emailcerto@mydomain.com>"`

- Remover mudanças feitas em arquivo do ultimo commit

```
git checkout HEAD^ nome-arquivo
git add nome-arquivo
git commit --amend --no-edit
```

---

## GitFlow

![gitflow](https://salesforcegraells.files.wordpress.com/2017/10/gitflow-workflow.jpg)

É um tipo de fluxo de trabalho usando git que se utiliza de mais branchs do que a *master* e a *dev* para que exista mais organização e evitar também conflitos ao atualizar o repositório remoto ao dar `git push`.

Estes branchs, ao contrário do *master* e *dev* são excluidos após a finalização da melhoria/correções/lançamento e a mesclagem com o branch correto.

> A master irá contér todo código já testado, versionado que será entregue ao cliente e a develop é onde todo fluxo de trabalho irá ocorrer antes de fazer o release versionado que será feito merge na master. A develop deve sempre conter o código mais atual, onde as branchs de features serão ramificadas tendo ela como base.

São criados então além dos dois branchs mencionados branchs para fins específicos que especificações de qual branch deve se originar e qual deve ser mesclados.

### Melhorias (feature/novo-componente)
Esses branchs são criados da branch *dev* e são mesclados na *dev*.

### Lançamentos (release/numero-da-versao)
Esses branchs são criados da branch *dev* e são mesclados na *master e dev*.

> "Branches de lançamento são usados para preparação do lançamento da próxima versão de produção. Nele são permitidas pequenas correções e atualização de versão nos arquivos"

### Correções (hotifx/numero-do-hotfix)
Esses branchs são criados da branch *master* e são mesclados na *mater e dev*.


### Comandos git sobre branches

- Criar nova branch
`git branch -b feature/nome`

- Renomar branch
`git branch -m novo-nome`

- Deletar branch
`git branch -d nome`

- Ir para outro branch
`git checkout nome`

- Mostra branchs locais e remotos:
`git branch -a`

### Para entender mais sobre esse fluxo: 
- [Fluxo de versionamento de software com git flow](https://blog.ateliedocodigo.com.br/fluxo-de-versionamento-de-software-com-git-flow-b9f5195c679e)
- [Utilizando o fluxo Git Flow](https://medium.com/trainingcenter/utilizando-o-fluxo-git-flow-e63d5e0d5e04)


---


## Extensões no Visual Studio Code

### GitLens
O Visual Studio já possui diversas funcionalidades a utilizar o git, auxiliando a verificar os arquivos modificados e quais as mudanças realizadas; a remover todas as alterações de um arquivo e até dar um commit.

Também é possível utilizar a extensão GitLens que adiciona funcionalidades que permitem visualizar quem criou/alterou uma linha específica.

---


## Referências

- [GitSheet](https://gitsheet.wtf/?ref=producthunt)
- [Git FLight Rules](https://github.com/k88hudson/git-flight-rules#repositories)
- [Git CheatSheet](https://github.com/tiimgreen/github-cheat-sheet#git)
- [Git Cheat Sheet](https://github.com/bennadel/git-cheat-sheet)
- [A successful git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
- [Fluxo de versionamento de software com git flow](https://blog.ateliedocodigo.com.br/fluxo-de-versionamento-de-software-com-git-flow-b9f5195c679e)

