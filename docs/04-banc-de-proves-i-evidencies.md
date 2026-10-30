# Document 04: Banc de Proves i Evidències Reals

Aquest document recull les proves d'integració i funcionament executades sobre la
plataforma, amb el criteri d'acceptació i el resultat obtingut.

## 4.0 Resum d'execució

| # | Prova | Criteri d'acceptació | Resultat |
| :--- | :--- | :--- | :---: |
| 1 | Instal·lació de Drupal | Assistent completat i connexió amb MariaDB | **PASS** |
| 2 | Publicació de contingut | Article visible a `/node/1` | **PASS** |
| 3 | Gestió de continguts | Article llistat amb autor i data | **PASS** |
| 4 | Detecció del mòdul propi | Mòdul visible i activable a *Extend* | **PASS** |
| 5 | Edició en calent | Canvi visible sense reinici del contenidor | **PASS** |
| 6 | Logs de PHP | Avís diagnosticat i sense impacte funcional | **PASS** (amb observació) |
| 7 | Abast de la infraestructura | HA i NFS fora de l'abast acordat | **N/A** (descartat) |

---

## Prova 1: Assistent d'instal·lació de Drupal 10.6.19

* **URL d'accés:** `http://localhost:8080/core/install.php`
* **Idioma seleccionat:** català.
* **Perfil:** estàndard.
* **Resultat:** connexió correcta amb el servei MariaDB (`bsl-db`) gràcies al
  healthcheck. S'arriba al pas 8 de finalització i actualització de les traduccions
  sense incidències.
* **Versió verificada del gestor:** **Drupal 10.6.19**.
* **Evidència:** figura 5 de la memòria tècnica.

## Prova 2: Accés al CMS i publicació de contingut

* **Credencials d'administrador:** `sysadmin_bsl`.
* **Contingut de prova creat:** article del Business Case titulat «Nova directiva NIS2:
  requisits de ciberseguretat per a pimes i entitats locals».
* **Ruta de publicació:** `http://localhost:8080/node/1`.
* **Estat:** publicat i accessible.

## Prova 3: Panell de gestió de continguts

* **Ruta d'administració:** `http://localhost:8080/admin/content`.
* **Resultat:** l'article apareix llistat amb títol, tipus de contingut «Article»,
  autor `sysadmin_bsl` i data d'actualització 28/10/2026.

## Prova 4: Reconeixement del mòdul propi a *Extend*

* **Ruta:** `http://localhost:8080/admin/modules`.
* **Paquet:** BCN SecureLegal.
* **Mòdul:** «Traçabilitat d'accés a documents» (`bsl_client_docs`).
* **Resultat:** Drupal detecta automàticament el mòdul gràcies al bind mount de
  `./modules/custom/bsl_client_docs`. En activar-lo, mostra el missatge d'estat
  inicial: `bsl_client_docs: versió 1`.

## Prova 5: Cicle d'edició en calent sense reinici

Aquesta prova valida que les modificacions del codi font al host es reflecteixen
immediatament a l'aplicació sense reiniciar contenidors.

1. Estat inicial d'arrencada del contenidor:

   ```bash
   docker inspect -f '{{.State.StartedAt}}' bsl-drupal
   # Sortida: 2026-10-21T18:21:10.417492923Z
   ```

2. Modificació del fitxer al host mitjançant `sed`:

   ```bash
   sed -i 's/versio 1/versio 2/' modules/custom/bsl_client_docs/bsl_client_docs.module
   ```

3. Comprovació de no reinici del contenidor:

   ```bash
   docker inspect -f '{{.State.StartedAt}}' bsl-drupal
   # Sortida: 2026-10-21T18:21:10.417492923Z (exactament la mateixa marca temporal)
   ```

4. Verificació al navegador: en recarregar la pàgina (`http://localhost:8080`), el
   missatge superior de Drupal s'actualitza de manera immediata mostrant
   `bsl_client_docs: versió 2`.

> **Per què funciona:** amb un bind mount, PHP llegeix el fitxer del sistema de fitxers
> compartit a cada petició. No cal recompilar ni reiniciar cap servei. Si el canvi
> afecta el registre de *hooks*, cal regenerar la memòria cau de Drupal.

## Prova 6: Diagnosi d'avisos als logs de PHP

* **Comanda executada:** `docker compose logs drupal --tail=20`.
* **Missatge detectat:** `Deprecated: PHP Request Shutdown: assert.active INI setting is
  deprecated in Unknown on line 0`.
* **Diagnosi tècnica:** des de PHP 8.3, la directiva `assert.active` ha quedat
  obsoleta en favor de `zend.assertions`. La imatge oficial `drupal:10.6.19-apache`
  empra PHP 8.3 sobre Debian i genera aquest avís de nivell `E_DEPRECATED`.
* **Impacte:** no afecta el funcionament de Drupal ni atura les transaccions de base de
  dades.
* **Solució per a producció:** definir `error_reporting = E_ALL & ~E_DEPRECATED` al
  fitxer `php.ini` o bé establir la configuració `system.logging:error_level: hide` a
  `settings.php`.

## Prova 7: Abast de la infraestructura (apartats 3.3 i 3.4 de l'enunciat)

> **Aclariment de referències:** aquesta prova fa referència als apartats **3.3 (alta
> disponibilitat distribuïda)** i **3.4 (NFS multi-servidor)** de l'*enunciat original*
> de la pràctica, que no coincideixen amb la numeració d'aquest repositori (on el 3.3
> és permisos i el 3.4 és verificació).

D'acord amb les indicacions confirmades pel professorat, els apartats 3.3 (alta
disponibilitat distribuïda) i 3.4 (NFS multi-servidor) van quedar descartats de la
pràctica. Es valida com a solució definitiva l'arquitectura conteneritzada autònoma
On-Premise amb Docker Compose, amb les conseqüències següents:

| Escenari | Estat | Justificació |
| :--- | :--- | :--- |
| Alta disponibilitat (clúster) | Fora d'abast | Cost desproporcionat per a 25 usuaris; l'RTO es cobreix amb backup i reinici del servei |
| Emmagatzematge NFS compartit | Fora d'abast | Els volums amb nom cobreixen la persistència en un únic node |
| Recuperació davant de fallada | Cobert | Backup diari + snapshots de volum (vegeu el document 06) |
