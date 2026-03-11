Guardando as respostas do desafio para não perder ou esquecer o que foi feito.

## Desafio 1 - Criar a branch feature-login e remover o commit "login implementado" da main

Primeiro precisamos criar uma branch a partir da main chamada feature-login.

O comando git checkout com o parametro -b é usado para criar uma nova branch e mudar para ela imediatamente. O nome da branch que queremos criar é feature-login, então o comando completo fica:

```bash
git checkout -b feature-login
```

Agora que criamos a branch, precisamos remover o commit "login implementando" da main. Para isso, vamos utilizar o git rebase.

Primeiro, precisamos ter uma clareza sobre o histórico de commits para identificar o commit que queremos remover. Usaremos o comando git log para visualizar o histórico de commits:

```bash
git log --oneline
```

Depois de identificar o commit, grave a quantidade de commits que estão acima do commit. Vamos supor que sejam 2 commits, o git rebase espera um hash do commit ou o números de commits anterior a HEAD para fazer a rebase, então o comando para remover o commit seria:

```bash
git rebase -i HEAD~2
```

O git vai abrir um editor de texto com a lista dos commits. Para remover o commit, basta substituir a palavra "pick" no começo da linha do commit por "drop". Depois de salvar e fechar o editor, pode ser necessário resolver conflitos, caso existam, e depois finalizar o rebase com:

```bash
git rebase --continue
```

Como os commits foram reescritos, é necessário forçar o push para atualizar o repositório remoto:

```bash
git push --force-with-lease origin main 
```

## Desafio 2 - Criar uma branch hotfix seguindo as boas práticas do gitflow, colocar o commit na main, criar a release e tag para produção

O commit hotfix não deveria existir na main, precisamos criar uma branch de hotfix e realizar o merge para a main, posteriormente.

Visualize o histórico de commits novamente, para identificar o commit de hotfix. Vamos criar uma branch a partir do commit anterior ao commit de hotfix. A estratégia é realizar o cherry-pick do commit de hotfix para a branch que vamos criar, e depois realizar o merge para a main.

No comando de exemplo, vamos supor que o hash do commit anterior seja HOTFIX_HASH.

```bash
git checkout -b hotfix/conserta-producao HOTFIX_HASH
``` 

Depois de criar a branch, vamos realizar o cherry-pick do commit de hotfix para a branch que acabamos de criar. Supondo que o hash do commit de hotfix seja HOTFIX_COMMIT_HASH, o comando seria:

```bash
git cherry-pick HOTFIX_COMMIT_HASH
``` 

Agora vamos consertar a branch main. O commit de hotfix ainda existe na main, vamos remover com o `git reset`. Vamos utilizar o mesmo hash do commit anterior ao hotfix para "resetar" a main. O comando seria:

```bash
git checkout main
git reset --hard HOTFIX_HASH
```

O comando `git reset --hard` vai mover o ponteiro (HEAD) para o commit especificado e descartar todas as mudanças feitas depois desse commit.

Agora podemos realizar o merge da branch de hotfix para a main:

```bash
git merge --no-ff hotfix/conserta-producao -m ""Hotfix: conserta produção""
```

Depois de realizar o merge, vamos criar a branch de release e a tag para produção.

```bash
git checkout -b release/v1.0.0
git tag -a v1.0.0 -m "Release v1.0.0"
```
Agora precisamos realizar o push da main, da branch de release e da tag para o repositório remoto:

```bash
git push origin main
git push origin release/v1.0.0
git push origin v1.0.0
```

## Desafio 3 - Remover o commit "teste temporario"

Primeiro, precisamos identificar o commit "teste temporario" utilizando o comando `git log --oneline`. Depois de identificar o commit, vamos utilizar o comando `git rebase -i` para remover o commit da história.

Precisamos do commit antes do commit "teste temporario" para realizar o rebase. O comando seria:

```bash
git rebase -i e792a66
```

No editor de texto, basta substituir a palavra "pick" por "drop" no commit "teste temporario". Depois de salvar o arquivo, pode surgir conflitos, resolva e depois continue o rebase com:

```bash
git add . # ou git add <arquivo_resolvido>
git rebase --continue
``` 

Depois de finalizar o rebase, é necessário forçar o push para atualizar o repositório remoto:

```bash
git push --force-with-lease origin main
```

## Desafio 4 - Remover o merge "Merge branch 'feature-temp'"

Mesmo processo do desafio anterior, precisamos identificar o commit de merge utilizando o comando `git log --oneline`. Depois de identificar o commit, vamos utilizar o comando `git rebase -i` para remover o commit da história.

Precisamos do commit antes do commit de merge para realizar o rebase. Identifique o commit, realize a alteração no editor de texto, finalie o rebase e depois faça o push para atualizar o histórico no repositório remoto. 

## Desafio 5 - Alterar a mensagem do commit "correção final???" 

O processo é similar aos anterior, precisamos do rebase interativo para alterar a mensagem do commit. 

Identifique o commit "correção final???" utilizando o comando `git log --oneline`. Depois de identificar o commit, vamos utilizar o comando `git rebase -i` para alterar a mensagem do commit.

Dessa vez, ao invés de substituir a linha do commit por "drop", vamos substituir por "reword ou r". Depois de salvar o arquivo, o git vai solicitar que seja editada a mensagem do commit em um outro arquivo de texto. Altere a mensagem, e finalize o rebase.

Depois de finalizar o rebase, execute o push para o repositório remoto.


## Desafio 6 - Recuperar o arquivo "config.js" sem reverter todo o commit

Primeiro, precisamos identificar o commit onde o arquivo "config.js" foi perdido. Utilize o comando `git log --oneline` para identificar o commit.

Utilizei o comando `git log --oneline --name-only` para visualizar o histórico de commits junto com os arquivos modificados em cada commit. Depois de identificar o commit onde o arquivo "config.js" foi perdido, vamos utilizar o comando `git checkout` para recuperar o arquivo.

Eu fiz o mesmo processo que o desafio do hotfix. Criei uma branch a partir da main, e depois utilizei o comando git restore --source=<COMMIT> -- config.js para recuperar o arquivo. O comando seria:

```bash
git checkout -b recover-config
git restore --source=<COMMIT_HASH> -- config.js
```

Depois é só adicionar o arquivo recuperado, realizar o commit e depois o push para o repositório remoto:

```bash
git add config.js
git commit -m "Recupera o arquivo config.js"
git push origin recover-config
```
Dá pra utilizar um comando mais robusto do git log, para uma working tree mais robusta:

```bash
git log --all --full-history -- <filename>
```