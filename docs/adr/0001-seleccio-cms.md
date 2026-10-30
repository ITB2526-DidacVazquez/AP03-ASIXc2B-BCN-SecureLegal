# ADR 0001 — Selecció del CMS: Drupal 10

* **Estat:** acceptada
* **Data:** 2026-10-21
* **Decisors:** Emilia (PM/BA), Didac (SysAdmin/DevOps), Aleix (Dev/QA)
* **Consultats:** professorat del mòdul M0376

## Context

BCN SecureLegal S.L. necessita un portal corporatiu, una intranet i una àrea privada de
lliurament de documents, amb desplegament **100 % On-Premise** per exigències del
secret professional, el RGPD i la Directiva NIS2. Els documents d'auditoria són
l'actiu més sensible i no poden quedar accessibles per URL directa.

Restriccions:

* Llicència lliure, sense subscripcions obligatòries ni telemetria a tercers.
* Permisos granulars per rol i per camp.
* Sistema de fitxers privats fora de l'arrel web.
* Desplegable en contenidors, amb MariaDB.
* Capacitat multillengua (català, castellà, anglès).

## Opcions considerades

1. **WordPress** — corba d'aprenentatge mínima, ecosistema immens, però rols bàsics i
   necessitat de múltiples connectors de tercers per aïllar fitxers privats.
2. **Drupal 10** — permisos per rol i camp al nucli, *private file system* natiu,
   Views, multillengua, equip de seguretat dedicat.
3. **Joomla** — ACL flexible de sèrie, però comunitat i ecosistema més reduïts.

## Decisió

S'adopta **Drupal 10.6.19** amb Apache 2.4.69 i PHP 8.3 sobre la imatge oficial
`drupal:10.6.19-apache`, amb MariaDB 10.11.19 en un contenidor separat.

## Justificació

* Cobreix les quatre necessitats funcionals **sense connectors de tercers**, cosa que
  redueix la superfície d'atac (requisit central del projecte).
* El sistema de fitxers privats és natiu i validat per sessió: elimina la descàrrega
  directa per URL.
* La configuració és exportable a YAML, la qual cosa permet versionar-la a Git i
  auditar-ne els canvis (vegeu el document 00).
* Llicència GPLv2+ compatible amb el desplegament On-Premise i amb el model de negoci
  del client.

## Conseqüències

**Positives**

* Menys dependències externes i menys risc de vulnerabilitats per connectors.
* Traçabilitat de canvis mitjançant configuració com a codi.
* Sobirania tecnològica completa.

**Negatives / costos assumits**

* Corba d'aprenentatge més alta per a l'equip.
* Requereix administració per consola (Composer, Drush) que la imatge oficial no
  inclou: caldrà una imatge pròpia per a producció.
* La interfície d'edició és menys intuïtiva per a usuaris no tècnics que la de
  WordPress; caldrà formació.

## Alternatives descartades

* **WordPress:** el cost de seguretat d'encadenar plugins per a permisos i fitxers
  privats es considera inacceptable per a un despatx jurídic.
* **Joomla:** ecosistema i documentació més limitats; menys flexibilitat en tipus de
  contingut dinàmics.

## Referències

* [Document 02 — Comparativa de CMS](../02-seleccio-i-comparativa-cms.md)
* [ADR 0002 — Desplegament On-Premise amb Docker Compose](0002-desplegament-on-premise.md)
