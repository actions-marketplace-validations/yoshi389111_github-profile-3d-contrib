# GitHub Profile 3D Contrib

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

<!-- Orden del código de idioma (excepto inglés) -->
[English (en)](../README.md) |
[Deutsch (de)](README.de.md) |
[Español (es)](README.es.md) |
Français (fr) |
[日本語 (ja)](README.ja.md) |
[한국어 (ko)](README.ko.md) |
[Português (pt)](README.pt.md) |
[Русский (ru)](README.ru.md) |
[简体中文 (zh-CN)](README.zh-CN.md) |
[繁體中文 (zh-TW)](README.zh-TW.md) |

> [!NOTE]
> Cette traduction a été générée par traduction automatique.
> Elle peut contenir des erreurs ou des expressions non naturelles.
> Les contributions pour améliorer la traduction sont les bienvenues !

## Vue d'ensemble

Cette action GitHub crée un calendrier de contributions GitHub sur une image de profil 3D.

## Comment utiliser (GitHub Actions) - Basique

Cette action GitHub génère votre calendrier de contributions 3D et le commite dans votre dépôt.
Après avoir ajouté l'action, le workflow s'exécute automatiquement une fois par jour.
Vous pouvez également déclencher le workflow manuellement.

### Étape 1. Créer un dépôt de profil spécial

Créez un dépôt sur GitHub portant le même nom que votre nom d'utilisateur.

- Par exemple, si le nom d'utilisateur est `octocat`, créez un dépôt nommé `octocat/octocat`.
- Voir aussi : [Gestion de votre README de profil](https://docs.github.com/fr/account-and-profile/how-tos/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme)

Dans ce dépôt, suivez les étapes ci-dessous.

### Étape 2. Créer le fichier de workflow

Créez un fichier de workflow comme ci-dessous.

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
> Vous pouvez modifier vos paramètres GitHub pour inclure les contributions des dépôts privés. Pour changer ce paramètre, cliquez sur `Paramètres de contribution` en haut à droite du calendrier de contribution standard, ou cliquez sur votre icône en haut à droite de l'écran, sélectionnez `Paramètres` ⇒ `Profil public` ⇒ `Contributions & Activité`, et cochez `Inclure les contributions privées sur mon profil`.
>
> Si vous souhaitez inclure des activités supplémentaires provenant de dépôts privés, enregistrez un jeton d'accès personnel comme secret et définissez-le dans la variable d'environnement `GITHUB_TOKEN` du fichier de workflow. Cependant, dans la plupart des cas, le `secrets.GITHUB_TOKEN` par défaut suffit.

La planification est définie pour s'exécuter une fois par jour par défaut.
Vous pouvez modifier l'heure programmée comme vous le souhaitez.

Cela ajoutera le workflow à votre dépôt.

#### Variables d'environnement

Dans l'exemple, seules `GITHUB_TOKEN` et `USERNAME` sont spécifiées comme variables d'environnement, mais vous pouvez spécifier les variables suivantes :

- `GITHUB_TOKEN` : (obligatoire) jeton d'accès
- `USERNAME` : (obligatoire) nom d'utilisateur cible (ou à spécifier en argument).
- `MAX_REPOS` : (optionnel) nombre max de dépôts, par défaut 100 - depuis la version 0.2.0
- `SETTING_JSON` : (optionnel) chemin du fichier json de configuration. Voir `sample-settings/*.json` et `src/type.ts` dans le dépôt `yoshi389111/github-profile-3d-contrib` pour plus de détails. - depuis la version 0.6.0
- `GITHUB_ENDPOINT` : (optionnel) endpoint GraphQL de Github. Par exemple, pour créer un calendrier de contributions basé sur l'activité GitHub Enterprise de votre entreprise au lieu de GitHub.com, définissez cette variable d'environnement. ex. `https://github.mycompany.com/api/graphql` - depuis la version 0.8.0
- `YEAR` : (optionnel) Pour les calendriers passés, spécifiez l'année. Ceci est destiné à être spécifié lors de l'exécution de l'outil en ligne de commande. - depuis la version 0.8.0

#### À propos de `GITHUB_TOKEN`

Le `secrets.GITHUB_TOKEN` défini dans la variable d'environnement `GITHUB_TOKEN` dans l'exemple est un jeton d'accès spécial créé automatiquement par GitHub.

- Docs GitHub : [Utiliser GITHUB_TOKEN pour l’authentification dans les flux de travail](https://docs.github.com/fr/actions/tutorials/authenticate-with-github_token)

Si vous souhaitez générer un calendrier de contributions uniquement pour les dépôts publics, utilisez cette valeur.
Il n'est pas nécessaire de créer un secret manuellement.

De plus, si vous souhaitez inclure l'activité de vos dépôts privés dans votre calendrier de contributions, cochez "Inclure les contributions privées sur mon profil" dans la section "Paramètres de profil" de "Profil public" dans vos paramètres de profil.

En outre, si vous souhaitez inclure des informations d'activité supplémentaires provenant de dépôts privés, créez un jeton d'accès avec les autorisations appropriées.
Enregistrez ce jeton comme secret avec le nom de votre choix (par exemple, `MY_PERSONAL_ACCESS_TOKEN`).
Cependant, notez que les secrets créés par l'utilisateur ne peuvent pas commencer par `GITHUB_`.

- Docs GitHub : [Secrets](https://docs.github.com/fr/actions/concepts/security/secrets)

Définissez ce secret comme valeur de la variable d'environnement `GITHUB_TOKEN`.

```diff
          env:
-           GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
+           GITHUB_TOKEN: ${{ secrets.MY_PERSONAL_ACCESS_TOKEN }}
            USERNAME: ${{ github.repository_owner }}
```

#### À propos de l'heure de planification

Dans l'exemple, il est défini pour démarrer à 18:00 UTC.
Ceci afin qu'il s'exécute à minuit JST, qui est l'heure locale de l'auteur.

```yaml
on:
  schedule: # 03:00 JST == 18:00 UTC
    - cron: "0 18 * * *"
```

Vous pouvez le modifier à l'heure que vous souhaitez.
Nous recommandons minuit (vers 3h du matin) heure locale.
Cependant, notez que l'heure doit être spécifiée en UTC.

### Étape 3. Exécuter manuellement cette action GitHub

La première fois, exécutez ce workflow manuellement.

- `Actions` -> `GitHub-Profile-3D-Contrib` -> `Run workflow`

Les images de profil sont générées aux chemins suivants :

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

Si vous spécifiez la variable d'environnement `SETTING_JSON` sans propriété `fileName` dans le fichier json, l'image suivante sera générée :

- `profile-3d-contrib/profile-customize.svg`

Vous pouvez utiliser ces images dans votre README.md comme ci-dessous.

Exemple : version verte

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-green-animate.svg)

Exemple : version saison (hémisphère nord.)

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-season-animate.svg)

Exemple : version saison (hémisphère sud.)

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-south-season-animate.svg)

Exemple : version vue nocturne

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-view.svg)

Exemple : version verte nocturne

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-green.svg)

Exemple : version arc-en-ciel nocturne

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-rainbow.svg)

Exemple : version git block

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

### Étape 4. Ajouter l'image au README.md

Ajoutez le chemin de l'image générée dans votre fichier README.

Exemple :

```md
![](./profile-3d-contrib/profile-green-animate.svg)
```

## Comment utiliser (GitHub Actions) - Exemples avancés

- [Plus d'infos dans EXAMPLES.md](./EXAMPLES.md)

## Comment utiliser (local)

Définissez la variable d'environnement `GITHUB_TOKEN` sur votre jeton d'accès personnel.

```sh
export GITHUB_TOKEN=XXXXXXXXXXXXXXXXXXXXX
```

Exécutez la commande suivante en remplaçant `USER_NAME` par votre nom d'utilisateur GitHub ou le nom d'utilisateur cible.

```sh
node_modules/.bin/ts-node src/index.ts USER_NAME
```

ou

```sh
npm run build
node . USER_NAME
```

## Licence

&copy; 2021 SATO Yoshiyuki. Sous licence MIT.
