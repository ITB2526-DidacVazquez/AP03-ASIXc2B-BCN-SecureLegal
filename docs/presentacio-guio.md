# Guió de la presentació executiva (15 minuts)

> Aquest guió correspon a la versió recomanada de **7 diapositives**. El fitxer
> `Presentacio_Projecte_BCN_SecureLegal.pptx` conté 6: la diapositiva 5 d'aquest guió
> (cultura oberta i traçabilitat) s'ha d'afegir manualment o exposar oralment sobre la
> diapositiva de conclusions.

**Repartiment:** 5 minuts per persona · Emilia (negoci i gestió) · Didac
(infraestructura) · Aleix (CMS, mòdul i QA).

---

## D1 — Portada (30 s) · Emilia

**Text a la diapositiva**

* Implantació de CMS On-Premise
* Portal, intranet i àrea de clients amb Drupal 10
* Client: BCN SecureLegal S.L. · 28/10/2026

**Locució:** «Presentem la plataforma web d'un despatx que custodia expedients sota
secret professional. La decisió clau no va ser *quin* CMS usar, sinó *com demostrar*
que les dades no surten mai del seu CPD.»

## D2 — El client i el problema (2 min) · Emilia

**Text**

* 25 professionals · model B2B · Barcelona i Madrid
* 4 necessitats: blog, catàleg, intranet i àrea de clients
* Restricció innegociable: **100 % On-Premise** (RGPD, secret professional, NIS2)

**Locució:** «La restricció és legal, no tecnològica: res de SaaS multitenant. Això
elimina la meitat del mercat de CMS i condiciona tota l'arquitectura.»

## D3 — Decisió: per què Drupal (2 min) · Aleix

**Text**

* Fitxers privats fora de l'arrel web i permisos per rol i camp
* Equip de seguretat dedicat · multillengua · GPLv2+ sense *lock-in*
* WordPress: cinc connectors per fer el mateix · Joomla: ecosistema més petit

**Locució:** «No l'hem triat per gust: és l'únic que cobreix les quatre necessitats
sense connectors de tercers. Cada connector evitat és una vulnerabilitat menys.»

## D4 — Arquitectura (2 min) · Didac

**Text**

* `bsl-drupal` (127.0.0.1:8080→80) + `bsl-db` (3306 tancat, `service_healthy`)
* Volums `drupal_sites`, `drupal_private` i `db_data`
* Enduriment: secretos per entorn, `no-new-privileges`, rotació de logs

**Locució:** «Dos contenidors, una xarxa interna sense sortida i ni un port de base de
dades exposat. La carpeta privada viu fora de l'arrel web: no hi ha URL que hi arribi.»

## D5 — Cultura oberta i traçabilitat (2 min) · Emilia + Didac

**Text**

* Codi auditable + configuració com a codi (YAML versionat a Git)
* Kanban → branca → PR revisada → CI → desplegament → etiqueta
* Cada canvi amb autor, data i motiu: RGPD art. 5.2 en la pràctica

**Locució:** «Aquí està el pont entre les tres parts del projecte: la transparència no
es promet, es versiona. L'historial de Git és el nostre registre d'auditoria.»

## D6 — Planificació i equip (2 min) · Emilia

**Text**

* 16 dies hàbils · **80 h** · 26,7 / 26,8 / 26,5 h (33 % cadascú)
* 6 fases amb fites verificables
* Risc principal: corba d'aprenentatge de Drupal → revisió creuada

**Locució:** «El repartiment és pràcticament simètric: menys de mitja hora de diferència
entre els tres.»

## D7 — Resultats, límits i següent pas (2 min) · Aleix

**Text**

* Drupal 10.6.19 operatiu · article NIS2 publicat · edició en calent sense reinicis
* 6 proves superades, 0 incidències crítiques
* Límit declarat: el mòdul de traçabilitat és una demostració
* **Petició:** validar el pas a producció amb TLS, backup provat i mòdul complet

**Locució:** «No venim a dir que tot està fet, sinó a demanar la validació de tres
condicions per passar a producció.»

---

## Demo en viu (opcional, 2 min) · Didac

Si hi ha temps, executar la prova d'edició en calent:

```bash
docker inspect -f '{{.State.StartedAt}}' bsl-drupal
sed -i 's/versio 2/versio 3/' modules/custom/bsl_client_docs/bsl_client_docs.module
docker inspect -f '{{.State.StartedAt}}' bsl-drupal   # marca temporal idèntica
```

> Si la demo falla, no improvisar: passar a l'annex i continuar amb les conclusions.

## Preguntes previsibles

| Pregunta | Resposta curta |
| :--- | :--- |
| Per què no WordPress amb plugins? | Cada connector afegeix superfície d'atac i dependència d'un tercer; en un despatx jurídic el risc no és assumible. |
| Com garantiu que ningú baixa un document aliè? | Fitxers privats fora de l'arrel web + permisos per rol i camp; la traçabilitat completa arriba a la fase 2. |
| Què passa si cau el servidor? | RTO de 4 h amb backup diari i snapshots; sense alta disponibilitat per abast acordat. |
| Per què Git és part del producte? | La configuració versionada és l'evidència d'auditoria: qui, quan i per què. |
| Quant costa? | 80 h de projecte; el maquinari és un servidor del CPD existent. |
