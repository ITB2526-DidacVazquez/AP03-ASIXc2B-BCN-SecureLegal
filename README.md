# BCN SecureLegal S.L. — Plataforma CMS Drupal 10 (On-Premise)

[![CFGS ASIXc2B](https://img.shields.io/badge/CFGS-ASIXc2B-102C57.svg)](https://www.itb.cat/)
[![Drupal](https://img.shields.io/badge/Drupal-10.6.19-0678BE.svg)](https://www.drupal.org/)
[![MariaDB](https://img.shields.io/badge/MariaDB-10.11-003545.svg)](https://mariadb.org/)
[![Docker Compose](https://img.shields.io/badge/Docker%20Compose-v2.27+-2496ED.svg)](https://docs.docker.com/compose/)
[![CI](https://github.com/ITB2526-DidacVazquez/AP03-ASIXc2B-BCN-SecureLegal/actions/workflows/ci.yml/badge.svg)](https://github.com/ITB2526-DidacVazquez/AP03-ASIXc2B-BCN-SecureLegal/actions/workflows/ci.yml)
[![License: GPL-2.0+](https://img.shields.io/badge/License-GPL--2.0%2B-green.svg)](LICENSE)

Projecte d'implantació d'un gestor de continguts (CMS) per a la firma jurídica **BCN
SecureLegal S.L.**, especialitzada en ciberseguretat i protecció de dades. Desenvolupat
en el marc del mòdul **M0376 (RA2) — Implantació d'aplicacions web** de l'**Institut
Tecnològic de Barcelona (ITB)**.

> **Idea central del projecte:** una firma jurídica no pot *prometre* confidencialitat,
> l'ha de *demostrar*. Per això el sistema és 100 % On-Premise, de codi obert i amb tota
> la configuració versionada a Git: cada canvi queda auditat amb autor, data i motiu.

---

## Índex

1. [Equip i rols](#1-equip-i-rols)
2. [Arquitectura del sistema](#2-arquitectura-del-sistema)
3. [Estructura del repositori](#3-estructura-del-repositori)
4. [Guia d'arrencada](#4-guia-darrencada)
5. [Desenvolupament en calent del mòdul](#5-desenvolupament-en-calent-del-mòdul)
6. [Documentació tècnica](#6-documentació-tècnica)
7. [Flux de treball amb Git](#7-flux-de-treball-amb-git)
8. [Estat del projecte, limitacions i roadmap](#8-estat-del-projecte-limitacions-i-roadmap)
9. [Llicència](#9-llicència)

---

## 1. Equip i rols

| Rol | Persona | Responsabilitats |
| :--- | :--- | :--- |
| **Rol A** — Project Manager i Business Analyst | Emilia | Model de negoci, memòria executiva, planificació (Gantt, Kanban) i coordinació |
| **Rol B** — SysAdmin i DevOps | Didac ([@ITB2526-DidacVazquez](https://github.com/ITB2526-DidacVazquez)) | Arquitectura Docker Compose, xarxes aïllades, persistència i banc de proves |
| **Rol C** — CMS Developer i QA | Aleix | Selecció tècnica, configuració de Drupal, mòdul `bsl_client_docs` i QA |

## 2. Arquitectura del sistema

Per imperatiu legal (secret professional) i compliment normatiu (RGPD, Directiva NIS2),
el sistema s'executa íntegrament en servidors propis (On-Premise):

```text
Host Linux (CPD On-Premise)
│
├── [Xarxa Docker: edge]
│   └── bsl-drupal (drupal:10.6.19-apache)
│       ├── Port publicat: 127.0.0.1:8080 -> 80  (darrere del proxy TLS)
│       ├── Volum: drupal_sites   -> /var/www/html/sites
│       ├── Volum: drupal_private -> /var/www/private   (fora de l'arrel web)
│       └── Bind mount: ./modules/custom/bsl_client_docs
│
└── [Xarxa Docker: backend — internal: true]
    └── bsl-db (mariadb:10.11.19)
        ├── Port intern: 3306 (no publicat al host)
        ├── Healthcheck: healthcheck.sh --connect --innodb_initialized
        └── Volum: db_data -> /var/lib/mysql
```

| Component | Versió fixada | Justificació |
| :--- | :--- | :--- |
| Drupal | 10.6.19 | CMS seleccionat per permisos granulars i fitxers privats natius |
| Apache / PHP | 2.4.69 / 8.3 | Inclosos a la imatge oficial `drupal:10.6.19-apache` |
| MariaDB | 10.11.19 | Branca LTS, motor transaccional InnoDB |

## 3. Estructura del repositori

```text
AP03-ASIXc2B-BCN-SecureLegal/
├── docker-compose.yml              # Serveis db i drupal, xarxes i volums
├── .env.example                    # Plantilla de variables (sense secrets reals)
├── .gitignore / .gitattributes     # Exclusions i normalització de fitxers
├── .editorconfig                   # Estil de codi comú
├── LICENSE                         # GPL-2.0+
├── CONTRIBUTING.md                 # Convencions de branques i commits
├── CHANGELOG.md                    # Historial de versions
├── .github/
│   ├── workflows/ci.yml            # CI: yamllint, phpcs, docker compose config
│   └── pull_request_template.md    # Plantilla de revisió creuada
├── modules/custom/bsl_client_docs/ # Mòdul propi (versió de demostració)
└── docs/
    ├── 00-cultura-oberta-i-traçabilitat.md
    ├── 01-business-case.md
    ├── 02-seleccio-i-comparativa-cms.md
    ├── 03-guia-desplegament-docker.md
    ├── 04-banc-de-proves-i-evidencies.md
    ├── 05-memoria-i-planificacio.md
    ├── 06-seguretat-compliment-i-riscos.md
    ├── 07-practica-ap03-lliurament.md
    └── adr/                        # Registres de decisió arquitectònica
        ├── 0001-seleccio-cms.md
        └── 0002-desplegament-on-premise.md
```

## 4. Guia d'arrencada

### Requisits previs

* GNU/Linux (Ubuntu Server 24.04 LTS o Debian 12 recomanats).
* Docker Engine 26.x o superior.
* Docker Compose v2.27 o superior.
* Git.

### Passos

```bash
# 1. Clonar el repositori
git clone git@github.com:ITB2526-DidacVazquez/AP03-ASIXc2B-BCN-SecureLegal.git
cd AP03-ASIXc2B-BCN-SecureLegal

# 2. Crear el fitxer d'entorn i definir contrasenyes reals
cp .env.example .env
#    editeu .env: MARIADB_ROOT_PASSWORD i MARIADB_PASSWORD són obligatòries

# 3. Validar la configuració
docker compose config --quiet

# 4. Desplegar
docker compose up -d

# 5. Comprovar l'estat
docker compose ps     # bsl-db ha de mostrar "healthy"
curl -sI http://localhost:8080   # ha de retornar 302 cap a l'instal·lador
```

Accés web: <http://localhost:8080> (només interfície local; el tràfic extern passa pel
proxy invers amb TLS).

## 5. Desenvolupament en calent del mòdul

El directori `./modules/custom/bsl_client_docs` es munta al contenidor com a bind mount.
Això permet editar el codi font des del host i veure els canvis al navegador sense
reiniciar el contenidor:

```bash
# Comprovar que el contenidor no s'ha reiniciat
docker inspect -f '{{.State.StartedAt}}' bsl-drupal

# Modificar el codi al host
sed -i 's/versio 1/versio 2/' modules/custom/bsl_client_docs/bsl_client_docs.module

# Tornar a comprovar: la marca temporal ha de ser idèntica
docker inspect -f '{{.State.StartedAt}}' bsl-drupal
```

> **Nota:** la imatge oficial no inclou Drush. Per netejar la memòria cau, feu servir
> *Configuració → Rendiment → Esborra tots els caches*. Vegeu el document 03, §3.6.

## 6. Documentació tècnica

| Document | Contingut |
| :--- | :--- |
| [00 — Cultura oberta i traçabilitat](docs/00-cultura-oberta-i-traçabilitat.md) | Per què open source, configuració com a codi i Git sostenen la confiança |
| [01 — Business Case](docs/01-business-case.md) | Empresa, model B2B, necessitats i requisit On-Premise |
| [02 — Selecció de CMS](docs/02-seleccio-i-comparativa-cms.md) | Comparativa, justificació, matriu de rols i mòdul propi |
| [03 — Desplegament Docker](docs/03-guia-desplegament-docker.md) | Serveis, xarxes, volums, permisos i verificació |
| [04 — Banc de proves](docs/04-banc-de-proves-i-evidencies.md) | Proves, evidències i diagnosi de logs |
| [05 — Memòria i planificació](docs/05-memoria-i-planificacio.md) | Objectius, 80 h, Gantt i càrregues ponderades |
| [06 — Seguretat i compliment](docs/06-seguretat-compliment-i-riscos.md) | Model d'amenaces, backups, RTO/RPO i normativa |
| [07 — Lliurament AP03](docs/07-practica-ap03-lliurament.md) | Text complet del lliurament acadèmic |

## 7. Flux de treball amb Git

| Element | Convenció |
| :--- | :--- |
| Branca principal | `main`, protegida: cap *push* directe |
| Branques de treball | `feat/<issue>-<descripcio>`, `fix/…`, `docs/…`, `chore/…`, `ci/…` |
| Missatges de commit | *Conventional Commits* (`feat:`, `fix:`, `docs:`, `ci:`) |
| Integració | Pull Request + 1 aprovació d'un altre membre |
| Verificació | GitHub Actions: `yamllint`, `phpcs` (Drupal), `docker compose config` |
| Versions | Etiquetes semàntiques (`v1.0.0`) sobre `main` |
| Secrets | `.env` exclòs; `.env.example` només amb valors ficticis |

Detall complet a [CONTRIBUTING.md](CONTRIBUTING.md) i al
[document 00](docs/00-cultura-oberta-i-traçabilitat.md).

## 8. Estat del projecte, limitacions i roadmap

**Versió actual: `v1.0.0`** — lliurament AP03 (28/10/2026). Branca `main` neta, CI en
verd, 8 documents versionats.

### Limitacions conegudes

1. `bsl_client_docs` és una **versió de demostració**: no registra accessos ni aplica
   caducitat d'enllaços.
2. La imatge oficial no inclou Composer ni Drush, cosa que bloqueja
   `config:export`/`config:import`.
3. No hi ha desplegament automàtic: `docker compose up -d` és manual.
4. La prova de restauració de backups no s'ha executat.
5. Alta disponibilitat i NFS queden fora d'abast per indicació del professorat.

### Roadmap

| Fase | Contingut | Prioritat |
| :--- | :--- | :--- |
| 1 | `Dockerfile` propi amb Composer i Drush + configuració com a codi | Alta |
| 2 | Mòdul complet: taula de log, caducitat d'enllaços i permisos | Alta |
| 3 | Proxy invers amb TLS 1.3 i capçaleres de seguretat | Alta |
| 4 | Prova de restauració documentada i automatització de backups | Mitjana |
| 5 | Desplegament automàtic des de `main` (GitHub Actions + *runner* al CPD) | Mitjana |
| 6 | Actualització a Drupal 10.6.20 i calendari de pedaços | Mitjana |

## 9. Llicència

Aquest projecte es distribueix sota llicència de codi obert
[GNU General Public License v2.0 o posterior (GPL-2.0+)](LICENSE).
