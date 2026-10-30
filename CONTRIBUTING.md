# Contribuir al projecte

Gràcies per revisar aquest repositori. Aquestes són les convencions acordades per
l'equip (Emilia, Didac, Aleix) i formen part del model de qualitat del projecte: cap
canvi arriba a `main` sense traçabilitat i revisió creuada.

## 1. Flux de treball

1. Crea una branca des de `main`:

   ```bash
   git switch -c feat/12-descripcio-curta
   ```

   Prefixos permesos: `feat/`, `fix/`, `docs/`, `chore/`, `ci/`.

2. Fes commits petits i atòmics seguint **Conventional Commits**:

   ```text
   feat(mòdul): afegeix registre d'accessos a documents
   fix(compose): corregeix la codificació de MariaDB
   docs(05): corregeix el total d'hores a 80
   ```

   Tipus permesos: `feat`, `fix`, `docs`, `chore`, `ci`, `refactor`, `test`.

3. Obre una *Pull Request* contra `main` i demana revisió a un altre membre.
4. La CI ha de passar en verd (`yamllint`, `phpcs`, `docker compose config`).
5. Fes *squash merge* i esborra la branca.

## 2. Regles de revisió creuada

* Qui escriu el canvi **no** l'aprova.
* El revisor comprova: correcció tècnica, ortografia, coherència amb la documentació i
  que no s'hagi afegit cap secret.
* Els canvis de configuració de Drupal han d'incloure l'exportació YAML corresponent.

## 3. Secrets i dades sensibles

* **Mai** es versiona el fitxer `.env` ni cap credencial real.
* Els exemples de `.env.example` han de contenir valors ficticis evidents.
* Els documents d'evidència no poden incloure contrasenyes, IP públiques ni dades de
  clients reals.

## 4. Estil

* Català amb accents, `ç` i `·` (no s'accepta text sense diacrítics).
* Markdown amb títols jeràrquics (`#`, `##`, `###`), una sola H1 per fitxer.
* Taules alineades amb `:---` i unitats sempre amb espai: `80,0 h`, `16 dies`.
* Codi PHP seguint l'estàndard Drupal (`phpcs --standard=Drupal`).

## 5. Lliuraments

Cada versió es tanca amb una etiqueta semàntica i una entrada al `CHANGELOG.md`:

```bash
git tag -a v1.0.0 -m "Lliurament AP03"
git push origin v1.0.0
```
