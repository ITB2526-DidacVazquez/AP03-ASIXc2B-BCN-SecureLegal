# Changelog

Tots els canvis rellevants d'aquest projecte es documenten en aquest fitxer.
El format segueix [Keep a Changelog](https://keepachangelog.com/ca/1.1.0/) i el
versionat semàntic [SemVer](https://semver.org/lang/ca/).

## [1.0.3] — 2026-10-30

### Afegit

* Diapositiva 5 «Cultura Oberta i Traçabilitat»: tres blocs (codi auditable,
  traçabilitat de cada canvi i compliment normatiu) i la cadena Kanban → branca → PR
  → CI → tag → desplegament.
* Guió de presentació reescrit per seguir l'ordre real de les 7 diapositives.

### Canviat

* Renumerada la diapositiva de conclusions (5 → 6).
* El fitxer lliurat passa a dir-se `Presentacio-Projecte-7-diapositives.pptx`.

## [1.0.2] — 2026-10-30

### Afegit

* La CI valida la sintaxi PHP (`php -l`) abans d'executar `phpcs`.

## [1.0.1] — 2026-10-30

### Afegit

* Guió de la presentació amb locució per diapositiva i preguntes previsibles.
* Els fitxers lliurats (pràctica, memòria i presentació) s'incorporen al repositori
  a la carpeta `lliurament/`.

### Corregit

* `.docx` i `.pptx`: «directorist» → «directoris», nota interna «s'ha de verificar»
  eliminada, «districte 22@» duplicat, «amb Github», «hook hook_page_top» redundant i
  «Cruzado» → «Creuat».
* Xifres de la presentació i de la memòria: 80 h i 26,7 / 26,8 / 26,5 h.

## [1.0.0] — 2026-10-28

### Afegit

* Document 00: cultura oberta, programari lliure i cadena de traçabilitat
  (Kanban → branca → PR → CI → desplegament).
* Matriu de rols i permisos del CMS (document 02, §2.3.1).
* Registres de decisió arquitectònica: `adr/0001` (selecció de CMS) i `adr/0002`
  (desplegament On-Premise).
* Document 06: model d'amenaces, backups, RTO/RPO i punts de control normatiu.
* Mòdul `bsl_client_docs` documentat com a versió de demostració, amb l'estructura
  completa prevista per a producció.
* `LICENSE` (GPL-2.0+), `CONTRIBUTING.md`, `.gitattributes`, `.editorconfig`,
  plantilla de *Pull Request* i pipeline de CI (GitHub Actions).

### Corregit

* **Càlcul de dedicació:** el total era de 72,0 h i en realitat suma 80,0 h. S'han
  recalculat les columnes de suport i revisió i la càrrega ponderada
  (26,7 / 26,8 / 26,5 h; desviació màxima 0,35 h).
* **Infraestructura:** la carpeta privada `/var/www/private` no estava muntada al
  `docker-compose.yml`; s'ha afegit el volum `drupal_private`.
* **Codificació:** s'imposa `utf8mb4` / `utf8mb4_unicode_ci` a MariaDB (abans només
  era una afirmació de la documentació).
* **Xarxes:** es defineixen les xarxes `backend` (`internal: true`) i `edge`; abans
  s'usava el bridge per defecte, que té sortida a Internet.
* **Ports:** el servei web s'exposa només a `127.0.0.1:8080` en lloc de `0.0.0.0`.
* **Credencials:** s'eliminen les contrasenyes per defecte del compose; ara són
  obligatòries i el desplegament falla si falten.
* **Imatges:** es fixen les versions (`drupal:10.6.19-apache`, `mariadb:10.11.19`)
  per garantir la reproducibilitat.
* **Drush:** s'elimina la comanda `vendor/bin/drush` del README, que fallava perquè la
  imatge oficial no inclou `vendor/`.
* **Permisos:** `settings.php` passa a `root:root` amb `444` (abans `www-data`, que pot
  revertir el `chmod`).
* **Referències creuades:** la prova 7 del banc de proves s'aclareix respecte als
  apartats 3.3 i 3.4 de l'enunciat original.
* **Documentació duplicada:** es fusionen `04-memoria-tecnica` i
  `05-memoria-tecnica-i-planificacio` en un únic document 05.
* **Ortografia i estil:** accents, `ç` i `·` a tots els documents; correcció de
  «te prohibit», «mes», «basic», «de serie», «directorist», «Cruzado» i del doble espai
  a l'anàlisi de logs.
* **Estat de Git:** es recupera el document complet del lliurament que havia quedat
  sobreescrit (`docs/07`) i s'incorporen al repositori els documents que no estaven
  versionats.

### Eliminat

* Directiva `version: '3.8'` del `docker-compose.yml` (obsoleta a Compose v2).
* Contrasenyes reals de `.env.example`.
* Notes de treball internes dins del text del lliurament.
