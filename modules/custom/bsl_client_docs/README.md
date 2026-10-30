# bsl_client_docs

Mòdul propi de BCN SecureLegal S.L. per a la traçabilitat de l'accés als documents
d'auditoria de l'àrea privada.

> **Avís:** aquesta és la **versió de demostració** desplegada al lliurament AP03.
> Serveix per validar el cicle d'edició en calent amb *bind mount*; **no** implementa
> encara el registre d'accessos ni la caducitat d'enllaços. No s'ha de desplegar a
> producció en aquest estat.

## Abast actual

| Fitxer | Funció |
| :--- | :--- |
| `bsl_client_docs.info.yml` | Metadades del mòdul (nom, paquet, compatibilitat) |
| `bsl_client_docs.module` | `hook_page_top()`: mostra `bsl_client_docs: versio N` |

## Estructura prevista per a producció

```text
bsl_client_docs/
├── bsl_client_docs.info.yml
├── bsl_client_docs.module
├── bsl_client_docs.permissions.yml   # permisos: veure el registre d'accessos
├── bsl_client_docs.services.yml      # servei d'auditoria
├── config/install/                   # configuració per defecte
└── src/
    ├── Controller/                   # descàrrega controlada de documents
    ├── EventSubscriber/              # intercepció de descàrregues
    └── AuditLog.php                  # escriptura del registre
```

## Prova d'edició en calent

```bash
docker inspect -f '{{.State.StartedAt}}' bsl-drupal
sed -i 's/versio 2/versio 3/' modules/custom/bsl_client_docs/bsl_client_docs.module
docker inspect -f '{{.State.StartedAt}}' bsl-drupal   # marca temporal idèntica
```

## Estàndard de codi

El codi segueix l'estàndard Drupal i es valida a la CI amb `phpcs`:

```bash
phpcs --standard=Drupal --extensions=php,module,yml modules/custom
```
