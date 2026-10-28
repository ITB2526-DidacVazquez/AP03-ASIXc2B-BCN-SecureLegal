# Memòria Tècnica del Projecte: Implantació de CMS On-Premise
## Exercici 4: Lliurable Independent — BCN SecureLegal S.L. — ASIXc2B

| Dades del Projecte | Detall |
| :--- | :--- |
| **Empresa Client** | BCN SecureLegal S.L. (Serveis Jurídics, RGPD i Ciberseguretat) |
| **Equip de Treball** | Emilia (Project Manager) &nbsp;|&nbsp; Didac (SysAdmin & DevOps) &nbsp;|&nbsp; Aleix (CMS Developer & QA) |
| **Mòdul Acadèmic** | M0376 - Implantació d'aplicacions web (RA2) — Institut Tecnològic de Barcelona |
| **Data de Publicació** | 28 d'octubre de 2026 — Versió 1.0 Final |

---

## 1. Descripció de l'Empresa

BCN SecureLegal S.L. és una firma de consultoria jurídica i auditoria tecnològica fundada a Barcelona, especialitzada en la protecció de dades de caràcter personal (RGPD, LOPDGDD), dret digital, ciberseguretat i compliment de directives europees de seguretat de les xarxes i sistemes d'informació (Directiva NIS2).

L'empresa disposa de la seva seu corporativa central al districte tecnològic de les Glòries (Barcelona) i compta amb una delegació comercial a Madrid. La plantilla està constituïda per 25 treballadors d'alt perfil professional (advocats experts en dret TIC, enginyers auditors de ciberseguretat, consultors de compliment normatiu i personal de gestió).

El seu model de negoci opera exclusivament en l'àmbit **B2B (Business to Business)**, prestant serveis d'assessorament recurrent, auditories de vulnerabilitats, plans de continuïtat de negoci i servei de Delegat de Protecció de Dades extern (DPO as a Service) per a hospitals privats, pimes industrials, entitats financeres i corporacions locals d'Espanya i de la Unió Europea.

---

## 2. Objectius del Projecte

La implantació del nou sistema corporatiu té per finalitat modernitzar la presència digital de BCN SecureLegal i establir una plataforma segura de gestió de continguts i relació amb els clients:

1. **Portal de continguts jurídics i catàleg de serveis:** Dissenyar i posar en producció un portal web amb blog especialitzat en notícies jurídiques, resolucions de l'AEPD i anàlisi de sentències per generar autoritat de marca i atreure nous clients corporatius.
2. **Intranet corporativa:** Habilitar una intranet amb autenticació protegida per gestionar plantilles contractuals, guies de bones pràctiques i protocols interns de l'equip jurídic.
3. **Àrea de clients protegida:** Desenvolupar un repositori d'alta seguretat mitjançant un sistema de fitxers privats per lliurar informes confidencials d'auditoria i documents legals amb caducitat i traçabilitat estricta.
4. **Compliment rigorós del requisit On-Premise:** Allotjar la plataforma íntegrament en servidors propis (On-Premise) al CPD de l'empresa sota contenidors Docker Compose, garantint la sobirania absoluta de la informació i el secret professional sense cap dependència de proveïdors SaaS externs.

---

## 3. Requeriments Tècnics del Projecte

D'acord amb la infraestructura real validada a l'Exercici 3, els requeriments tècnics del sistema s'estructuren sense alta disponibilitat ni sistemes de fitxers compartits per xarxa NFS, prioritzant la simplicitat, el rendiment i l'aïllament:

| Àmbit | Especificació Tècnica On-Premise |
| :--- | :--- |
| **Hardware Recomanat** | Servidor dedicat al CPD propi: CPU 4 nuclis / 8 fils (Intel Xeon o AMD EPYC), 16 GB RAM DDR4 ECC, 200 GB d'emmagatzematge SSD NVMe en configuració RAID 1 per a redundància de dades, SAI (UPS) dedicat. |
| **Software Base** | Sistema Operatiu GNU/Linux (Ubuntu Server 24.04 LTS o Debian 12 64-bit), motor Docker Engine 26.x o superior, Docker Compose v2.27+, GNU Nano / Bash. |
| **Plataforma CMS** | Drupal 10.6.19 sobre servidor web Apache 2.4.69 (Debian) i intèrpret PHP 8.3 integrats a la imatge oficial `drupal:10-apache`. |
| **Base de Dades** | MariaDB 10.11 (imatge `mariadb:10.11`) amb codificació `utf8mb4` i motor d'emmagatzematge transaccional InnoDB. |
| **Xarxa i Ports** | Xarxa `bridge` privada interna de Docker. El port web 80 del contenidor s'exposa al port 8080 del host (amb terminació HTTPS/TLS 1.3 mitjançant proxy corporatiu). El port 3306 de MariaDB roman aïllat a la xarxa interna de Docker sense exposició exterior. |
| **Persistència de Dades** | Volum amb nom `drupal_sites` per al directori `/var/www/html/sites` (preservant `settings.php` i pujades públiques); volum amb nom `db_data` per a `/var/lib/mysql`. Carpeta privada `/var/www/private` fora de l'arrel web per a informes confidencials. |
| **Polítiques de Seguretat** | Permisos de només lectura per a `core/` i `vendor/`; fitxer `settings.php` protegit en mode 444; contrasenyes de BD parametritzades per variables d'entorn; còpies de seguretat diàries automatitzades amb `mysqldump` i snapshots de volums. |

---

## 4. Estratègies d'Implantació

La implantació es desenvolupa seguint un cicle de vida dividit en 6 fases consecutives:

* **Fase 1: Anàlisi de requisits i viabilitat (Dies 1 a 3):** Definició del Business Case de BCN SecureLegal, avaluació comparativa de CMS (WordPress vs Drupal vs Joomla), selecció de Drupal i formalització del repartiment de tasques.
* **Fase 2: Preparació de la infraestructura On-Premise (Dies 4 a 6):** Instal·lació de Docker i Docker Compose al servidor Linux, redacció de `docker-compose.yml`, definició de healthchecks per a MariaDB, configuració de dependències amb `condition: service_healthy` i creació de volums persistents.
* **Fase 3: Instal·lació i configuració base del CMS (Dies 7 a 9):** Execució de l'assistent d'instal·lació de Drupal 10.6.19 en català, connexió amb MariaDB, establiment del compte d'administrador `sysadmin_bsl` i fixació de permisos segurs (`settings.php` en 444).
* **Fase 4: Desenvolupament del mòdul personalitzat (Dies 10 a 12):** Configuració del bind mount per a la carpeta `modules/custom/bsl_client_docs/`, creació dels fitxers de metadades i codi PHP, activació a Extend i validació del cicle d'edició en calent via comanda `sed`.
* **Fase 5: Banc de proves, seguretat i QA (Dies 13 a 14):** Creació de tipus de continguts reals (Article NIS2), verificació d'aïllament de l'àrea privada, revisió de logs de contenidors (diagnosi de l'avís d'obsolescència `assert.active`) i proves de rendiment.
* **Fase 6: Tancament, memòria executiva i traspàs (Dies 15 a 16):** Redacció de la memòria tècnica, elaboració de la presentació executiva de 6 diapositives, enregistrament de la simulació de reunió Meet i preparació de la defensa a classe.

---

## 5. Taula de Tasques i Hores

A continuació es detalla el pla de treball amb les tasques, responsables principals, revisors i hores estimades extretes de la planificació consolidada de l'equip (`AP03_reparto_v2.xlsx`), sumant un esforç total de **72 hores** de treball col·laboratiu:

| Fase | Descripció de la Tasca | Resp. | Revisor | Suport | Hores |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | Anàlisi i planificació del projecte | Emilia | Didac | Aleix | 6.0 h |
| **2** | Empresa: Identitat, model de negoci i comunicació | Emilia | Aleix | Didac | 6.0 h |
| **3a** | 2.1 Investigació de 3 CMS (WP, Drupal, Joomla) | Emilia | Didac | Aleix | 2.5 h |
| **3b** | 2.2 Taula comparativa de criteris tècnics | Didac | Emilia | Aleix | 1.5 h |
| **3c** | 2.3 Selecció i justificació de Drupal | Aleix | Emilia | Didac | 2.0 h |
| **3d** | 2.4 Extensibilitat i disseny del plugin propi | Aleix | Emilia | Didac | 2.0 h |
| **4** | 3.1 Fitxa tècnica del CMS, versions i permisos | Aleix | Didac | Emilia | 8.0 h |
| **5** | 3.2 Entorn Dev amb Docker Compose i bind mounts | Aleix | Didac | Emilia | 8.0 h |
| **6** | 3.3 Desplegament de serveis i healthchecks | Didac | Emilia | Aleix | 10.0 h |
| **7** | 3.4 Configuració de xarxes internes i seguretat | Didac | Emilia | Aleix | 10.0 h |
| **8** | 3.5 Evidències i proves reals (edició en calent) | Didac | Aleix | Emilia | 8.0 h |
| **9** | 4.1 Memòria del projecte, Gantt i tauler | Emilia | Didac | Aleix | 8.0 h |
| **10a** | 4.2 Presentació executiva en PowerPoint (6 slides) | Emilia | Aleix | Didac | 3.0 h |
| **10b** | 4.3 Vídeo Meet i 4.4 Presentació oral a classe | Els tres | Cruzado | Cruzado | 5.0 h |
| | **Total Esforç Equip** | | | | **72.0 h** |

---

## 6. Diagrama de Gantt

La planificació temporal s'ha estructurat en **16 dies hàbils laborals**, coordinant dependències de tasques:

| Fase de Treball | Inici | Final | Responsable | Fita / Lliurable |
| :--- | :--- | :--- | :--- | :--- |
| **Fase 1: Anàlisi i Requisits** | Dia 1 | Dia 3 | Emilia (PM) | Business Case definit |
| **Fase 2: Infraestructura Docker** | Dia 4 | Dia 6 | Didac (DevOps) | Pila docker-compose operativa |
| **Fase 3: Instal·lació Drupal** | Dia 7 | Dia 9 | Aleix (Dev) | Drupal 10.6.19 instal·lat |
| **Fase 4: Mòdul bsl_client_docs** | Dia 10 | Dia 12 | Aleix (Dev) | Bind mount i edició en calent |
| **Fase 5: Validació i QA** | Dia 13 | Dia 14 | Didac (DevOps) | Banc de proves i diagnosi logs |
| **Fase 6: Memòria i Presentació** | Dia 15 | Dia 16 | Emilia (PM) / Equip | Lliurament final i defensa |

---

## 7. Repartiment per Persona i Càrrega Ponderada

Per garantir l'equitat i simular un equip professional, s'aplica el model de càrrega ponderada establert a l'equip: el responsable aporta el 50% de les hores de la tasca, el suport aporta el 30%, el revisor aporta el 20%, i les tasques conjuntes es divideixen a parts iguals (1/3 per membre):

| Persona | H. Resp. | H. Suport | H. Revisor | H. Compart. | Càrrega Pond. | % Total |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Emilia (Rol A)** | 25.5 h | 16.0 h | 25.5 h | 1.67 h | **24.3 h** | 33.8 % |
| **Didac (Rol B)** | 29.5 h | 16.0 h | 21.5 h | 1.67 h | **25.5 h** | 35.4 % |
| **Aleix (Rol C)** | 20.0 h | 35.0 h | 12.0 h | 1.67 h | **22.2 h** | 30.8 % |
| **Total Equip** | 75.0 h | 67.0 h | 59.0 h | 5.00 h | **72.0 h** | **100.0 %** |

> [!NOTE] Conclusió de càrrega
> La diferència màxima entre membres és de només 3.3 hores ponderades (diferència inferior al 5%), complint amb escreix l'equilibri de dedicació exigit a l'activitat.

---

## 8. Estratègia i Eina de Seguiment (GitHub Projects / Trello)

Per gestionar el projecte de manera àgil i transparent, s'ha emprat un tauler Kanban a **GitHub Projects** (alternativament compatible amb Trello) articulat en 4 columnes de flux:
1. **Backlog:** Tasques planificades pendents d'iniciar amb definició de requeriments i etiquetes de rol (PM, DevOps, Dev).
2. **En Curs (In Progress):** Tasques assignades que es troben en fase activa d'execució tècnica o redacció.
3. **Revisió (Peer Review):** Tasques finalitzades pel responsable pendents de validació creuada pel revisor designat.
4. **Fet (Done):** Tasques completament superades amb codi integrat i evidències capturades.

> [!IMPORTANT] Nota sobre la captura del tauler de seguiment
> Per a la tramesa final definitiva al Google Classroom, cal incorporar la captura de pantalla real del tauler de treball (GitHub Projects / Trello) de l'equip mostrant l'estat final de les 14 targetes de tasques en columna 'Done' amb les etiquetes dels integrants (Emilia, Didac, Aleix).
