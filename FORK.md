# Mon fork de slk : à lire avant de reprendre

Ce fichier n'existe que sur la branche `local` de `yjean/slk`. Il n'est pas destiné à upstream : ne l'inclus jamais dans une PR.

## Dépôts et branches

| Branche | Rôle |
|---|---|
| `main` | Miroir de `upstream/main` (gammons/slk). Aucun commit perso. |
| `local` | **Branche installée sur la machine** : `upstream/main` + mes correctifs pas encore mergés, + ce fichier. |
| `fix/…`, `feat/…` | Une branche par PR, créée depuis `upstream/main`, poussée sur `origin`, puis PR vers upstream. |

Remotes : `origin` = `yjean/slk` (mon fork), `upstream` = `gammons/slk`.

## Mes contributions

| PR | Branche | État (au 22/11/2026) | Dans `local` |
|---|---|---|---|
| [#476](https://github.com/gammons/slk/pull/476) fix(ui): attach a pasted file path the terminal quoted or escaped | `fix/paste-quoted-file-paths` | Review de gammons le 16/11 : 2 bugs à corriger + conventions de test. Corrigé le 22/11 (`cf0a6b27`), en attente d'une nouvelle review. | oui |

#476 fait qu'un fichier collé ou glissé dans le composer est joint même quand le terminal a « protégé » son chemin : `\ ` (Ghostty, iTerm2), `'…'`, `"…"` ou `file://`. Le chemin tel quel est toujours essayé en premier.

## Installation locale

Go est fixé par `mise.local.toml`, exclu via `.git/info/exclude` (le dépôt n'en a pas) : Go 1.26 et golangci-lint 2.14.0, la version de la CI.

```sh
V=$(git describe --tags --always)-local
go build -trimpath -ldflags="-s -w -X main.version=$V -X main.commit=$(git rev-parse --short HEAD) -X main.date=$(date -u +%Y-%m-%dT%H:%M:%SZ)" \
  -o ~/.local/bin/slk ./cmd/slk
slk --version   # vX.Y.Z-N-gSHA-local
```

Le binaire n'est pas éditable comme sqlit : après tout changement de branche ou rebase, il faut recompiler.

## Garder `local` à jour

```sh
git fetch upstream
git switch local
git rebase upstream/main      # une fois une PR mergée upstream, son commit disparaît ici de lui-même
# puis recompiler (ci-dessus)
```

Pour ajouter un correctif : le faire sur sa propre branche depuis `upstream/main`, ouvrir la PR, puis `git merge <branche>` dans `local`.

## Avant d'ouvrir ou de mettre à jour une PR

Ce que demande `AGENTS.md`, et ce que lance la CI :

```sh
go build ./... && go vet ./... && go test ./... -race && gofmt -l . && golangci-lint run
```

Leurs conventions : tests en `package ui` construits avec `newTestApp(t, with…)`, et tout nouveau helper ajouté au tableau du code partagé de `AGENTS.md`.

## Connexion à Slack

slk lit la session de l'app Slack desktop (`slk --add-workspace`). Sur cette machine, Slack est une web app : il faut donc utiliser `slk --add-workspace --browser` (DevTools → Network → « Copy as cURL » d'une requête `/api/` sur app.slack.com).
