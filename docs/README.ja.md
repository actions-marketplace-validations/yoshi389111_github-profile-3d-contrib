# GitHub Profile 3D Contrib

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

<!-- 言語コードの順序（英語を除く） -->
[English (en)](../README.md) |
[Deutsch (de)](README.de.md) |
[Español (es)](README.es.md) |
[Français (fr)](README.fr.md) |
日本語 (ja) |
[한국어 (ko)](README.ko.md) |
[Português (pt)](README.pt.md) |
[Русский (ru)](README.ru.md) |
[简体中文 (zh-CN)](README.zh-CN.md) |
[繁體中文 (zh-TW)](README.zh-TW.md) |

## 概要

この GitHub Action は GitHub のコントリビュートカレンダーの 3D 版を SVG で作成します。

## 使い方 (GitHub Actions) - 基本

このアクションは、GitHub プロファイルの3D 版のコントリビュートカレンダー（いわゆる芝生）の 3D 版の SVG を GitHub プロフィール用に作成し、リポジトリにコミットします。

このアクションを追加した後、ワークフローは1日1回自動で実行されます。
手動でワークフローをトリガーすることもできます。

### 手順 1. スペシャルなリポジトリを作る

ユーザー名と同じ名前で GitHub にリポジトリを作成してください。

- 例：ユーザー名が `octocat`の場合は、`octocat/octocat` という名前のリポジトリを作成します。
- 参考：[プロフィールの README を管理する](https://docs.github.com/ja/account-and-profile/how-tos/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme)

このリポジトリで、以降の手順を実行します。

### 手順 2. ワークフローファイルを作る

以下のようなワークフローファイルを作成します。

- `.github/workflows/profile-3d.yml`

```yaml:.github/workflows/profile-3d.yml
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
> GitHubの設定を変更することで、プライベートリポジトリからのコントリビュートも含めることができます。この設定を変更するには、標準のコントリビュートカレンダーの右上にある「Contribution settings」をクリックするか、画面右上のアイコンをクリックして「Settings」⇒「Public profile」⇒「Contributions & Activity」を選択し、「Include private contributions on my profile」にチェックを入れてください。
>
> さらに、プライベートリポジトリの追加活動も含めたい場合は、適切な権限を持つ personal access token をシークレットとして登録し、ワークフローファイルの `GITHUB_TOKEN` に設定してください。
> 通常はデフォルトの `secrets.GITHUB_TOKEN` で十分だと思います。

デフォルトでは、スケジュールは1日に1回実行されるように設定されています。
スケジュール時刻は必要に応じて変更できます。

これにより、アクションがリポジトリに追加されます。

#### 環境変数

サンプルでは `GITHUB_TOKEN` と `USERNAME` のみ指定していますが、以下の環境変数を指定できます：

- `GITHUB_TOKEN` : (必須) アクセストークン
- `USERNAME` : (必須) 対象のユーザー名. （あるいは引数で指定する）
- `MAX_REPOS` : (任意) 最大のリポジトリ数。デフォルトは100 - バージョン 0.2.0 で追加
- `SETTING_JSON` : (任意) 設定JSONファイルパス。詳細は `yoshi389111/github-profile-3d-contrib` リポジトリの `sample-settings/*.json` や `src/type.ts` を参照してください - バージョン 0.6.0 で追加
- `GITHUB_ENDPOINT` : (任意) Github GraphQL エンドポイント。たとえば、GitHub.comではなく、会社のGitHub Enterpriseのアクティビティに基づいてコントリビュートカレンダーを作成したい場合は、この環境変数を設定します。例： `https://github.mycompany.com/api/graphql` - バージョン 0.8.0 で追加
- `YEAR` : (任意) 過去のカレンダーを出力する場合、年を指定。特にコマンドラインからツールを実行するときを想定しています。 - バージョン 0.8.0 で追加

#### `GITHUB_TOKEN` について

サンプルで `GITHUB_TOKEN` 環境変数に設定している `secrets.GITHUB_TOKEN` は、github が自動で作成する特別なアクセストークンです。

- GitHub Docs: [ワークフローでの認証に GITHUB_TOKEN を使用する](https://docs.github.com/ja/actions/tutorials/authenticate-with-github_token)

パブリックリポジトリのみを対象にコントリビューションカレンダーを生成したい場合には、この値を登録してください。
手動でシークレットを作る必要はありません。

また、プライベートリポジトリでの活動状況をコントリビューションカレンダーに含めたい場合には、ユーザーの設定項目にある「Public profile」の「Contributions & Activity」で「Include private contributions on my profile」にチェックを付けてください。

さらに、プライベートリポジトリでの追加活動情報も集計の対象にしたい場合などでは、適切な権限を持ったアクセストークンを作成してください。
そのアクセストークンを、シークレットとして任意の名前で登録してください（例えば、`MY_PERSONAL_ACCESS_TOKEN`）。
ただし、ユーザ作成のシークレットは、`GITHUB_` から始めることはできないので、注意してください。

- GitHub Docs: [シークレット](https://docs.github.com/ja/actions/concepts/security/secrets)

そのシークレットを `GITHUB_TOKEN` 環境変数に指定してください。

```diff
          env:
-           GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
+           GITHUB_TOKEN: ${{ secrets.MY_PERSONAL_ACCESS_TOKEN }}
            USERNAME: ${{ github.repository_owner }}
```

#### スケジュール時間について

サンプルでは UTC の 18 時に起動するようにしています。
これは作者のローカルタイムである JST での深夜に動作させるためです。

```yaml
on:
  schedule: # 03:00 JST == 18:00 UTC
    - cron: "0 18 * * *"
```

実際に使用する場合には任意の時間に変更してもらって問題ありません。
おすすめは、あなたのローカルタイムでの深夜（午前3時頃）です。
ただし指定は UTC でする必要があるので、注意してください。

### 手順 3. アクションを手動起動する

追加したアクションを起動してください。

- `Actions` -> `GitHub-Profile-3D-Contrib` -> `Run workflow`

プロフィール画像は以下のパスで生成されます。

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

あるいは、`SETTING_JSON` を指定した場合は、以下のファイルが生成されます。

- `profile-3d-contrib/profile-customize.svg`

これらの画像を README.md で利用できます。

例：green バージョン

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-green-animate.svg)

例：season バージョン（北半球）

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-season-animate.svg)

例：season バージョン（南半球）

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-south-season-animate.svg)

例：night view バージョン

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-view.svg)

例：night green バージョン

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-green.svg)

例：night rainbow バージョン

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-rainbow.svg)

例：git block バージョン

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

### 手順 4. README.md を追加

生成された画像のパスを readme ファイルに追加します。

例：

```md
![](./profile-3d-contrib/profile-green-animate.svg)
```

## 使い方 (GitHub Actions) - 高度な例

- [詳細は EXAMPLES.md を参照](../EXAMPLES.md)

## 使い方 (ローカル)

環境変数 `GITHUB_TOKEN` には「personal access token」を指定してください。

```sh
export GITHUB_TOKEN=XXXXXXXXXXXXXXXXXXXXX
```

`USER_NAME` をあなたの GitHub ユーザー名または対象としたいユーザー名に置き換えて、次のコマンドを実行します。

```sh
node_modules/.bin/ts-node src/index.ts USER_NAME
```

または

```sh
npm run build
node . USER_NAME
```

## License

&copy; 2021 SATO Yoshiyuki. Licensed under the MIT License.
