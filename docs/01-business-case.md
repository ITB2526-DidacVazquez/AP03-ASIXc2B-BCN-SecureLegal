# Document 01: Business Case de BCN SecureLegal S.L.

## 1.1 Identitat i sector

| Camp | Detall |
| :--- | :--- |
| **Nom comercial** | BCN SecureLegal S.L. |
| **Sector** | Serveis jurídics especialitzats en dret tecnològic, protecció de dades de caràcter personal (RGPD, LOPDGDD), ciberseguretat i compliment de normatives europees (Directiva NIS2). |
| **Missió** | Proporcionar assessorament juridicotècnic i auditories de compliment de seguretat amb el màxim rigor, garantint la confidencialitat absoluta dels expedients gestionats. |
| **Forma jurídica** | Societat limitada (S.L.) |

## 1.2 Ubicació i abast

* **Seu central:** Barcelona, districte tecnològic 22@ (les Glòries).
* **Delegació comercial:** Madrid.
* **Abast geogràfic:** operativa principal a Catalunya i la resta d'Espanya, amb
  capacitat d'assessorament transfronterer a empreses i entitats de la Unió Europea
  subjectes a la normativa comunitària de privacitat i ciberseguretat.

## 1.3 Model de negoci i perfil de client

* **Model operatiu:** B2B (*Business to Business*).
* **Cartera de serveis:**
  * Consultoria i auditories de compliment del RGPD i de la Directiva NIS2.
  * Hores d'assessorament jurídic especialitzat en ciberincidents.
  * Delegat de Protecció de Dades extern (*DPO as a Service*).
  * Plans de formació en privacitat i seguretat de la informació.
* **Clients tipus:** hospitals i entitats sanitàries privades, petites i mitjanes
  empreses industrials i tecnològiques, cooperatives, entitats financeres i
  administracions locals.
* **Equip humà:** 25 professionals altament qualificats (advocats TIC, auditors de
  sistemes, consultors de compliment i personal d'administració).

## 1.4 Necessitats estratègiques de comunicació

1. **Blog jurídic i portal d'actualitat.** Espai de divulgació d'anàlisis de
   sentències, novetats normatives i resolucions sancionadores de l'AEPD, per generar
   autoritat de mercat i captar nous clients corporatius.
2. **Catàleg de serveis.** Fitxes clares dels serveis jurídics i d'auditoria oferts,
   amb tarifes orientatives i formularis segurs de sol·licitud de pressupost.
3. **Intranet corporativa.** Plataforma d'accés protegit per al personal de l'empresa
   per compartir plantilles de contractes, protocols d'actuació interna i circulars
   legals.
4. **Àrea de clients privada i xifrada.** Repositori protegit per al lliurament
   confidencial d'informes d'auditoria, certificats i dictàmens legals, amb control
   d'accés estricte.

### 1.4.1 Contingut legal obligatori (requisit afegit)

Com a despatx que assessora en matèria de protecció de dades, el portal ha d'incloure
des del primer dia:

* Avís legal i condicions d'ús.
* Política de privacitat i política de galetes (*cookies*).
* Formulari de contacte amb base jurídica declarada i informació bàsica de protecció
  de dades (art. 13 RGPD).
* Registre d'activitats de tractament (RAT) intern, versionat a la intranet.

Aquests elements formen part del catàleg de continguts i es tracten com a tipus de
contingut propis de Drupal, no com a pàgines improvisades.

## 1.5 Requeriment clau: desplegament On-Premise

Atesa la naturalesa altament sensible de les dades tractades, el secret professional
dels lletrats i les exigències del RGPD, BCN SecureLegal té prohibit allotjar dades de
clients en servidors de núvols públics compartits (*multitenant*) o en plataformes
SaaS gestionades per tercers.

Per aquest motiu, la totalitat de la plataforma s'ha de desplegar **On-Premise**,
allotjada al CPD propi de l'empresa sota contenidors Docker independents, garantint la
custòdia exclusiva i sobirana de la informació.

Aquest requisit condiciona tres decisions del projecte:

| Decisió | Conseqüència |
| :--- | :--- |
| **Llicència** | Només programari de codi obert amb llicència lliure (GPLv2+), sense subscripcions obligatòries a tercers. |
| **Arquitectura** | Desplegament conteneritzat autònom, amb xarxa interna aïllada i persistència local. |
| **Governança** | Configuració versionada a Git: cada canvi sobre el sistema queda auditat (vegeu el document 00). |

> **Traçabilitat normativa:** el requisit On-Premise no és una preferència tècnica, és
> la conseqüència directa de l'art. 32 RGPD (seguretat del tractament), del secret
> professional i de les obligacions de gestió de riscos de la Directiva NIS2.
