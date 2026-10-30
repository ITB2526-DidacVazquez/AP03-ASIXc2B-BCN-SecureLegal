# Document 06: Seguretat, Compliment Normatiu i Gestió de Riscos

## 6.1 Model d'amenaces (abast del projecte)

S'ha aplicat una anàlisi simplificada tipus STRIDE sobre els actius crítics: documents
d'auditoria, credencials de base de dades i configuració del CMS.

| Amenaça | Vector | Actiu afectat | Mitigació implementada | Estat |
| :--- | :--- | :--- | :--- | :--- |
| **Information disclosure** | Descàrrega directa per URL d'un fitxer privat | Informes d'auditoria | `file_private_path` fora de l'arrel web + control de permisos per sessió | Implementat |
| **Elevation of privilege** | Rols amb permisos excessius | Tots | Matriu de rols amb mínim privilegi (document 02, §2.3.1) | Implementat |
| **Tampering** | Canvi de configuració no auditat | Configuració del CMS | Configuració com a codi + revisió creuada a Git | Parcial (fase 2) |
| **Spoofing** | *Host header injection* | Portal | `trusted_host_patterns` a `settings.php` | Configurat |
| **Denial of service** | Esgotament de disc per logs | Servei web | Rotació de logs (`max-size`, `max-file`) | Implementat |
| **Repudiation** | Accés a document sense registre | Informes | Mòdul `bsl_client_docs` (versió completa) | Pendent (fase 2) |
| **Credential theft** | Contrasenyes al repositori | Base de dades | `.env` exclòs, variables obligatòries sense valor per defecte | Implementat |

### 6.1.1 Riscos residuals i tractament

| Risc residual | Probabilitat | Impacte | Tractament |
| :--- | :--- | :--- | :--- |
| Mòdul de traçabilitat en versió demo | Alta | Mitjà | Desenvolupament a la fase 2 abans de producció |
| Absència de TLS al contenidor | Mitjana | Alt | Terminació TLS al proxy invers corporatiu (document 03, §3.1) |
| Contrasenyes en fitxer `.env` al host | Mitjana | Alt | Migració a Docker secrets / vault corporatiu |
| Actualització a 10.6.20 pendent | Mitjana | Mitjà | Finestra de manteniment mensual |
| Sense alta disponibilitat | Baixa | Mitjà | Acceptat: RTO de 4 h cobert amb backup i reinici |

## 6.2 Còpies de seguretat i recuperació

| Paràmetre | Valor proposat |
| :--- | :--- |
| **Freqüència** | Diària (02:00), automàtica via `cron` |
| **Abast** | `mysqldump` de `bsl_drupal` + snapshot del volum `drupal_sites` + `drupal_private` |
| **Retenció** | 7 còpies diàries, 4 setmanals, 12 mensuals |
| **Ubicació** | Disc del CPD + còpia secundària xifrada fora del rack |
| **RTO (temps de recuperació)** | 4 hores |
| **RPO (pèrdua màxima de dades)** | 24 hores |
| **Prova de restauració** | Trimestral, documentada amb data i resultat |

Comandes de referència:

```bash
# Còpia de la base de dades
docker compose exec db sh -c \
  'mysqldump -u root -p"$MARIADB_ROOT_PASSWORD" --single-transaction \
   --routines --triggers "$MARIADB_DATABASE"' > backup_$(date +%F).sql

# Còpia dels fitxers (volums)
docker run --rm -v bcn-securelegal_drupal_sites:/data -v "$PWD":/backup \
  alpine tar czf /backup/sites_$(date +%F).tar.gz -C /data .
```

> **Pendent:** la prova de restauració no s'ha executat en aquest lliurament; és una
> condició prèvia al pas a producció.

## 6.3 Registre d'accessos i protecció de dades

El registre d'accessos a documents conté adreces IP, que són **dades personals**.
Per tant:

| Requisit | Decisió |
| :--- | :--- |
| **Base jurídica** | Interès legítim i obligació legal (art. 6.1.c i 6.1.f RGPD) |
| **Finalitat** | Seguretat de la informació i traçabilitat d'accessos |
| **Minimització** | Només usuari, IP, document, data i resultat de l'accés |
| **Conservació** | 12 mesos, i després agregació anonimitzada |
| **Accés** | Només rols `auditor` i `sysadmin` |
| **Informació** | Recollit al RAT i a la política de privacitat |

## 6.4 Compliment normatiu: punts de control

| Norma | Article / requisit | Com es cobreix |
| :--- | :--- | :--- |
| **RGPD** | Art. 5.2 (responsabilitat proactiva) | Configuració i canvis versionats a Git (document 00) |
| **RGPD** | Art. 32 (seguretat del tractament) | Xarxa aïllada, permisos, xifrat en trànsit, backups |
| **RGPD** | Art. 13 (informació a l'interessat) | Política de privacitat i formularis informats |
| **RGPD** | Art. 30 (registre d'activitats) | RAT versionat a la intranet |
| **LOPDGDD** | Art. 28 (bloqueig de dades) | Retenció definida i anonimització |
| **NIS2** | Gestió de riscos i traçabilitat | Model d'amenaces (§6.1) i registre d'accessos |
| **LSSI-CE** | Art. 10 (informació del prestador) | Avís legal i condicions d'ús |

## 6.5 Contingut legal del portal

Tipus de contingut propis de Drupal, creats a la fase 3:

1. **Avís legal** — identificació del prestador, CIF, domicili i contacte.
2. **Política de privacitat** — finalitats, bases jurídiques, terminis i drets.
3. **Política de galetes** — inventari de galetes i consentiment previ.
4. **Formulari de contacte** — informació bàsica de protecció de dades (art. 13 RGPD).
5. **Bústia del DPO** — canal per a l'exercici de drets.

## 6.6 Limitacions declarades d'aquest lliurament

1. El mòdul `bsl_client_docs` és una **demostració** del cicle d'edició en calent; no
   implementa encara el registre ni la caducitat d'enllaços.
2. No hi ha pipeline de desplegament automàtic: el `docker compose up -d` és manual.
3. La imatge no inclou Composer ni Drush, cosa que bloqueja `config:import`/`export`.
4. No s'ha executat la prova de restauració de backups.
5. L'alta disponibilitat i l'emmagatzematge NFS queden fora d'abast per indicació del
   professorat.
