# Guió de la presentació executiva (15 minuts)

> Aquest guió segueix **l'ordre exacte** del fitxer
> `lliurament/Presentacio-Projecte-7-diapositives.pptx` (7 diapositives).
> Repartiment: 5 minuts per persona — Emilia (negoci i gestió), Didac
> (infraestructura) i Aleix (CMS, mòdul i QA).

---

## D1 — Portada (30 s) · Emilia

**Text a la diapositiva:** títol, subtítol, els tres rols i les dades del client.

**Locució:** «Presentem la plataforma web d'un despatx que custodia expedients sota
secret professional. La decisió clau no va ser *quin* CMS usar, sinó *com demostrar*
que les dades no surten mai del CPD del client.»

## D2 — Perfil corporatiu i objectius de negoci (2 min) · Emilia

**Text:** 25 professionals · model B2B · 100 % On-Premise · les quatre necessitats
(blog, catàleg, intranet i àrea de clients).

**Locució:** «La restricció és legal, no tecnològica: res de SaaS multitenant. Això
elimina la meitat del mercat de CMS i condiciona tota l'arquitectura. Per això vam
triar Drupal: cobreix les quatre necessitats sense connectors de tercers, i cada
connector evitat és una vulnerabilitat menys.»

## D3 — Arquitectura de desplegament (2 min) · Didac

**Text:** `bsl-drupal` (127.0.0.1:8080 → 80) + `bsl-db` (3306 tancat,
`service_healthy`) · volums `drupal_sites`, `drupal_private` i `db_data` · imatges
fixades.

**Locució:** «Dos contenidors, una xarxa interna sense sortida i ni un port de base de
dades exposat. La carpeta privada viu fora de l'arrel web: no hi ha cap URL que hi
arribi.»

## D4 — Entorn de desenvolupament i edició en calent (2 min) · Didac + Aleix

**Text:** bind mount del mòdul · `StartedAt` idèntic abans i després de `sed` ·
missatge `versio 1` → `versio 2`.

**Locució:** «Aquesta és la prova que el desplegament és àgil de debò: modifiquem el
codi al host i el contenidor no s'ha reiniciat. Si voleu, ho fem ara en directe.»

## D5 — Planificació, Gantt i repartiment (2 min) · Emilia

**Text:** 16 dies hàbils · **80 h** · 26,7 / 26,8 / 26,5 h (33 % cadascú) · 6 fases amb
fites verificables.

**Locució:** «El repartiment és pràcticament simètric: menys de mitja hora de diferència
entre els tres. Cada fase té un lliurable que es pot comprovar.»

## D6 — Cultura oberta i traçabilitat (2 min) · Emilia + Aleix

**Text:** codi auditable (GPLv2+) · configuració com a codi versionada a Git · cadena
Kanban → branca → PR → CI → tag → desplegament · compliment RGPD art. 5.2 i NIS2.

**Locució:** «Aquí està el pont entre les tres parts del projecte: la transparència no
es promet, es versiona. L'historial de Git és el nostre registre d'auditoria: qui va
canviar què, quan i per què.»

## D7 — Conclusions i resultats (2 min) · Aleix

**Text:** Drupal 10.6.19 operatiu · article NIS2 publicat · mòdul propi provat en calent
· persistència i sobirania · **petició de validació**.

**Locució:** «No venim a dir que tot està fet, sinó a demanar la validació de tres
condicions per passar a producció: TLS, backup provat i mòdul de traçabilitat complet.»

---

## Demo en viu (opcional, 2 min) · Didac

```bash
docker inspect -f '{{.State.StartedAt}}' bsl-drupal
sed -i 's/versio 2/versio 3/' modules/custom/bsl_client_docs/bsl_client_docs.module
docker inspect -f '{{.State.StartedAt}}' bsl-drupal   # marca temporal idèntica
```

> Si la demo falla, no improvisar: passar a la conclusió i continuar.

## Preguntes previsibles

| Pregunta | Resposta curta |
| :--- | :--- |
| Per què no WordPress amb plugins? | Cada connector afegeix superfície d'atac i dependència d'un tercer; en un despatx jurídic el risc no és assumible. |
| Com eviteu que un client baixi un document d'un altre? | Fitxers privats fora de l'arrel web + permisos per rol i camp; la traçabilitat completa arriba a la fase 2 del roadmap. |
| Què passa si cau el servidor? | RTO de 4 h amb backup diari i snapshots; no hi ha alta disponibilitat per abast acordat. |
| Per què Git és part del producte? | La configuració versionada és l'evidència d'auditoria: autor, data i motiu de cada canvi. |
| Quant costa? | 80 h de projecte; el maquinari és un servidor del CPD existent. |
