# Document 02: Investigació, Comparativa i Selecció del CMS

## 2.1 Anàlisi de les alternatives avaluades

S'han avaluat tres gestors de continguts de codi obert líders del mercat. Els tres es
distribueixen amb llicència lliure (GPLv2 o posterior), condició indispensable per al
requisit On-Premise.

### WordPress

* **Arquitectura:** PHP amb base de dades MySQL/MariaDB.
* **Punts forts:** ecosistema molt extens de plantilles i connectors; interfície
  d'edició de blocs accessible per a usuaris no tècnics.
* **Inconvenients:** el model de rols natiu és molt bàsic. Per aïllar fitxers privats
  i establir permisos fins cal encadenar múltiples connectors de tercers, la qual cosa
  introdueix un risc inacceptable de vulnerabilitats i incidents de seguretat.

### Drupal

* **Arquitectura:** PHP sota patrons orientats a objectes i components del framework
  Symfony. Suport natiu per a MariaDB/MySQL, PostgreSQL i SQLite.
* **Punts forts:** sistema natiu de fitxers privats aïllats fora del *DocumentRoot*,
  gestió granular de permisos per rol i camp, motor de vistes dinàmiques (Views),
  multillenguatge integrat al nucli i equip de seguretat dedicat (*Drupal Security
  Team*).
* **Inconvenients:** corba d'aprenentatge més elevada i requisit d'administració
  avançada per consola (Composer, Drush).

### Joomla

* **Arquitectura:** PHP sota patró MVC propi, compatible amb MariaDB/MySQL i
  PostgreSQL.
* **Punts forts:** sistema de control d'accés (ACL) flexible integrat de sèrie i
  suport multilingüe natiu.
* **Inconvenients:** comunitat d'usuaris i desenvolupadors més reduïda; ecosistema
  d'extensions i documentació tècnica menys exhaustiva que Drupal.

## 2.2 Taula comparativa de criteris

| Criteri | WordPress | Drupal | Joomla |
| :--- | :--- | :--- | :--- |
| **Llenguatge** | PHP | PHP (orientat a objectes, Symfony) | PHP (patró MVC) |
| **Bases de dades** | MySQL / MariaDB | MySQL / MariaDB, PostgreSQL, SQLite | MySQL / MariaDB, PostgreSQL |
| **Comunitat i extensions** | Molt gran; qualitat desigual | Gran i corporativa; mòduls molt curats | Mitjana; estable però menor |
| **Llicència** | GPLv2 o posterior | GPLv2 o posterior | GPLv2 o posterior |
| **Seguretat** | Risc alt en connectors externs | Equip dedicat; permisos granulars al nucli | ACL integrada; ecosistema reduït |
| **Personalització** | Fàcil per a webs senzilles; complex per a permisos | Molt flexible al nucli (camps, vistes, fluxos) | Bona amb extensions |
| **Adequació a BCN SecureLegal** | Mitjana | **Alta (seleccionat)** | Mitjana |

## 2.3 Justificació de la tria de Drupal

Drupal és la plataforma seleccionada perquè cobreix de manera nativa i robusta les
quatre necessitats establertes sense dependre de connectors de tercers:

1. **Blog jurídic.** Gestió d'articles amb taxonomies per classificar resolucions de
   l'AEPD i flux editorial de moderació (Esborrany → Revisió → Publicat).
2. **Catàleg de serveis.** Tipus de contingut estructurat amb camps personalitzats i
   consultes parametritzades amb Views.
3. **Intranet corporativa.** Restricció d'accés per rol d'usuari.
4. **Àrea de clients privada.** Sistema de fitxers privats gestionat a nivell de
   nucli, que impedeix l'accés directe per URL i valida els permisos de sessió.

### 2.3.1 Matriu de rols i permisos del CMS

Els permisos granulars només són una garantia si es concreten en una matriu. Aquesta
és la proposta implementada a l'entorn de proves:

| Permís | `anonim` | `client` | `advocat` | `auditor` | `editor` | `sysadmin` |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Veure blog i catàleg públic | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| Accedir a la intranet | — | — | ✔ | ✔ | ✔ | ✔ |
| Crear/editar articles | — | — | ✔ | — | ✔ | ✔ |
| Revisar i publicar articles | — | — | — | — | ✔ | ✔ |
| Pujar documents a l'àrea privada | — | — | ✔ | — | — | ✔ |
| Descarregar documents propis | — | ✔ | ✔ | ✔ | — | ✔ |
| Descarregar documents de qualsevol client | — | — | — | ✔ | — | ✔ |
| Gestionar usuaris i rols | — | — | — | — | — | ✔ |
| Consultar el registre d'accessos | — | — | — | ✔ | — | ✔ |

> **Principi de mínim privilegi:** cap rol funcional té permisos d'administració, i el
> rol `client` només veu els documents associats al seu identificador.

### 2.3.2 Registre de decisió arquitectònica (ADR)

La decisió queda formalitzada a [`adr/0001-seleccio-cms.md`](adr/0001-seleccio-cms.md),
amb el context, les alternatives descartades, la decisió i les conseqüències assumides.
Aquest format converteix la comparativa en un document d'enginyeria auditable.

## 2.4 Mòdul personalitzat: `bsl_client_docs`

Per satisfer els requisits de traçabilitat i compliment del RGPD i de la Directiva
NIS2, es dissenya el mòdul `bsl_client_docs`:

* **Ubicació oficial:** `modules/custom/bsl_client_docs/` (el codi de la comunitat viu
  a `modules/contrib/`).
* **Objectiu funcional (abast complet):** interceptar les descàrregues d'informes
  confidencials de l'àrea privada, enregistrar en una taula de log la identitat de
  l'usuari, l'adreça IP i la marca temporal, i verificar la vigència temporal de
  l'enllaç autoritzat (per exemple, 30 dies).
* **Abast implementat en aquest lliurament:** versió mínima de demostració formada per
  `bsl_client_docs.info.yml` i `bsl_client_docs.module`, que implementa
  `hook_page_top()` per validar el cicle d'edició en calent mitjançant *bind mount*.
* **Estructura prevista per a producció:** `bsl_client_docs.permissions.yml`,
  `bsl_client_docs.services.yml`, controladors i *event subscribers* a `src/`, i
  esquemes de configuració per defecte a `config/install/`.
* **Limitació declarada:** la traçabilitat real de descàrregues és a la fase 2 del
  roadmap (vegeu `README.md`), no en aquest lliurament.

### 2.4.1 Valor afegit per al client

El mòdul aporta tres beneficis concrets:

1. **Prova documental:** genera evidències tècniques d'accés exigibles en una auditoria
   (art. 5.2 i art. 32 RGPD).
2. **Reducció del risc d'exfiltració:** la caducitat dels enllaços limita la finestra
   d'exposició d'un document compartit.
3. **Diferenciació comercial:** el client pot rebre un extracte del registre d'accessos
   com a part del servei.
