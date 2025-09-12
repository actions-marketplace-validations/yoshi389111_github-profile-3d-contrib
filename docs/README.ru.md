# GitHub Profile 3D Contrib

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

<!-- Порядок кодов языков (кроме английского) -->
[English (en)](../README.md) |
[Deutsch (de)](README.de.md) |
[Español (es)](README.es.md) |
[Français (fr)](README.fr.md) |
[日本語 (ja)](README.ja.md) |
[한국어 (ko)](README.ko.md) |
[Português (pt)](README.pt.md) |
Русский (ru) |
[简体中文 (zh-CN)](README.zh-CN.md) |
[繁體中文 (zh-TW)](README.zh-TW.md) |

> [!NOTE]
> Этот перевод был создан с помощью машинного перевода.
> Он может содержать ошибки или неестественные выражения.
> Вклад в улучшение перевода приветствуется!

## Обзор

Этот GitHub Action создает календарь вкладов GitHub на 3D-изображении профиля.

## Как использовать (GitHub Actions) - Базовый

Этот GitHub Action генерирует ваш 3D-календарь вкладов профиля GitHub и коммитит его в ваш репозиторий.
После добавления GitHub Action рабочий процесс запускается автоматически один раз в день.
Вы также можете запустить рабочий процесс вручную.

### Шаг 1. Создайте специальный репозиторий профиля

Создайте репозиторий на GitHub с таким же именем, как ваше имя пользователя.

- Например, если имя пользователя `octocat`, создайте репозиторий с именем `octocat/octocat`.
- См. также: [Управление файлом сведений о профиле](https://docs.github.com/ru/account-and-profile/how-tos/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme)

В этом репозитории выполните следующие шаги.

### Шаг 2. Создайте файл рабочего процесса

Создайте файл рабочего процесса, как показано ниже.

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
> Вы можете изменить настройки GitHub, чтобы включить вклады из приватных репозиториев. Для этого нажмите `Contribution settings` в правом верхнем углу стандартного календаря вкладов или нажмите на свой значок в правом верхнем углу экрана, выберите `Settings` ⇒ `Public profile` ⇒ `Contributions & Activity` и отметьте `Include private contributions on my profile`.
>
> Если вы хотите включить дополнительные активности из приватных репозиториев, зарегистрируйте персональный токен доступа как секрет и установите его в переменную окружения `GITHUB_TOKEN` в файле рабочего процесса. Однако в большинстве случаев достаточно значения по умолчанию `secrets.GITHUB_TOKEN`.

По умолчанию расписание установлено на запуск один раз в день.
Вы можете изменить время запуска по своему усмотрению.

Это добавит рабочий процесс в ваш репозиторий.

#### Переменные окружения

В примере указаны только `GITHUB_TOKEN` и `USERNAME` как переменные окружения, но вы можете указать следующие переменные:

- `GITHUB_TOKEN` : (обязательно) токен доступа
- `USERNAME` : (обязательно) целевое имя пользователя (или укажите аргументом).
- `MAX_REPOS` : (опционально) максимальное количество репозиториев, по умолчанию 100 - начиная с версии 0.2.0
- `SETTING_JSON` : (опционально) путь к файлу настроек json. См. `sample-settings/*.json` и `src/type.ts` в репозитории `yoshi389111/github-profile-3d-contrib` для подробностей. - начиная с версии 0.6.0
- `GITHUB_ENDPOINT` : (опционально) конечная точка Github GraphQL. Например, если вы хотите создать календарь вкладов на основе активности вашей корпоративной GitHub Enterprise, установите эту переменную окружения. Например: `https://github.mycompany.com/api/graphql` - начиная с версии 0.8.0
- `YEAR` : (опционально) Для календарей прошлых лет укажите год. Это предназначено для использования при запуске инструмента из командной строки. - начиная с версии 0.8.0

#### О `GITHUB_TOKEN`

`secrets.GITHUB_TOKEN`, установленный в переменной окружения `GITHUB_TOKEN` в примере, — это специальный токен доступа, автоматически создаваемый GitHub.

- Документация GitHub: [Использование GITHUB_TOKEN для проверки подлинности в рабочих процессах](https://docs.github.com/ru/actions/tutorials/authenticate-with-github_token)

Если вы хотите генерировать календарь вкладов только для публичных репозиториев, используйте это значение.
Нет необходимости создавать секрет вручную.

Также, если вы хотите включить активность в приватных репозиториях в календарь вкладов, отметьте "Include private contributions on my profile" в разделе "Profile settings" в настройках профиля.

Кроме того, если вы хотите включить дополнительную информацию об активности из приватных репозиториев, создайте токен доступа с соответствующими разрешениями.
Зарегистрируйте этот токен как секрет с любым именем (например, `MY_PERSONAL_ACCESS_TOKEN`).
Однако обратите внимание, что секреты, созданные пользователем, не могут начинаться с `GITHUB_`.

- Документация GitHub: [Секреты](https://docs.github.com/ru/actions/concepts/security/secrets)

Установите этот секрет в переменную окружения `GITHUB_TOKEN`.

```diff
          env:
-           GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
+           GITHUB_TOKEN: ${{ secrets.MY_PERSONAL_ACCESS_TOKEN }}
            USERNAME: ${{ github.repository_owner }}
```

#### О времени запуска

В примере запуск установлен на 18:00 UTC.
Это потому, что он будет запускаться в полночь JST, что соответствует местному времени автора.

```yaml
on:
  schedule: # 03:00 JST == 18:00 UTC
    - cron: "0 18 * * *"
```

Вы можете изменить время на любое удобное.
Рекомендуется выбирать полночь (около 3 часов ночи) по вашему местному времени.
Однако обратите внимание, что время должно быть указано в UTC.

### Шаг 3. Запустите GitHub Action вручную

В первый раз запустите этот рабочий процесс вручную.

- `Actions` -> `GitHub-Profile-3D-Contrib` -> `Run workflow`

Изображения профиля генерируются по следующим путям:

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

Если вы укажете переменную окружения `SETTING_JSON` без свойства `fileName` в json-файле, будет сгенерировано следующее изображение:

- `profile-3d-contrib/profile-customize.svg`

Вы можете использовать эти изображения в вашем README.md, как показано ниже.

Пример: зеленая версия

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-green-animate.svg)

Пример: сезонная версия (Северное полушарие.)

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-season-animate.svg)

Пример: сезонная версия (Южное полушарие.)

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-south-season-animate.svg)

Пример: ночная версия

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-view.svg)

Пример: ночная зеленая версия

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-green.svg)

Пример: ночная радужная версия

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-rainbow.svg)

Пример: версия git block

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

### Шаг 4. Добавьте изображение в README.md

Добавьте путь к сгенерированному изображению в ваш README файл.

Пример:

```md
![](./profile-3d-contrib/profile-green-animate.svg)
```

## Как использовать (GitHub Actions) - Продвинутые примеры

- [Подробнее в EXAMPLES.md](../EXAMPLES.md)

## Как использовать (локально)

Установите переменную окружения `GITHUB_TOKEN` на ваш персональный токен доступа.

```sh
export GITHUB_TOKEN=XXXXXXXXXXXXXXXXXXXXX
```

Выполните следующую команду, заменив `USER_NAME` на ваше имя пользователя GitHub или целевое имя пользователя.

```sh
node_modules/.bin/ts-node src/index.ts USER_NAME
```

или

```sh
npm run build
node . USER_NAME
```

## Лицензия

&copy; 2021 SATO Yoshiyuki. Лицензия MIT.
