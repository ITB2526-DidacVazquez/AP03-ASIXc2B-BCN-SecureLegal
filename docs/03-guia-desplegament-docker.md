# Document 03: Guia de Desplegament amb Docker Compose

## 3.1 Pila de serveis

La infraestructura s'orquestra mitjançant Docker Compose amb dos serveis principals i
tres volums amb nom. Les imatges estan **fixades a versions concretes** per garantir
la reproducibilitat del desplegament.

| Servei | Imatge | Contenidor | Xarxa | Port |
| :--- | :--- | :--- | :--- | :--- |
| `db` | `mariadb:10.11.19` | `bsl-db` | `backend` (interna) | 3306 no publicat |
| `drupal` | `drupal:10.6.19-apache` | `bsl-drupal` | `backend` + `edge` | `127.0.0.1:8080 → 80` |

> **Nota de versió.** Durant la revisió del lliurament s'ha detectat que l'equip de
> Drupal ha publicat la versió 10.6.20 (pedaç de seguretat). La versió 10.6.19 és la
> verificada a les evidències; l'actualització a 10.6.20 és una tasca de manteniment
> planificada (vegeu el roadmap al `README.md`).

### 1. Servei `db` (base de dades)

* **Imatge oficial:** `mariadb:10.11.19` (branca LTS 10.11).
* **Nom del contenidor:** `bsl-db`.
* **Reinici:** `unless-stopped`.
* **Aïllament de xarxa:** connectat només a la xarxa `backend`, definida amb
  `internal: true`. Això vol dir que **no té accés a Internet ni al host** i que el
  port 3306 no s'exposa en cap cas.
* **Codificació:** s'imposa `--character-set-server=utf8mb4` i
  `--collation-server=utf8mb4_unicode_ci` a la comanda d'arrencada, perquè la
  configuració no depengui dels valors per defecte de la imatge.
* **Credencials:** es llegeixen de variables d'entorn obligatòries
  (`${MARIADB_ROOT_PASSWORD:?…}`). Si el fitxer `.env` no les defineix, el
  desplegament **falla en lloc d'arrencar amb una contrasenya coneguda**.
* **Healthcheck configurat:**

  ```yaml
  healthcheck:
    test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
    interval: 10s
    timeout: 5s
    retries: 10
    start_period: 30s
  ```

  El mecanisme comprova que el motor transaccional InnoDB està completament
  inicialitzat abans de marcar el contenidor com a sa (`healthy`). El `start_period`
  evita falsos negatius durant la primera arrencada.

### 2. Servei `drupal` (aplicació web)

* **Imatge oficial:** `drupal:10.6.19-apache` (Apache 2.4.69, PHP 8.3).
* **Nom del contenidor:** `bsl-drupal`.
* **Port publicat:** `127.0.0.1:8080:80`. El servei **no s'exposa a la xarxa externa**:
  només escolta a la interfície local, i el tràfic corporatiu arriba pel proxy invers
  amb TLS 1.3. Això evita publicar HTTP en clar al CPD.
* **Dependència d'arrencada:**

  ```yaml
  depends_on:
    db:
      condition: service_healthy
  ```

  Evita errors d'arrencada assegurant que Drupal no intenta connectar-se a la base de
  dades fins que aquesta ha superat satisfactòriament el healthcheck.
* **Enduriment:** `security_opt: no-new-privileges:true` i rotació de logs
  (`max-size: 10m`, `max-file: 5`) per evitar que el disc del CPD s'ompli.

## 3.2 Persistència i volums

| Volum | Muntat a | Contingut | Motiu |
| :--- | :--- | :--- | :--- |
| `drupal_sites` | `/var/www/html/sites` | `settings.php`, `sites/default/files` | Preserva configuració i pujades públiques |
| `drupal_private` | `/var/www/private` | Informes confidencials d'auditoria | Fora de l'arrel web: sense accés HTTP directe |
| `db_data` | `/var/lib/mysql` | Taules i registres SQL | Persistència de la base de dades |

* **Bind mount de desenvolupament:** `./modules/custom/bsl_client_docs` es munta a
  `/var/www/html/modules/custom/bsl_client_docs`, cosa que permet editar codi en calent
  des del host sense reiniciar el servei.
* **Producció:** el bind mount se substitueix per una imatge construïda amb el codi
  dins (`docker build`), de manera que el contenidor no depèn del sistema de fitxers
  del host.

> **Correcció respecte a la versió anterior d'aquest document:** la carpeta privada
> `/var/www/private` s'esmentava a la documentació però **no estava muntada** al
> `docker-compose.yml`. S'ha afegit el volum `drupal_private` per fer-la efectiva.

## 3.3 Estructura de permisos i seguretat

| Directori / fitxer | Propietari recomanat | Permisos | Descripció |
| :--- | :--- | :--- | :--- |
| `core/`, `vendor/` | `root:root` | `755` directoris, `644` fitxers | Només lectura per a `www-data` |
| `modules/contrib/` | `root:root` | `755` directoris, `644` fitxers | Mòduls descarregats de la comunitat |
| `modules/custom/` | Usuari amfitrió / `www-data` | `755` directoris, `644` fitxers | Codi propi amb bind mount |
| `sites/default/files/` | `www-data:www-data` | `775` | Pujades públiques al volum persistent |
| `/var/www/private` | `www-data:www-data` | `750` | Documents confidencials fora de l'arrel web |
| `sites/default/settings.php` | `root:root` | `444` | Només lectura estricta un cop instal·lat |

> **Correcció important:** a la versió anterior el fitxer `settings.php` constava com a
> propietat de `www-data` amb permisos `444`. Atès que el propietari pot tornar a
> aplicar `chmod`, això no protegeix res: el propi procés web podria fer-se escriptura.
> El propietari ha de ser `root:root`, i a producció cal afegir
> `$settings['config_readonly'] = TRUE;` per bloquejar canvis de configuració des de la
> interfície.

### 3.3.1 Enduriment addicional recomanat per a producció

| Mesura | Directiva / eina |
| :--- | :--- |
| Contenidor sense escriptura | `read_only: true` + `tmpfs: /tmp` |
| Reducció de capacitats | `cap_drop: [ALL]`, `cap_add: [CHOWN, SETUID, SETGID, DAC_OVERRIDE]` |
| Límits de recursos | `deploy.resources.limits` (CPU/RAM) o `mem_limit` |
| Gestió de secrets | Docker secrets o *vault* corporatiu en lloc de `.env` |
| Rotació i retenció de logs | `logging.driver: json-file` amb `max-size`/`max-file` |
| Actualitzacions | Calendari mensual de pedaços + `composer update` auditat |

## 3.4 Comandes de verificació del sistema

```bash
# Versió del motor PHP i mòduls carregats
docker compose exec drupal php -v

# Permisos numèrics i UIDs de configuració
docker compose exec drupal ls -ln /var/www/html/sites/default

# Estructura de mòduls propis i de tercers
docker compose exec drupal ls -l /var/www/html/modules

# Identificació de l'usuari d'execució web
docker compose exec drupal id www-data

# Validació de la sintaxi del compose (també s'executa a la CI)
docker compose config --quiet

# Estat dels contenidors i healthchecks
docker compose ps
```

## 3.5 Posada en marxa

```bash
cp .env.example .env          # 1. definiu contrasenyes reals
docker compose config         # 2. valideu la configuració
docker compose up -d          # 3. aixequueu la pila
docker compose ps             # 4. comproveu que bsl-db és "healthy"
curl -sI http://localhost:8080  # 5. ha de retornar 302 cap a l'instal·lador
```

## 3.6 Eina d'administració: Drush

La imatge oficial `drupal:10.6.19-apache` **no inclou Composer ni Drush**. Per això:

* En aquest lliurament, la neteja de memòria cau es fa des de la interfície
  (*Configuració → Rendiment → Esborra tots els caches*).
* Per a producció es preveu un `Dockerfile` propi que instal·li Composer i Drush, pas
  necessari també per a `config:export` / `config:import` (configuració com a codi).

> **Correcció respecte a la versió anterior:** el `README` proposava
> `docker compose exec drupal vendor/bin/drush cache:rebuild`, una comanda que falla
> perquè el directori `vendor/` no existeix a la imatge oficial.
