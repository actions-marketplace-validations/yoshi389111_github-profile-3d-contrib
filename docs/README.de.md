# GitHub Profile 3D Contrib

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

<!-- Sprachreihenfolge (außer Englisch) -->
[English (en)](../README.md) |
Deutsch (de) |
[Español (es)](README.es.md) |
[Français (fr)](README.fr.md) |
[日本語 (ja)](README.ja.md) |
[한국어 (ko)](README.ko.md) |
[Português (pt)](README.pt.md) |
[Русский (ru)](README.ru.md) |
[简体中文 (zh-CN)](README.zh-CN.md) |
[繁體中文 (zh-TW)](README.zh-TW.md) |

> [!NOTE]
> Diese Übersetzung wurde maschinell erstellt.
> Sie kann Fehler oder unnatürliche Ausdrücke enthalten.
> Beiträge zur Verbesserung der Übersetzung sind willkommen!

## Übersicht

Diese GitHub Action erstellt einen GitHub-Beitragskalender auf einem 3D-Profilbild.

## Verwendung (GitHub Actions) – Basis

Diese GitHub Action generiert Ihren 3D-Beitragskalender und committet ihn in Ihr Repository.
Nach dem Hinzufügen der Action wird der Workflow automatisch einmal täglich ausgeführt.
Sie können den Workflow auch manuell auslösen.

### Schritt 1. Spezielles Profil-Repository erstellen

Erstellen Sie ein Repository auf GitHub mit demselben Namen wie Ihr Benutzername.

- Beispiel: Wenn der Benutzername `octocat` ist, erstellen Sie ein Repository namens `octocat/octocat`.
- Siehe auch: [Verwalten der Profil-README](https://docs.github.com/de/account-and-profile/how-tos/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme)

Führen Sie in diesem Repository die folgenden Schritte aus.

### Schritt 2. Workflow-Datei erstellen

Erstellen Sie eine Workflow-Datei wie unten gezeigt.

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
> Sie können Ihre GitHub-Einstellungen ändern, um Beiträge aus privaten Repositories einzubeziehen. Um diese Einstellung zu ändern, klicken Sie auf `Beitragseinstellungen` oben rechts im Standard-Beitragskalender oder auf Ihr Symbol oben rechts auf dem Bildschirm, wählen Sie `Einstellungen` ⇒ `Öffentliches Profil` ⇒ `Beiträge & Aktivität` und aktivieren Sie `Private Beiträge in meinem Profil anzeigen`.
>
> Wenn Sie zusätzliche Aktivitäten aus privaten Repositories einbeziehen möchten, registrieren Sie ein persönliches Zugriffstoken als Secret und setzen Sie es in der Workflow-Datei auf die Umgebungsvariable `GITHUB_TOKEN`. In den meisten Fällen reicht jedoch das Standard-`secrets.GITHUB_TOKEN` aus.

Der Zeitplan ist standardmäßig auf einmal täglich eingestellt.
Sie können die geplante Zeit beliebig ändern.

Dadurch wird der Workflow zu Ihrem Repository hinzugefügt.

#### Umgebungsvariablen

Im Beispiel werden nur `GITHUB_TOKEN` und `USERNAME` als Umgebungsvariablen angegeben, aber Sie können die folgenden Variablen angeben:

- `GITHUB_TOKEN` : (erforderlich) Zugriffstoken
- `USERNAME` : (erforderlich) Ziel-Benutzername (oder als Argument angeben).
- `MAX_REPOS` : (optional) maximale Anzahl von Repositories, Standard 100 – seit Version 0.2.0
- `SETTING_JSON` : (optional) Pfad zur Einstellungs-JSON-Datei. Siehe `sample-settings/*.json` und `src/type.ts` im Repository `yoshi389111/github-profile-3d-contrib` für Details. – seit Version 0.6.0
- `GITHUB_ENDPOINT` : (optional) Github GraphQL-Endpunkt. Wenn Sie z. B. einen Beitragskalender basierend auf der GitHub Enterprise-Aktivität Ihres Unternehmens statt GitHub.com erstellen möchten, setzen Sie diese Umgebungsvariable. z. B. `https://github.mycompany.com/api/graphql` – seit Version 0.8.0
- `YEAR` : (optional) Für vergangene Kalender geben Sie das Jahr an. Dies ist für die Ausführung des Tools über die Kommandozeile gedacht. – seit Version 0.8.0

#### Über `GITHUB_TOKEN`

Das im Beispiel in der Umgebungsvariable `GITHUB_TOKEN` gesetzte `secrets.GITHUB_TOKEN` ist ein spezielles Zugriffstoken, das von GitHub automatisch erstellt wird.

- GitHub-Dokumentation: [Verwenden von GITHUB_TOKEN für die Authentifizierung in Workflows](https://docs.github.com/de/actions/tutorials/authenticate-with-github_token)

Wenn Sie einen Beitragskalender nur für öffentliche Repositories erstellen möchten, verwenden Sie diesen Wert.
Es ist nicht notwendig, ein Secret manuell zu erstellen.

Wenn Sie Aktivitäten aus Ihren privaten Repositories in Ihren Beitragskalender aufnehmen möchten, aktivieren Sie "Private Beiträge in meinem Profil anzeigen" im Abschnitt "Profileinstellungen" Ihres Profils.

Wenn Sie zusätzliche Aktivitätsinformationen aus privaten Repositories einbeziehen möchten, erstellen Sie ein Zugriffstoken mit den entsprechenden Berechtigungen.
Registrieren Sie dieses Token als Secret mit einem beliebigen Namen (z. B. `MY_PERSONAL_ACCESS_TOKEN`).
Beachten Sie jedoch, dass benutzerdefinierte Secrets nicht mit `GITHUB_` beginnen dürfen.

- GitHub-Dokumentation: [Geheimnisse](https://docs.github.com/de/actions/concepts/security/secrets)

Setzen Sie dieses Secret als Wert der Umgebungsvariable `GITHUB_TOKEN`.

```diff
          env:
-           GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
+           GITHUB_TOKEN: ${{ secrets.MY_PERSONAL_ACCESS_TOKEN }}
            USERNAME: ${{ github.repository_owner }}
```

#### Über die geplante Uhrzeit

Im Beispiel ist der Start auf 18:00 UTC eingestellt.
Dadurch wird der Workflow um Mitternacht JST ausgeführt, was der lokalen Zeit des Autors entspricht.

```yaml
on:
  schedule: # 03:00 JST == 18:00 UTC
    - cron: "0 18 * * *"
```

Sie können die Zeit beliebig ändern.
Wir empfehlen Mitternacht (ca. 3 Uhr morgens) Ihrer lokalen Zeit.
Beachten Sie jedoch, dass die Zeit in UTC angegeben werden muss.

### Schritt 3. GitHub Action manuell ausführen

Führen Sie diesen Workflow beim ersten Mal manuell aus.

- `Actions` -> `GitHub-Profile-3D-Contrib` -> `Run workflow`

Die Profilbilder werden an den folgenden Pfaden generiert:

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

Wenn Sie die Umgebungsvariable `SETTING_JSON` ohne die Eigenschaft `fileName` in der JSON-Datei angeben, wird folgendes Bild generiert:

- `profile-3d-contrib/profile-customize.svg`

Sie können diese Bilder wie unten gezeigt in Ihrer README.md verwenden.

Beispiel: grüne Version

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-green-animate.svg)

Beispiel: Saison-Version (Nordhalbkugel.)

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-season-animate.svg)

Beispiel: Saison-Version (Südhalbkugel.)

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-south-season-animate.svg)

Beispiel: Nachtansicht-Version

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-view.svg)

Beispiel: Nacht-grün-Version

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-green.svg)

Beispiel: Nacht-Regenbogen-Version

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-night-rainbow.svg)

Beispiel: Git-Block-Version

![svg](https://raw.githubusercontent.com/yoshi389111/github-profile-3d-contrib/main/docs/demo/profile-gitblock.svg)

### Schritt 4. Bild zur README.md hinzufügen

Fügen Sie den Pfad zum generierten Bild in Ihre README-Datei ein.

Beispiel:

```md
![](./profile-3d-contrib/profile-green-animate.svg)
```

## Verwendung (GitHub Actions) – Erweiterte Beispiele

- [Weitere Informationen in EXAMPLES.md](./EXAMPLES.md)

## Verwendung (lokal)

Setzen Sie die Umgebungsvariable `GITHUB_TOKEN` auf Ihr persönliches Zugriffstoken.

```sh
export GITHUB_TOKEN=XXXXXXXXXXXXXXXXXXXXX
```

Führen Sie den folgenden Befehl aus und ersetzen Sie `USER_NAME` durch Ihren GitHub-Benutzernamen oder den Ziel-Benutzernamen.

```sh
node_modules/.bin/ts-node src/index.ts USER_NAME
```

oder

```sh
npm run build
node . USER_NAME
```

## Lizenz

&copy; 2021 SATO Yoshiyuki. Lizenziert unter der MIT-Lizenz.
