# Document 05: Memòria Tècnica i Planificació Executiva

| Dada del projecte | Detall |
| :--- | :--- |
| **Empresa client** | BCN SecureLegal S.L. (serveis jurídics, RGPD i ciberseguretat) |
| **Equip** | Emilia (PM i Business Analyst) · Didac (SysAdmin i DevOps) · Aleix (CMS Developer i QA) |
| **Mòdul acadèmic** | M0376 — Implantació d'aplicacions web (RA2) · Institut Tecnològic de Barcelona |
| **Data de publicació** | 28 d'octubre de 2026 · versió 1.0 (final) |

## 5.1 Objectius del projecte

1. Desplegament On-Premise d'un portal web corporatiu i blog d'actualitat jurídica
   sobre Drupal 10.6.19.
2. Intranet corporativa per a plantilles i circulars internes.
3. Àrea segura de clients per al lliurament d'auditories confidencials.
4. Compliment estricte del secret professional i de la normativa de protecció de dades
   (RGPD i Directiva NIS2).

## 5.2 Requeriments d'infraestructura On-Premise

| Àmbit | Especificació |
| :--- | :--- |
| **Maquinari** | Servidor dedicat al CPD: 4 nuclis / 8 fils, 16 GB RAM DDR4 ECC, 200 GB SSD NVMe en RAID 1, SAI dedicat |
| **Programari base** | GNU/Linux (Ubuntu Server 24.04 LTS o Debian 12), Docker Engine 26.x o superior, Docker Compose v2.27+ |
| **Pila d'aplicació** | Drupal 10.6.19, Apache 2.4.69, PHP 8.3, MariaDB 10.11 |
| **Xarxa** | Xarxa `backend` interna (`internal: true`) i xarxa `edge`; MariaDB aïllada; port web publicat només a `127.0.0.1:8080` |
| **Persistència** | Volums `drupal_sites`, `drupal_private` i `db_data` |
| **Seguretat** | `settings.php` en `444` amb propietari `root:root`; `no-new-privileges`; rotació de logs; secrets fora del repositori |

El detall operatiu és al document [03](03-guia-desplegament-docker.md) i el model de
riscos al document [06](06-seguretat-compliment-i-riscos.md).

## 5.3 Fases d'implantació

| Fase | Dies | Contingut | Fita |
| :--- | :--- | :--- | :--- |
| 1 | 1–3 | Anàlisi de requisits i Business Case | Business Case aprovat |
| 2 | 4–6 | Infraestructura Docker, volums i healthchecks | `docker compose up -d` operatiu |
| 3 | 7–9 | Instal·lació de Drupal i parametrització de seguretat | Drupal 10.6.19 instal·lat |
| 4 | 10–12 | Mòdul `bsl_client_docs` i edició en calent | Mòdul actiu a *Extend* |
| 5 | 13–14 | Banc de proves, continguts i QA | 6 proves superades |
| 6 | 15–16 | Memòria, presentació i defensa | Lliurament final |

## 5.4 Taula de tasques i dedicació

L'esforç total planificat és de **80,0 hores** de treball col·laboratiu: 75,0 h de
tasques assignades més 5,0 h de tasques compartides.

| Fase | Descripció de la tasca | Resp. | Revisor | Suport | Hores |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Anàlisi i planificació del projecte | Emilia | Didac | Aleix | 6,0 h |
| 2 | Empresa: identitat, model de negoci i comunicació | Emilia | Aleix | Didac | 6,0 h |
| 3a | 2.1 Investigació de tres CMS (WP, Drupal, Joomla) | Emilia | Didac | Aleix | 2,5 h |
| 3b | 2.2 Taula comparativa de criteris tècnics | Didac | Emilia | Aleix | 1,5 h |
| 3c | 2.3 Selecció i justificació de Drupal | Aleix | Emilia | Didac | 2,0 h |
| 3d | 2.4 Extensibilitat i disseny del mòdul propi | Aleix | Emilia | Didac | 2,0 h |
| 4 | 3.1 Fitxa tècnica del CMS, versions i permisos | Aleix | Didac | Emilia | 8,0 h |
| 5 | 3.2 Entorn de desenvolupament amb Docker Compose | Aleix | Didac | Emilia | 8,0 h |
| 6 | 3.3 Desplegament de serveis i healthchecks | Didac | Emilia | Aleix | 10,0 h |
| 7 | 3.4 Xarxes internes i seguretat | Didac | Emilia | Aleix | 10,0 h |
| 8 | 3.5 Evidències i proves reals (edició en calent) | Didac | Aleix | Emilia | 8,0 h |
| 9 | 4.1 Memòria del projecte, Gantt i tauler | Emilia | Didac | Aleix | 8,0 h |
| 10a | 4.2 Presentació executiva | Emilia | Aleix | Didac | 3,0 h |
| 10b | 4.3 Vídeo Meet i 4.4 defensa oral | Els tres | Creuat | Creuat | 5,0 h |
| | **Total esforç de l'equip** | | | | **80,0 h** |

> **Correcció respecte a la versió anterior:** la taula sumava 80,0 h però el total
> declarat era de 72,0 h. S'ha corregit el total i, en conseqüència, tota la taula de
> càrregues ponderades (apartat 5.5).

## 5.5 Balanç ponderat de càrregues

Model acordat per l'equip: el responsable aporta el 50 % de les hores de la tasca, el
suport el 30 % i el revisor el 20 %; les tasques compartides es divideixen a parts
iguals (1/3 per membre).

| Membre | H. resp. | H. suport | H. revisor | H. compartides | Càrrega ponderada | % |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Emilia** (PM) | 25,5 h | 24,0 h | 25,5 h | 1,67 h | **26,7 h** | 33,4 % |
| **Didac** (DevOps) | 29,5 h | 13,0 h | 32,5 h | 1,67 h | **26,8 h** | 33,5 % |
| **Aleix** (Dev/QA) | 20,0 h | 38,0 h | 17,0 h | 1,67 h | **26,5 h** | 33,1 % |
| **Total equip** | 75,0 h | 75,0 h | 75,0 h | 5,00 h | **80,0 h** | **100,0 %** |

> **Conclusió de càrrega:** la diferència màxima entre membres és de **0,35 hores
> ponderades** (0,4 % del total), molt per sota del 5 % exigit: el repartiment és
> pràcticament simètric.

## 5.6 Diagrama de Gantt

Planificació en **16 dies hàbils** amb dependències entre fases:

| Fase | Inici | Final | Responsable | Fita / lliurable |
| :--- | :--- | :--- | :--- | :--- |
| 1. Anàlisi i requisits | Dia 1 | Dia 3 | Emilia (PM) | Business Case definit |
| 2. Infraestructura Docker | Dia 4 | Dia 6 | Didac (DevOps) | Pila `docker-compose` operativa |
| 3. Instal·lació de Drupal | Dia 7 | Dia 9 | Aleix (Dev) | Drupal 10.6.19 instal·lat |
| 4. Mòdul `bsl_client_docs` | Dia 10 | Dia 12 | Aleix (Dev) | Bind mount i edició en calent |
| 5. Validació i QA | Dia 13 | Dia 14 | Didac (DevOps) | Banc de proves i diagnosi de logs |
| 6. Memòria i presentació | Dia 15 | Dia 16 | Emilia (PM) / equip | Lliurament final i defensa |

```text
Dia     1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16
F1      ███████
F2            ███████
F3                  ███████
F4                        ███████
F5                              ███████
F6                                    ███████
```

## 5.7 Seguiment del projecte

El seguiment es va fer amb un tauler Kanban a **GitHub Projects** (4 columnes:
*Backlog*, *In Progress*, *In Review*, *Done*), amb revisió creuada obligatòria i
traçabilitat completa des de la tasca fins al desplegament. El detall del flux, els
rols i les convencions de Git és al document
[00](00-cultura-oberta-i-traçabilitat.md).

> [!IMPORTANT] Evidència pendent
> Cal adjuntar la captura del tauler amb les 14 targetes a *Done* i les etiquetes dels
> tres integrants.
