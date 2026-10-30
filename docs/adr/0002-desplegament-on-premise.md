# ADR 0002 — Desplegament On-Premise amb Docker Compose

* **Estat:** acceptada
* **Data:** 2026-10-22
* **Decisors:** Didac (SysAdmin/DevOps), Aleix (Dev/QA), Emilia (PM)
* **Relacionada:** [ADR 0001](0001-seleccio-cms.md)

## Context

El client exigeix que les dades no surtin del seu CPD. A més, l'equip necessita un
entorn de desenvolupament reproduïble, amb persistència i amb capacitat de demostrar
l'edició en calent del codi.

## Opcions considerades

1. **Instal·lació nativa** (Apache + PHP + MariaDB al host): simple, però no
   reproduïble ni aïllada; difícil de traslladar a producció.
2. **Màquines virtuals per servei:** aïllament bo, consum i gestió excessius per a
   l'abast del projecte.
3. **Docker Compose amb dos serveis i volums amb nom:** reproduïble, versionat al
   repositori i fàcil de reproduir al CPD del client.

## Decisió

S'adopta **Docker Compose** amb:

* `db` (MariaDB 10.11.19) a una xarxa `backend` marcada com a `internal: true`.
* `drupal` (Drupal 10.6.19) connectat a `backend` i a `edge`, amb el port publicat
  només a `127.0.0.1:8080`.
* Volums amb nom `db_data`, `drupal_sites` i `drupal_private`.
* `depends_on` amb `condition: service_healthy` i *healthcheck* sobre MariaDB.
* Bind mount del mòdul propi per a l'edició en calent.

## Justificació

* **Aïllament real:** la base de dades no té sortida a Internet ni al host; el port
  3306 no es publica.
* **Reproduïbilitat:** imatges fixades a versió i configuració al repositori.
* **Evidència didàctica:** el bind mount permet demostrar l'edició en calent amb
  `docker inspect` (StartedAt inalterat).
* **Cost:** un sol host cobreix els 25 usuaris previstos.

## Conseqüències

**Positives**

* Desplegable amb tres comandes i sense instal·lar res al host.
* Fàcil de replicar al CPD del client.
* Permet demostrar persistència i aïllament de xarxa.

**Negatives / costos assumits**

* Sense alta disponibilitat: una fallada del host atura el servei (RTO 4 h amb backup).
* Cal mantenir actualitzades les imatges i vigilar les CVE de les dependències.
* El bind mount no s'ha d'usar en producció: cal una imatge amb el codi incorporat.

## Referències

* [Document 03 — Guia de desplegament](../03-guia-desplegament-docker.md)
* [Document 06 — Seguretat i riscos](../06-seguretat-compliment-i-riscos.md)
