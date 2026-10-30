# Document 00: Cultura Oberta, Programari Lliure i Traçabilitat

> **Objectiu del document.** Justificar per què la cultura d'empresa oberta, la tria
> d'un CMS de codi obert i la gestió del projecte amb Git no són tres decisions
> independents, sinó una única estratègia de transparència verificable.

---

## 0.1 Per què una firma jurídica ha de ser «oberta»

BCN SecureLegal no ven únicament serveis jurídics: ven **confiança verificable**.
En l'àmbit del RGPD i de la Directiva NIS2, la confiança no es declara, es demostra.
La cultura d'empresa oberta és, per tant, un requisit operatiu i no una declaració
d'intencions: implica transparència en els processos, capacitat d'auditoria per part
del client i absència de dependències opaques de tercers.

Aquest principi es concreta en quatre decisions del projecte:

1. **Programari lliure com a garantia de sobirania.**
   Drupal es distribueix sota llicència GPLv2 o posterior: el codi és auditable, no
   hi ha *vendor lock-in* ni telemetria obligatòria cap a tercers, i la totalitat de
   les dades roman al CPD propi. La llicència lliure no és una qüestió de cost, sinó
   de **control**.

2. **Configuració com a codi (*configuration as code*).**
   Drupal exporta tota la seva estructura —tipus de contingut, rols, permisos, camps
   i vistes— a fitxers YAML mitjançant la directiva `config_sync_directory`. Aquests
   fitxers es versionen al repositori Git, de manera que cada canvi de configuració
   queda registrat amb autor, data i justificació. El resultat és un **registre
   d'auditoria tècnic** alineat amb el principi de responsabilitat proactiva de
   l'article 5.2 del RGPD i amb els requisits de governança de la Directiva NIS2.

3. **Documentació oberta i transferible.**
   El repositori inclou el Business Case, la comparativa de CMS, la guia de
   desplegament, el banc de proves i els registres de decisió arquitectònica (ADR).
   Qualsevol membre de l'equip —o el client— pot reconstruir per què es va prendre
   cada decisió, cosa que redueix el risc de dependència d'una sola persona
   (*bus factor*).

4. **Traçabilitat de l'accés a la informació.**
   El mòdul `bsl_client_docs` estén aquest principi al document confidencial: cada
   accés o descàrrega ha de quedar registrat amb usuari, IP i marca temporal, i
   sotmès a una caducitat verificable.

## 0.2 Del Kanban al desplegament: la cadena de traçabilitat

Cada lliurament del projecte recorre la mateixa cadena, de manera que **cap canvi
arriba a producció sense traçabilitat completa**:

```text
Tasca al Kanban (issue #N)
    └─> Branca de treball: feat/N-descripcio
          └─> Commits amb Conventional Commits
                └─> Pull Request + revisió creuada (obligatòria)
                      └─> CI: yamllint + phpcs + docker compose config
                            └─> Merge a main (branca protegida)
                                  └─> Tag de versió: v1.0.0
                                        └─> Desplegament al CPD
```

Aquesta cadena és la traducció tècnica de la cultura oberta: **qualsevol persona pot
respondre «qui va canviar què, quan i per què» en menys d'un minut**.

## 0.3 Flux de treball amb Git

El repositori segueix una estratègia *trunk-based* amb branques curtes i revisió
creuada obligatòria, coherent amb el model de rols de l'equip:

| Element | Convenció adoptada |
| :--- | :--- |
| Branca principal | `main`, protegida: cap *push* directe |
| Branques de treball | `feat/<issue>-<descripcio>`, `fix/…`, `docs/…`, `chore/…` |
| Missatges de commit | *Conventional Commits*: `feat:`, `fix:`, `docs:`, `chore:`, `ci:` |
| Integració | Pull Request + 1 aprovació d'un altre membre de l'equip |
| Verificació automàtica | GitHub Actions: `yamllint`, `phpcs` (estàndard Drupal), `docker compose config` |
| Versions | Etiquetes semàntiques (`v1.0.0`) sobre `main` |
| Secrets | `.env` exclòs pel `.gitignore`; `.env.example` només amb valors ficticis |
| Propietat intel·lectual | `LICENSE` GPL-2.0+ a l'arrel del repositori |

### Branques i versions publicades

| Branca / etiqueta | Contingut | Estat |
| :--- | :--- | :--- |
| `main` | Branca estable, sempre desplegable | Protegida |
| `feat/1-cultura-oberta` | Document 00 i flux de Git | Integrada |
| `fix/2-correccions-documentacio` | Correccions ortogràfiques, tècniques i de càlcul | Integrada |
| `fix/3-enduriment-compose` | Enduriment de la infraestructura Docker | Integrada |
| `ci/4-governanca` | CI, llicència, plantilles i *changelog* | Integrada |
| `v1.0.0` | Lliurament AP03 (28/10/2026) | Publicada |

## 0.4 Rols del projecte i responsabilitats (RACI)

| Activitat | Emilia (PM/BA) | Didac (SysAdmin/DevOps) | Aleix (Dev/QA) |
| :--- | :--- | :--- | :--- |
| Business Case i model de negoci | **R** | C | C |
| Comparativa i selecció de CMS | C | C | **R** |
| Arquitectura i desplegament Docker | C | **R** | C |
| Configuració de Drupal i mòdul propi | C | C | **R** |
| Banc de proves i QA | C | **R** | C |
| Seguretat i compliment normatiu | C | **R** | C |
| Planificació, Gantt i Kanban | **R** | C | C |
| Memòria i presentació executiva | **R** | C | C |
| Revisió creuada de cada lliurament | A | A | A |

**R** = responsable directe · **A** = aprueba/revisa · **C** = consultat.

## 0.5 Seguiment àgil: tauler Kanban

El seguiment s'ha canalitzat mitjançant un tauler Kanban a **GitHub Projects**,
articulat en quatre columnes:

1. **Backlog** — requisits inicials de cada tasca, amb etiqueta de rol (PM, DevOps, Dev).
2. **In Progress** — tasques en execució activa.
3. **In Review** — revisió creuada de codi i contingut pel revisor designat.
4. **Done** — tasques validades, amb codi integrat i evidències capturades.

> [!IMPORTANT] Evidència pendent d'adjuntar
> Per a la tramesa final al Google Classroom cal incorporar la captura real del
> tauler amb les 14 targetes a la columna *Done* i les etiquetes dels tres
> integrants (Emilia, Didac, Aleix).
