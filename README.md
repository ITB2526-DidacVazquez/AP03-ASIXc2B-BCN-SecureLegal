# 🛡️ BCN SecureLegal S.L. — Plataforma CMS Drupal 10 (On-Premise)

[![CFGS ASIXc2](https://img.shields.io/badge/CFGS-ASIXc2-blue.svg)](https://www.itb.cat/)
[![Drupal](https://img.shields.io/badge/Drupal-10.6.19-0678BE.svg)](https://www.drupal.org/)
[![MariaDB](https://img.shields.io/badge/MariaDB-10.11-003545.svg)](https://mariadb.org/)
[![Docker Compose](https://img.shields.io/badge/Docker%20Compose-v2.27+-2496ED.svg)](https://docs.docker.com/compose/)
[![License: GPL-2.0+](https://img.shields.io/badge/License-GPL--2.0%2B-green.svg)](LICENSE)

Projecte acadèmic d'implantació d'un gestor de continguts (CMS) per a la firma jurídica especialitzada en ciberseguretat i protecció de dades **BCN SecureLegal S.L.**, desenvolupat en el marc del mòdul **M0376 (RA2) - Implantació d'aplicacions web** a l'**Institut Tecnològic de Barcelona (ITB)**.

---

## 👥 Equip de Desenvolupament (Grup ASIXc 2B)

* **Rol A: Project Manager & Business Analyst — Emilia**  
  *Responsable de la definició corporativa, memòria del projecte, planificació temporal (Gantt, tauler de tasques) i coordinació executiva.*
* **Rol B: SysAdmin & DevOps — Didac ([@ITB2526-DidacVazquez](https://github.com/ITB2526-DidacVazquez))**  
  *Responsable de l'arquitectura d'infraestructura Docker Compose, xarxes aïllades, persistència amb volums amb nom i banc de proves tècniques.*
* **Rol C: CMS Developer & QA — Aleix**  
  *Responsable de la selecció tècnica del programari, configuració de Drupal 10, desenvolupament del mòdul d'extensió `bsl_client_docs` i control de qualitat (QA).*

---

## 🏛️ Arquitectura del Sistema (100% On-Premise)

Pel secret professional i la normativa de protecció de dades (RGPD, Directiva NIS2), el sistema opera íntegrament en servidors locals propis (On-Premise):

```text
Host Linux (CPD On-Premise)
│
├── [Docker Network: bridge interna]
│   ├── bsl-drupal (drupal:10-apache)
│   │   ├── Port exposat: 8080 -> 80 (HTTP)
│   │   ├── Volum persistent: drupal_sites -> /var/www/html/sites
│   │   └── Bind Mount: ./modules/custom/bsl_client_docs -> /var/www/html/modules/custom/bsl_client_docs
│   │
│   └── bsl-db (mariadb:10.11)
│       ├── Port intern: 3306 (aïllat a la xarxa bridge, sense exposició al host)
│       ├── Healthcheck: healthcheck.sh --connect --innodb_initialized
│       └── Volum persistent: db_data -> /var/lib/mysql
```

---

## 🚀 Guia Ràpida d'Arrencada

### Requisits previs
* Docker Engine 24+ i Docker Compose v2+
* Git i GNU Make / Bash

### 1. Clonar el repositori
```bash
git clone https://github.com/ITB2526-DidacVazquez/AP03-ASIXc2B-BCN-SecureLegal.git
cd AP03-ASIXc2B-BCN-SecureLegal
```

### 2. Configurar variables d'entorn
```bash
cp .env.example .env
```

### 3. Iniciar els serveis amb Docker Compose
```bash
docker compose up -d
```

### 4. Comprovar l'estat dels contenidors
```bash
docker compose ps
```
El servei `bsl-db` mostrarà l'estat `(healthy)` abans que `bsl-drupal` rebi connexions gràcies a la directiva `depends_on: condition: service_healthy`.

### 5. Accedir a l'aplicació
Obre el navegador web a:  
👉 **http://localhost:8080**

---

## 🧩 Mòdul Propi: `bsl_client_docs`
El mòdul personalitzat s'allotja a `modules/custom/bsl_client_docs/` i té com a missió proporcionar traçabilitat d'accés i caducitat d'enllaços als documents confidencials de l'àrea de clients.
Gràcies al mecanisme de **bind mount**, qualsevol canvi realitzat des de la màquina host (com la comanda `sed -i 's/versio 1/versio 2/' ...`) es reflecteix a l'instant al CMS sense haver de reiniciar el contenidor (`docker inspect` StartedAt inalterat).

---

## 📚 Documentació del Projecte
Els informes i memòries tècniques completes es troben a la carpeta [`docs/`](docs/):
* [`01-business-case.md`](docs/01-business-case.md): Identitat, model de negoci i necessitats de comunicació.
* [`02-seleccio-cms.md`](docs/02-seleccio-cms.md): Investigació comparativa de WordPress, Drupal i Joomla.
* [`03-guia-desplegament-docker.md`](docs/03-guia-desplegament-docker.md): Detall de serveis, volums, xarxes i permisos.
* [`04-memoria-tecnica.md`](docs/04-memoria-tecnica.md): Memòria completa d'implantació, Gantt i repartiment d'esforços.

---

## 📄 Llicència
Aquest projecte es distribueix sota llicència de codi obert [GNU General Public License v2.0 o posterior (GPL-2.0+)](https://www.gnu.org/licenses/old-licenses/gpl-2.0.html).
