# RA2 - Pràctica AP03 Investigació i Implementació de CMS (part 1)

| Objectius | Implanta gestors de continguts seleccionant-los i establint la configuració dels paràmetres. |
| :--- | :--- |
| **Tipus de Pràctica** | Activitat pràctica grupal |
| **Material:** | Lliure |
| **Lliurament** | S'ha de lliurar una còpia d'aquest document amb les respostes a la tasca corresponent del **Google Classroom**. Documenta el procés amb **captures de pantalla** i explica amb les teves paraules què has fet i quins problemes has trobat. Les captures han de contenir els elements necessaris per **identificar l'autoria i veracitat**. No es permet alterar les captures de pantalla ni cap còpia literal de cap font. El treball pot ser avaluat oralment. |
| **Dades del Grup** | **NOM I COGNOMS:** Emilia, Didac, Aleix &nbsp;|&nbsp; **GRUP:** ASIXc 2B &nbsp;|&nbsp; **DATA:** 21/10/2026 (Revisió final: 28/10/2026) |

---

## 1. Objectiu de la Pràctica

Simular el cicle de vida d'un projecte tecnològic real basat en un **CMS (Content Management System)**. L'objectiu és definir una necessitat de negoci, investigar i seleccionar el CMS adequat, i dissenyar/implementar una arquitectura de desplegament professional, segura i conteneritzada mitjançant **Docker Compose**, acabant amb la presentació del projecte al client.

---

## 2. Organització i Rols

La pràctica es realitza en **grups de 3 persones**. Per facilitar el repartiment de la feina i simular un entorn professional corporatiu, cada membre ha assumit un rol principal, mantenint una col·laboració transversal i revisió mútua obligatòria:

* **Rol A: Project Manager & Business Analyst — Emilia.** Responsable de la definició de l'empresa (Business Case), la memòria executiva del projecte, la planificació temporal (Gantt, tauler de seguiment) i la presentació executiva.
* **Rol B: SysAdmin & DevOps — Didac.** Responsable de l'arquitectura d'infraestructura, desplegament Docker Compose, gestió de contenidors, volums persistents, xarxes aïllades i documentació del banc de proves/logs tècnics.
* **Rol C: CMS Developer & QA — Aleix.** Responsable de la investigació tècnica del programari, configuració de Drupal, entorn de desenvolupament amb bind mounts, proves del mòdul personalitzat de traçabilitat i documentació tècnica.

---

## 3. Desenvolupament de la Pràctica

### Exercici 1: Business Case (6 punts)

#### 1.1 Identitat i sector
* **Nom:** BCN SecureLegal S.L.
* **Sector:** serveis jurídics altament especialitzats en protecció de dades, ciberseguretat, compliment normatiu (RGPD, LOPDGDD, Directiva NIS2) i auditories de seguretat informàtica.
* **Logotip:**

<p align="center">
  <img src="project_images/logo_bsl.png" alt="Logotip oficial de BCN SecureLegal S.L." width="360" />
  <br/><em>Figura 1: Logotip corporatiu oficial de BCN SecureLegal S.L.</em>
</p>

#### 1.2 Ubicació i abast
La seu central de l'empresa està ubicada a Barcelona (districte de les Glòries, districte 22@), comptant amb una delegació operativa a Madrid. BCN SecureLegal desenvolupa la seva activitat principal a Catalunya i la resta d'Espanya, però assessora regularment entitats i empreses europees que requereixen compliment transfronterer del RGPD i de la normativa europea de ciberseguretat, consolidant un abast nacional amb clara vocació internacional a l'àmbit de la Unió Europea.

#### 1.3 Model de negoci i client
L'empresa opera sota un model de negoci purament **B2B (Business to Business)**, prestant serveis com ara hores d'assessorament jurídic-tècnic, auditories de compliment de seguretat i privacitat, plans de formació a mida i externalització de la figura de Delegat de Protecció de Dades (DPO / DPD). La seva cartera de clients comprèn petites i mitjanes empreses tecnològiques, hospitals i clíniques privades, cooperatives i administracions locals. L'equip humà està format per uns 25 professionals altament qualificats entre advocats tecnològics, auditors de ciberseguretat, consultors de compliment i personal d'administració.

#### 1.4 Necessitats de comunicació
Per donar resposta als requeriments estratègics de l'empresa, es defineixen quatre necessitats essencials de comunicació digital:
1. **Blog jurídic i portal de notícies:** publicació periòdica d'articles especialitzats, resolucions sancionadores de l'AEPD i anàlisi de novetats normatives per al posicionament i captació de nous clients corporatius.
2. **Catàleg de serveis:** presentació estructurada dels serveis oferts, tarifes orientatives, condicions de contractació i formularis de contacte i petició de pressupost.
3. **Intranet corporativa:** espai d'accés exclusiu per al personal de l'empresa per compartir plantilles contractuals, protocols d'actuació, guies internes i circulars.
4. **Àrea de clients protegida:** repositori xifrat i estrictament aïllat on cada client pot consultar i descarregar informes d'auditoria, certificats i documentació confidencial sota estrictes controls d'accés.

#### 1.5 Requeriment clau: On-Premise
Atesa la naturalesa altament sensible de les dades gestionades, el secret professional dels lletrats i les exigències rigoroses de la legislació de protecció de dades, BCN SecureLegal no pot delegar l'allotjament de dades ni documents de clients en plataformes SaaS ni en núvols públics compartits (multitenant).

Per tant, és un requeriment innegociable que la totalitat de la infraestructura sigui desplegada **On-Premise**, allotjada al CPD propi de l'empresa sota el seu control físic i lògic directe. Aquest factor condiciona directament la selecció del CMS (requerint programari de codi obert i llicència lliure sense dependència de subscripcions a tercers) i l'arquitectura del sistema (garantint un desplegament conteneritzat autònom, resilient i amb dades estrictament custodiades a nivell local).

---

### Exercici 2: Selecció del CMS (12 punts)

#### 2.1 Investigació de tres CMS
Davant els requisits d'un portal amb blog especialitzat, catàleg, intranet corporativa i repositori privat per a l'àrea de clients desplegat On-Premise, s'han analitzat tres dels gestors de continguts de codi obert líders del mercat:

* **WordPress:** És el gestor de continguts amb major quota d'implantació a nivell mundial. Basat en PHP i sistemes de bases de dades MySQL/MariaDB, destaca per una interfície d'edició per blocs molt intuïtiva i un vast ecosistema de plantilles i connectors. No obstant això, el seu punt crític rau en la seguretat: la gran heterogeneïtat i desigualtat de qualitat dels connectors de tercers constitueix el principal vector de vulnerabilitats del sector. A més, el seu model de rols i permisos és bàsic, requerint múltiples mòduls externs per gestionar restriccions avançades.
* **Drupal:** És una plataforma de gestió de continguts i marc de treball modular concebut per a projectes empresarials de gran envergadura i requeriments avançats de seguretat. Escrit en PHP sota patrons orientats a objectes i recolzat en components del framework Symfony, és compatible de forma nativa amb MySQL/MariaDB, PostgreSQL i SQLite. Incorpora al seu nucli funcionalitats crítiques com tipus de continguts personalitzables, motor de vistes dinàmiques (Views), gestió multilingüe nativa, sistema de fitxers privats aïllat i fluxos de treball amb moderació editorial. Disposa d'un equip especialitzat en ciberseguretat (Drupal Security Team) que audita i coordina alertes de seguretat de forma preventiva.
* **Joomla:** Se situa en un terme intermedi entre l'accessibilitat de WordPress i la complexitat estructural de Drupal. Programat en PHP amb suport per a MySQL/MariaDB i PostgreSQL, ofereix de sèrie un model de control d'accés (ACL) molt flexible i suport multilingüe. Tanmateix, la seva comunitat d'usuaris i desenvolupadors és més reduïda, oferint un catàleg d'extensions i una documentació tècnica menys exhaustiva que els seus competidors.

Els tres gestors es distribueixen sota llicències lliures compatibles amb On-Premise (GNU GPL v2 o posterior).

#### 2.2 Taula comparativa

| Criteri | WordPress | Drupal | Joomla |
| :--- | :--- | :--- | :--- |
| **Llenguatge de programació** | PHP | PHP (orientat a objectes, components Symfony) | PHP (patró MVC propi) |
| **Base de dades** | MySQL / MariaDB | MySQL / MariaDB, PostgreSQL, SQLite | MySQL / MariaDB, PostgreSQL |
| **Comunitat i ecosistema** | Molt gran; molts plugins i temes, qualitat desigual | Gran i professional; menys mòduls però més curats | Mitjana; estable però amb menys extensions |
| **Llicència de programari** | GPL v2 o posterior | GPL v2 o posterior | GPL v2 o posterior |
| **Model de seguretat** | Nucli segur, però alt risc en plugins de tercers; rols bàsics | Equip de seguretat dedicat; permisos fins per rol i camp | ACL integrada i potent; ecosistema més petit |
| **Facilitat de personalització** | Molt fàcil per a webs simples; complex per a permisos avançats | Molt flexible (camps, vistes, fluxos al nucli); corba alta | Bona amb extensions; arquitectura menys modular |
| **Adequació a BCN SecureLegal** | Mitjana | **Alta (Guanyador)** | Mitjana |

#### 2.3 Selecció i justificació
**Decisió final: CMS seleccionat per al projecte: Drupal.**

Atesa la condició de despatx jurídic que custòdia expedients de compliment normatiu sota el secret professional i el RGPD, la integritat, el control d'accessos granular i la seguretat pesen molt més que la rapidesa de muntatge inicial. Drupal cobreix de forma nativa les quatre necessitats establertes:

| Necessitat (Ex. 1.4) | Com la cobreix Drupal |
| :--- | :--- |
| **Blog jurídic i notícies** | Tipus de contingut «Article», sistema de taxonomies per classificar resolucions de l'AEPD/jurisprudència, moderació de continguts i publicació programada. |
| **Catàleg de serveis** | Tipus de contingut personalitzat «Servei» amb camps estructurats (abast, preu, lliurables), maquetació mitjançant vistes dinàmiques (Views) i formularis integrats. |
| **Intranet corporativa** | Aïllament per als usuaris autenticats amb permisos granulars segons el rol assignat (advocat sènior, auditor tècnic, personal d'administració). |
| **Àrea de clients protegida** | Sistema natiu de fitxers privats (*private file system*), gestionat fora del DocumentRoot del servidor web, que impedeix la descàrrega directa per URL i valida permisos per sessió. |

**Avantatges addicionals determinants:**
* **Multillenguatge al nucli:** suport multilingüe natiu (català, castellà, anglès) integrat al nucli, essencial per a assessorament d'empreses de la Unió Europea.
* **Flux de moderació editorial:** flux editorial (Draft -> Review -> Published) que garanteix que cap document ni article surti a la llum sense el vistiplau previ d'un soci del despatx.
* **Arquitectura de persistència dockeritzada:** compatibilitat robusta amb MariaDB i PostgreSQL mitjançant contenidors independents de Docker Compose, suportant persistència mitjançant volums locals dedicats i memòria cau eficient per al desplegament de l'exercici 3.

**Per què s'han descartat les altres alternatives:**
* **WordPress:** tot i la rapidesa de desenvolupament, la seva arquitectura requereix una cadena de múltiples plugins de tercers per aïllar fitxers privats i establir permisos fins, la qual cosa introdueix un risc inacceptable de bretxes de seguretat.
* **Joomla:** disposa d'una bona gestió d'usuaris (ACL), però compta amb una comunitat de suport més reduïda i un ecosistema de desenvolupament amb menor flexibilitat per a tipus de contingut dinàmics que Drupal.

**Inconvenients assumits per l'equip:**
* Major temps d'aprenentatge i configuració en comparació amb solucions convencionals.
* Requisit d'administració avançada per línia d'ordres mitjançant Docker Compose i gestor de dependències Composer.

#### 2.4 Extensibilitat i plugin propi
Per donar resposta al requisit de confidencialitat i traçabilitat del RGPD, es dissenya un mòdul propi d'extensió per a Drupal.

* **Proposta del mòdul:** «Traçabilitat i caducitat d'accés a documents d'auditoria» (nom tècnic de la màquina: `bsl_client_docs`).
* **Funcionalitat:** Cada vegada que un client o treballador accedeix o descarrega un informe tècnic d'auditoria de l'àrea privada, el mòdul intercepta la petició, enregistra en una taula de log la identitat de l'usuari, la marca de temps (*timestamp*) i la seva adreça IP, i comprova que l'enllaç temporal no hagi superat el període de vigència autoritzat (p. ex., 30 dies).
* **Ubicació en el sistema:** `modules/custom/bsl_client_docs/`. Dins de l'estructura de Drupal i del contenidor Docker, el codi propi es desplega a `modules/custom/bsl_client_docs/` (on els mòduls de la comunitat van a `modules/contrib/`). En l'entorn de desenvolupament (Exercici 3.2), aquesta carpeta es munta mitjançant un bind mount cap a la màquina amfitriona per permetre la modificació de codi en calent.
* **Valor afegit per a BCN SecureLegal:** Aporta evidències tècniques pròpies i irrefutables de traçabilitat exigides pel RGPD i la directiva NIS2, minimitza el risc d'exfiltració d'informació i constitueix un element de diferenciació comercial davant dels clients corporatius.

---

### Exercici 3: Documentació Tècnica i Desplegament (16 punts)

#### 3.1 Fitxa tècnica del CMS

##### A. Dades generals del sistema
| Camp | Valor |
| :--- | :--- |
| **Plataforma CMS** | Drupal 10.6.19 (versió real identificada a les captures de la instal·lació) |
| **Llicència de programari** | GNU General Public License v2.0 o posterior (GPL-2.0+), 100% compatible On-Premise |
| **Llenguatge i framework** | PHP (orientat a objectes, arquitectura basada en components de Symfony) |
| **Servidor web** | Apache 2.4.69 (Debian) integrat a la imatge oficial de contenidor `drupal:10-apache` |
| **Motor de base de dades** | MariaDB 10.11 (imatge oficial `mariadb:10.11`) |
| **Versió de PHP** | PHP 8.3 (versió de la imatge `drupal:10-apache`; requerit mínim PHP 8.1; s'ha de verificar la sortida exacta de consola amb l'ordre `php -v`) |

##### B. Paràmetres crítics de configuració
| Paràmetre | On es defineix | Propòsit tècnic |
| :--- | :--- | :--- |
| `$databases` | `sites/default/settings.php` | Credencials de connexió SQL (host, base, usuari, contrasenya). A l'entorn Docker Compose el host és el nom del servei `db` assignat pel DNS intern. |
| `hash_salt` | `sites/default/settings.php` | Cadena criptogràfica d'alta entropia utilitzada per generar tokens CSRF, enllaços d'un sol ús i hashing intern de seguretat. |
| `trusted_host_patterns` | `sites/default/settings.php` | Llista blanca d'expressions regulars per als dominis admesos (p. ex. `^localhost$`, `^bsl\.local$`). Bloqueja atacs d'enverinament de capçalera Host. |
| `file_public_path` i `file_private_path` | `sites/default/settings.php` | Ruta de fitxers públics (`sites/default/files`) i carpeta privada d'auditories fora de l'arrel web (`/var/www/private`). |
| `config_sync_directory` | `sites/default/settings.php` | Directori on s'exporta i importa l'estructura del lloc en fitxers YAML, facilitant el traspàs entre entorns. |
| `reverse_proxy` i `reverse_proxy_addresses` | `sites/default/settings.php` | Configuració perquè Drupal reconegui la capçalera `X-Forwarded-For` quan es col·loca un proxy invers davant del contenidor. |
| `twig.config` (debug i cache) | `sites/default/services.yml` | Control del motor de plantilles Twig. A l'entorn de producció la memòria cau ha d'estar activada i el debug desactivat. |
| `error_level` | `sites/default/settings.php` | Nivell de depuració d'errors. En producció s'estableix a `hide` per evitar filtrar detalls d'infraestructura als visitants. |
| `memory_limit` i `upload_max_filesize` | `php.ini` | Límit de memòria per procés (256M) i mida màxima de fitxer adjunt (64M) per garantir la càrrega fluida d'informes pesats. |

##### C. Estructura de directoris i permisos de seguretat
A la imatge oficial de contenidor, l'arrel de servei és `/var/www/html`:

| Ruta del directori | Contingut allotjat | Propietari i permisos recomanats |
| :--- | :--- | :--- |
| `core/`, `vendor/` | Nucli de Drupal i dependències externes gestionades per Composer | Propietari root/deployer; permisos 755 (directoris) i 644 (fitxers). Només lectura per a `www-data`. |
| `modules/contrib/`, `themes/contrib/` | Mòduls i temes descarregats de la comunitat Drupal | Només lectura per a `www-data` (755/644). |
| `modules/custom/`, `themes/custom/` | Codi personalitzat propi de BCN SecureLegal (`bsl_client_docs`) | Permisos 755/644. Muntat amb bind mount en desenvolupament. |
| `sites/default/files/` | Pujades públiques gestionades pels usuaris del web | Propietari `www-data:www-data` amb permisos d'escriptura (775). Allotjat al volum persistent `drupal_sites`. |
| Carpeta privada (`/var/www/private`) | Documents confidencials d'auditoria de l'àrea de clients | Propietari `www-data`, permisos 750, fora de l'arrel web per impedir accés HTTP directe. |
| `sites/default/settings.php` | Configuració principal i claus secretes de connexió SQL | Permisos de només lectura estricta 444; carpeta contenidora `sites/default` protegida en 555. |

##### D. Recomanacions de seguretat per a MariaDB
* Ús de codificació `utf8mb4` i intercalació `utf8mb4_unicode_ci` amb motor transaccional InnoDB.
* Creació d'un compte dedicat amb permisos exclusius sobre la base de dades `bsl_drupal`; mai emprar el compte `root` per a l'aplicació web.
* **Aïllament de xarxa:** El servei MariaDB opera connectat a la xarxa interna de tipus `bridge` de Docker, de manera que el port 3306 no s'exposa al sistema host ni a xarxes externes, quedant accessible de forma privada i exclusiva per al contenidor `bsl-drupal`.

##### E. Estructura del mòdul personalitzat: Mòdul de demostració vs. Estructura completa prevista
* **1. Mòdul mínim de demostració (Entorn Dev):** Per a la presentació de la pràctica i validació del cicle de vida d'edició en calent, es va desplegar una versió reduïda composta exclusivament per `bsl_client_docs.info.yml` (metadades per registrar l'extensió) i `bsl_client_docs.module` (implementant el hook `hook_page_top()` per imprimir missatges informatius com `'bsl_client_docs: versio 1'` i `'versio 2'`).
* **2. Estructura completa prevista del mòdul en producció:** En la fase de desenvolupament final cap a producció, el mòdul s'estructurarà amb fitxers de definició de permisos (`bsl_client_docs.permissions.yml`), serveis d'auditoria (`bsl_client_docs.services.yml`), controladors i plugins d'esdeveniments a la carpeta `src/`, i esquemes de configuració per defecte a `config/install/`.

##### F. Comandes de verificació tècnica de la instal·lació
* `docker compose exec drupal php -v`: Comprova la versió del motor PHP i mòduls carregats al contenidor.
* `docker compose exec drupal ls -ln /var/www/html/sites/default`: Comprova els UIDs i GIDs dels fitxers de configuració com `settings.php`.
* `docker compose exec drupal ls -l /var/www/html/modules`: Verifica l'estructura de directoris de mòduls `contrib` i `custom`.
* `docker compose exec drupal id www-data`: Verifica que l'usuari d'execució d'Apache és `www-data` amb `uid=33` i `gid=33`.

---

#### 3.2 Configuració i funcionament de l'entorn de desenvolupament amb Docker Compose

##### A. Serveis del Docker Compose
El fitxer `docker-compose.yml` defineix dos serveis interconnectats:
* **Servei db:** Contenidor de base de dades anomenat `bsl-db` basat en la imatge oficial `mariadb:10.11`. Gestiona l'emmagatzematge de taules mitjançant el volum persistent `db_data` muntat a `/var/lib/mysql`. Defineix les credencials administratives i de l'aplicació mitjançant variables d'entorn.
* **Servei drupal:** Contenidor web anomenat `bsl-drupal` basat en la imatge oficial `drupal:10-apache`. Exposa el port 80 del contenidor cap al port 8080 del host amfitrió, munta el volum persistent `drupal_sites` i estableix un bind mount per al codi del mòdul propi.

<p align="center">
  <img src="project_images/docker_compose_nano.png" alt="Fitxer docker-compose.yml" width="480" />
  <br/><em>Figura 2: Fitxer docker-compose.yml definit per als serveis db i drupal amb healthcheck i volums.</em>
</p>

##### B. Mecanisme de comprovació d'estat (Healthcheck)
Un *healthcheck* és una instrucció que permet al dimoni de Docker comprovar de forma periòdica si el servei que s'executa a l'interior d'un contenidor està realment llest per atendre peticions (*healthy*) o si encara està en procés d'arrencada (*starting/unhealthy*).

Al servei `db`, el healthcheck executa la comanda `test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]` amb un interval de 10 segons, temps d'espera (*timeout*) de 5 segons i fins a 10 intents. Això garanteix que MariaDB no només ha iniciat el procés del sistema, sinó que el motor transaccional InnoDB està completament inicialitzat i admet consultes SQL.

##### C. Control de dependències amb depends_on
La directiva `depends_on` defineix l'ordre d'arrencada i inicialització dels serveis. Per defecte, un `depends_on` bàsic només espera que el contenidor depenent s'hagi llançat, la qual cosa provoca errors greus quan Drupal intenta connectar-se a una base de dades que encara està creant els seus esquemes interns.

Al projecte s'utilitza la condició avançada `depends_on: db: condition: service_healthy`. Gràcies a això, Docker s'assegura que el contenidor `bsl-drupal` no s'inicia fins que el contenidor `bsl-db` ha superat satisfactòriament el seu healthcheck, evitant de soca-rel els errors de connexió fallida durant l'assistent d'instal·lació.

##### D. Persistència de dades mitjançant el volum drupal_sites
`drupal_sites` és un volum amb nom (*named volume*) gestionat directament pel motor de Docker que s'associa a la ruta `/var/www/html/sites`. Aquest directori conté fitxers crítics com `settings.php` (amb el hash salt i la connexió a la BD) i el directori `sites/default/files` (pujades públiques de logos, imatges i documents). L'ús d'un volum amb nom garanteix que tota la informació configurada persisteix al disc del host fins i tot si els contenidors s'aturen, s'eliminen o es recreen amb noves versions.

##### E. Desenvolupament àgil mitjançant Bind Mount
A diferència dels volums gestionats, el *bind mount* vincula directament una carpeta del sistema de fitxers amfitrió amb una ruta interna del contenidor. Al projecte es comparteix el directori host `./modules/custom/bsl_client_docs` cap a `/var/www/html/modules/custom/bsl_client_docs`.

Aquest mecanisme és clau en desenvolupament perquè permet als programadors crear i modificar fitxers de codi (YAML, PHP, CSS) des del sistema operatiu del host amb els seus editors habituals (nano, VS Code) i que els canvis es reflecteixin de forma instantània a l'entorn Drupal sense haver de fer cap `docker build` ni reiniciar el contenidor.

<p align="center">
  <img src="project_images/module_info_nano.png" alt="Fitxer bsl_client_docs.info.yml" width="520" />
  <br/><em>Figura 3: Fitxer bsl_client_docs.info.yml creat al host per al registre del mòdul mínim de demostració.</em>
</p>

##### F. Comprovació del desplegament de serveis
A la següent captura es valida l'arrencada correcta de la pila de contenidors mitjançant `docker compose ps`, on s'observa el contenidor `bsl-db` en estat `Up (healthy)` i el contenidor `bsl-drupal` en estat `Up` mapejant el port 8080. Així mateix, una petició `curl -sI http://localhost:8080` retorna el codi HTTP `302 Found` generat per Apache 2.4.69 (Debian) redirigint cap a l'assistent d'instal·lació, i `docker compose exec drupal ls -l` confirma que el fitxer del mòdul és visible dins del contenidor amb permisos correctes:

<p align="center">
  <img src="project_images/docker_ps_curl.png" alt="Comprovació docker compose ps i curl" width="540" />
  <br/><em>Figura 4: Verificació de contenidors actius (docker compose ps), resposta HTTP 302 i visibilitat del fitxer al bind mount.</em>
</p>

---

#### 3.3 Evidències i proves

##### A. Prova 1: Desplegament i assistent d'instal·lació de Drupal
Accés al navegador web a la URL `http://localhost:8080/core/install.php`. S'executa l'assistent d'instal·lació de Drupal en català. La captura constata la versió exacta del gestor utilitzat: **Drupal 10.6.19**, arribant al pas 8 de finalització i càrrega de les traduccions de la configuració sense incidències de connexió amb MariaDB:

<p align="center">
  <img src="project_images/drupal_install_10_6_19.png" alt="Instal·lació Drupal 10.6.19" width="480" />
  <br/><em>Figura 5: Assistent d'instal·lació de Drupal 10.6.19 completant el pas de traduccions al català.</em>
</p>

##### B. Prova 2: Accés al CMS instal·lat i publicació de contingut
Un cop finalitzada la instal·lació, s'accedeix al portal web autenticat amb el compte d'administrador `sysadmin_bsl`. Es procedeix a validar la creació i publicació d'un contingut real del Business Case de tipus 'Article' titulat «Nova directiva NIS2: Requisits de ciberseguretat per a pimes i entitats locals», visible a la ruta `localhost:8080/node/1`:

<p align="center">
  <img src="project_images/drupal_article_created_v2.png" alt="Article creat i publicat" width="580" />
  <br/><em>Figura 6: Portal Drupal en funcionament amb l'article NIS2 publicat per l'administrador sysadmin_bsl.</em>
</p>

##### C. Prova 3: Tauler d'administració de continguts
Des de la barra d'administració superior, a la secció **Administració > Contingut** (`localhost:8080/admin/content`), es comprova que el sistema registra l'article en estat «Publicat», amb data de registre 28/10/2026 i associat a l'autor `sysadmin_bsl`:

<p align="center">
  <img src="project_images/drupal_admin_content_list.png" alt="Llistat de continguts a /admin/content" width="580" />
  <br/><em>Figura 7: Panell de gestió de contingut (/admin/content) confirmant l'article publicat.</em>
</p>

##### D. Prova 4: Detecció i activació del mòdul personalitzat (Extend)
A la pestanya **Ampliar / Extend** (`localhost:8080/admin/modules`), Drupal reconeix automàticament el paquet «BCN SecureLegal» i el mòdul «Traçabilitat d'accés a documents» gràcies al bind mount. En activar-lo inicialment, el missatge d'estat del sistema va confirmar la càrrega del mòdul mostrant `'bsl_client_docs: versio 1'`:

<p align="center">
  <img src="project_images/drupal_extend_v1.png" alt="Mòdul instal·lat a Extend" width="500" />
  <br/><em>Figura 8: Mòdul bsl_client_docs instal·lat a Extend mostrant el missatge d'estat 'versio 1'.</em>
</p>

Posteriorment, filtrant pel terme «Traça» a la llista d'extensions, s'observa el mòdul actiu juntament amb el missatge de l'actualització en calent:

<p align="center">
  <img src="project_images/drupal_admin_modules_v2.png" alt="Llista de mòduls amb versio 2" width="580" />
  <br/><em>Figura 9: Mòdul registrat al catàleg d'extensions de Drupal amb la versió 2 activa.</em>
</p>

##### E. Prova 5: Procediment complet d'edició en calent del mòdul sense reinici
Aquesta prova demostra el valor pràctic del bind mount en un entorn de desenvolupament, verificant que una modificació del codi font des del host es reflecteix immediatament a l'aplicació web sense requerir aturar ni recrear el contenidor.

* **Pas 1: Codi inicial del mòdul amb versio 1.** El fitxer `modules/custom/bsl_client_docs/bsl_client_docs.module` implementa el hook `hook_page_top()` amb la cadena `'bsl_client_docs: versio 1'`:
<p align="center">
  <img src="project_images/module_code_v1_nano.png" alt="Codi inicial del mòdul" width="520" />
  <br/><em>Figura 10: Codi PHP inicial del mòdul amb la impressió de 'versio 1'.</em>
</p>

* **Pas 2: Comprovació de l'hora d'arrencada prèvia del contenidor.** Mitjançant l'ordre `docker inspect -f '{{.State.StartedAt}}' bsl-drupal`, s'obté la marca temporal exacta d'inici del contenidor: `2026-10-21T18:21:10.417492923Z`.
<p align="center">
  <img src="project_images/docker_inspect_before_sed.png" alt="docker inspect inicial" width="520" />
  <br/><em>Figura 11: Hora d'arrencada inicial del contenidor bsl-drupal obtinguda amb docker inspect.</em>
</p>

* **Pas 3: Modificació en calent mitjançant l'ordre sed.** Des del terminal de la màquina host s'executa l'ordre de substitució directa:
```bash
sed -i 's/versio 1/versio 2/' modules/custom/bsl_client_docs/bsl_client_docs.module
```
<p align="center">
  <img src="project_images/sed_command.png" alt="Ordre sed" width="520" />
  <br/><em>Figura 12: Execució de l'ordre sed des del host modificant el fitxer del mòdul.</em>
</p>

* **Pas 4: Verificació que el contenidor no s'ha reiniciat.** Immediatament després d'executar `sed`, es torna a consultar l'estat del contenidor amb `docker inspect`. La sortida confirma que `StartedAt` continua essent `2026-10-21T18:21:10.417492923Z` (idèntica a la inicial). El contenidor ha continuat funcionant ininterrompudament:
<p align="center">
  <img src="project_images/docker_inspect_after_sed.png" alt="docker inspect posterior" width="520" />
  <br/><em>Figura 13: docker inspect posterior que demostra que el contenidor NO s'ha reiniciat.</em>
</p>

* **Pas 5: Evidència del canvi reflectit al navegador.** En recarregar qualsevol pàgina del portal Drupal (com s'observa clarament a la Figura 6 i a la Figura 7), el missatge superior de l'aplicació mostra ara: `'bsl_client_docs: versio 2'`.
* **Explicació tècnica:** En una arquitectura Apache/PHP amb bind mount, l'intèrpret de PHP llegeix el fitxer del sistema de fitxers compartit a cada petició HTTP. No cal recompilar ni reiniciar serveis de sistema. En cas que el canvi afecti el registre de hooks, la neteja de la memòria cau interna de Drupal (des de Configuració > Rendiment o mitjançant l'ordre Drush `cr`) permet a Drupal regenerar el seu mapa de plugins conservant la instància en execució.

##### F. Prova 6: Anàlisi d'incidències tècniques i logs del contenidor
Durant la revisió dels logs de l'aplicació mitjançant `docker compose logs drupal --tail=20`, es van detectar missatges repetits d'avís de PHP:

<p align="center">
  <img src="project_images/docker_logs_assert_active.png" alt="Logs de PHP amb avís d'assert.active" width="540" />
  <br/><em>Figura 14: Logs del contenidor mostrant avisos de deprecació d'assert.active a PHP 8.3.</em>
</p>

* **Diagnòstic del missatge:** `Deprecated: PHP Request Shutdown: assert.active INI setting is deprecated in Unknown on line 0`.
* **Causa tècnica:** A partir de la versió PHP 8.3, la directiva `assert.active` de `php.ini` ha quedat declarada obsoleta (*deprecated*) en favor de `zend.assertions`. La imatge oficial de Docker utilitzada incorpora PHP 8.3 sobre Debian, i el nucli de Drupal o la configuració per defecte manté referències de tancament a aquesta directiva.
* **Impacte real:** Es tracta d'un avís de nivell `E_DEPRECATED` que no constitueix un error crític ni atura l'execució del CMS, ni impedeix operacions de base de dades ni càrrega de pàgines web.
* **Mesura correctiva:** A l'entorn de producció, aquest tipus d'avisos es neutralitzen establint `error_reporting = E_ALL & ~E_DEPRECATED` al fitxer `php.ini` del contenidor, o assegurant el paràmetre `system.logging error_level` en `'hide'` al fitxer `settings.php` de Drupal.

---

#### 3.4 Nota de consulta al professorat (Estat dels apartats 3.3 i 3.4)

> [!IMPORTANT] Confirmació acadèmica: Exempció dels apartats 3.3 i 3.4
> D'acord amb les instruccions confirmades pel professorat, els apartats 3.3 («Alta disponibilitat amb Docker Compose») i 3.4 («Arquitectura distribuïda multi-servidor amb NFS») no s'havien de realitzar, quedant definitivament exempts d'avaluació.
>
> En conseqüència, el projecte ha consolidat i validat la totalitat dels requeriments tècnics sobre l'arquitectura conteneritzada autònoma i robusta On-Premise mitjançant Docker Compose (desenvolupada íntegrament a l'apartat 3.2 i provada a l'apartat 3.3).

---

### Exercici 4: Presentació a Client (6 punts)

L'Exercici 4 desenvolupa les habilitats de gestió de projectes, planificació executiva i comunicació professional. Comprèn els següents lliurables desenvolupats en documents i formats independents:

* **A. Memòria Tècnica del Projecte (Document Independent):** Elaborada en un document complet i independent (`Memoria_Tecnica_Exercici_4_BCN_SecureLegal.docx` / `.md`) que detalla: descripció de l'empresa BCN SecureLegal S.L., objectius de la implantació, requeriments tècnics On-Premise (hardware, software, xarxa, persistència, seguretat), estratègies d'implantació en 6 fases, taula exhaustiva de tasques i hores (72 hores totals d'equip basades en `AP03_reparto_v2.xlsx`), diagrama de Gantt temporalitzat en 16 dies hàbils, repartiment ponderat de càrregues entre Emilia, Didac i Aleix, i estratègia de seguiment àgil mitjançant tauler Kanban a GitHub Projects / Trello.
* **B. Presentació Executiva (PowerPoint de 6 Diapositives):** Presentació en format PPTX (`Presentacio_Projecte_BCN_SecureLegal.pptx`) d'exactament 6 diapositives, amb un disseny executiu net, sintètic i visual, que sintetitza els punts clau de la memòria per a la reunió amb el client.
* **C. Simulació de Reunió amb el Client (Vídeo Meet de 15 minuts):** Guions tècnics de 5 minuts per a cadascun dels tres integrants (Emilia: Business Case i gestió; Didac: Infraestructura Docker i xarxes; Aleix: Drupal, mòdul propi i QA), garantint una exposició fluida i sense solapaments.
* **D. Presentació a Classe:** Guió de defensa oral i preparació de preguntes clau davant el tribunal docent i companys.
