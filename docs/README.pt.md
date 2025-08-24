# GitHub Profile 3D Contrib

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

<!-- Ordem do código de idioma (exceto inglês) -->
[English (en)](../README.md) |
[Deutsch (de)](README.de.md) |
[Español (es)](README.es.md) |
[Français (fr)](README.fr.md) |
[日本語 (ja)](README.ja.md) |
[한국어 (ko)](README.ko.md) |
Português (pt) |
[Русский (ru)](README.ru.md) |
[简体中文 (zh-CN)](README.zh-CN.md) |
[繁體中文 (zh-TW)](README.zh-TW.md) |

> [!NOTE]
> Esta tradução foi gerada por tradução automática.
> Pode conter erros ou expressões não naturais.
> Contribuições para melhorar a tradução são bem-vindas!

## Visão geral

Esta GitHub Action cria um calendário de contribuições do GitHub em uma imagem de perfil 3D.

## Como usar (GitHub Actions) - Básico

Esta GitHub Action gera o calendário 3D de contribuições do seu perfil do GitHub e faz commit no seu repositório.
Após adicionar a GitHub Action, o workflow é executado automaticamente uma vez por dia.
Você também pode disparar o workflow manualmente.

### Passo 1. Crie um repositório especial de perfil

Crie um repositório no GitHub com o mesmo nome do seu nome de usuário.

- Por exemplo, se o nome de usuário for `octocat`, crie um repositório chamado `octocat/octocat`.
- Veja também: [Gerenciar o README do seu perfil](https://docs.github.com/pt/account-and-profile/how-tos/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme)

Neste repositório, siga os passos abaixo.

### Passo 2. Crie o arquivo de workflow

Crie um arquivo de workflow como o exemplo abaixo.

- `.github/workflows/profile-3d.yml`

```yaml
name: GitHub-Profile-3D-Contrib

on:
  schedule: # 03:00 JST == 18:00 UTC
    - cron: "0 18 * * *"
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest
    name: generate-github-profile-3d-contrib
    steps:
      - uses: actions/checkout@v5
      - uses: yoshi389111/github-profile-3d-contrib@latest
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          USERNAME: ${{ github.repository_owner }}
      - name: Commit & Push
        run: |
          git config user.name github-actions
          git config user.email github-actions@github.com
          git add -A .
          if git commit -m "generated"; then
            git push
          fi
```

> [!NOTE]
> Você pode alterar as configurações do GitHub para incluir contribuições de repositórios privados. Para alterar esta configuração, clique em `Configurações de contribuição` no canto superior direito do calendário padrão de contribuições, ou clique no seu ícone no canto superior direito da tela, selecione `Configurações` ⇒ `Perfil público` ⇒ `Contribuições & Atividade`, e marque `Incluir contribuições privadas no meu perfil`.
>
> Se quiser incluir atividades adicionais de repositórios privados, registre um token de acesso pessoal como segredo e defina-o na variável de ambiente `GITHUB_TOKEN` no arquivo de workflow. No entanto, na maioria dos casos, o padrão `secrets.GITHUB_TOKEN` é suficiente.

O agendamento está definido para executar uma vez por dia por padrão.
Você pode alterar o horário agendado como desejar.

Isso adicionará o workflow ao seu repositório.

#### Variáveis de ambiente

No exemplo, apenas `GITHUB_TOKEN` e `USERNAME` são especificados como variáveis de ambiente, mas você pode especificar as seguintes variáveis de ambiente:

- `GITHUB_TOKEN` : (obrigatório) token de acesso
- `USERNAME` : (obrigatório) nome de usuário alvo (ou especifique como argumento).
- `MAX_REPOS` : (opcional) máximo de repositórios, padrão 100 - desde ver. 0.2.0
- `SETTING_JSON` : (opcional) caminho do arquivo json de configurações. Veja `sample-settings/*.json` e `src/type.ts` no repositório `yoshi389111/github-profile-3d-contrib` para detalhes. - desde ver. 0.6.0
- `GITHUB_ENDPOINT` : (opcional) endpoint GraphQL do Github. Por exemplo, se quiser criar um calendário de contribuições baseado na atividade do seu GitHub Enterprise da empresa em vez do GitHub.com, defina esta variável de ambiente. Exemplo: `https://github.mycompany.com/api/graphql` - desde ver. 0.8.0
- `YEAR` : (opcional) Para calendários passados, especifique o ano. Destinado a ser especificado ao executar a ferramenta pela linha de comando. - desde ver. 0.8.0

#### Sobre o `GITHUB_TOKEN`

O `secrets.GITHUB_TOKEN` definido na variável de ambiente `GITHUB_TOKEN` no exemplo é um token de acesso especial criado automaticamente pelo GitHub.

- GitHub Docs: [Usar GITHUB_TOKEN para autenticação em fluxos de trabalho](https://docs.github.com/pt/actions/tutorials/authenticate-with-github_token)

Se quiser gerar um calendário de contribuições apenas para repositórios públicos, use este valor.
Não é necessário criar um segredo manualmente.

Além disso, se quiser incluir atividade em seus repositórios privados no calendário de contribuições, marque "Incluir contribuições privadas no meu perfil" na seção "Configurações de perfil" em "Perfil público" nas configurações do seu perfil.

Além disso, se quiser incluir informações adicionais de atividade de repositórios privados, crie um token de acesso com as permissões apropriadas.
Registre esse token de acesso como um segredo com qualquer nome que desejar (por exemplo, `MY_PERSONAL_ACCESS_TOKEN`).
No entanto, observe que segredos criados pelo usuário não podem começar com `GITHUB_`.

- GitHub Docs: [Segredos](https://docs.github.com/pt/actions/concepts/security/secrets)

Defina esse segredo como valor da variável de ambiente `GITHUB_TOKEN`.

```diff
          env:
-           GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
+           GITHUB_TOKEN: ${{ secrets.MY_PERSONAL_ACCESS_TOKEN }}
            USERNAME: ${{ github.repository_owner }}
```

#### Sobre o horário do agendamento

No exemplo, está definido para iniciar às 18:00 UTC.
Isso porque será executado à meia-noite JST, que é o horário local do autor.

```yaml
on:
  schedule: # 03:00 JST == 18:00 UTC
    - cron: "0 18 * * *"
```

Você pode alterar para qualquer horário que desejar.
Recomendamos meia-noite (por volta das 3h) no seu horário local.
No entanto, observe que o horário deve ser especificado em UTC.

### Passo 3. Execute manualmente esta GitHub Action

Na primeira vez, execute este workflow manualmente.

- `Actions` -> `GitHub-Profile-3D-Contrib` -> `Run workflow`

As imagens de perfil são geradas nos seguintes caminhos:

- `profile-3d-contrib/profile-green-animate.svg`
- `profile-3d-contrib/profile-green.svg`
- `profile-3d-contrib/profile-season-animate.svg`
- `profile-3d-contrib/profile-season.svg`
- `profile-3d-contrib/profile-south-season-animate.svg`
- `profile-3d-contrib/profile-south-season.svg`
- `profile-3d-contrib/profile-night-view.svg`
- `profile-3d-contrib/profile-night-green.svg`
- `profile-3d-contrib/profile-night-rainbow.svg`
- `profile-3d-contrib/profile-gitblock.svg`

Se você especificar a variável de ambiente `SETTING_JSON` sem a propriedade `fileName` no arquivo json, a seguinte imagem será gerada:

- `profile-3d-contrib/profile-customize.svg`

Você pode usar essas imagens no seu README.md como mostrado abaixo.

Exemplo: versão verde

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-green-animate.svg)

Exemplo: versão de estação (Hemisfério Norte.)

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-season-animate.svg)

Exemplo: versão de estação (Hemisfério Sul.)

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-south-season-animate.svg)

Exemplo: versão visão noturna

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-view.svg)

Exemplo: versão verde noturna

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-green.svg)

Exemplo: versão arco-íris noturna

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-rainbow.svg)

Exemplo: versão git block

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

### Passo 4. Adicione a imagem ao README.md

Adicione o caminho para a imagem gerada no seu arquivo README.

Exemplo:

```md
![](./profile-3d-contrib/profile-green-animate.svg)
```

## Como usar (GitHub Actions) - Exemplos avançados

- [Mais informações em EXAMPLES.md](../EXAMPLES.md)

## Como usar (local)

Defina a variável de ambiente `GITHUB_TOKEN` para seu token de acesso pessoal.

```sh
export GITHUB_TOKEN=XXXXXXXXXXXXXXXXXXXXX
```

Execute o seguinte comando, substituindo `USER_NAME` pelo seu nome de usuário do GitHub ou o nome de usuário alvo.

```sh
node_modules/.bin/ts-node src/index.ts USER_NAME
```

ou

```sh
npm run build
node . USER_NAME
```

## Licença

&copy; 2021 SATO Yoshiyuki. Licenciado sob a Licença MIT.
