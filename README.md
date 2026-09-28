# Mis bases de datos Docker

Repositorio personal para levantar bases de datos locales de estudio sin instalar motores directamente en la computadora.

Cada base es independiente. Entra a la carpeta de la base que necesites y ejecuta Docker Compose desde ahí.

Las versiones están **fijadas** a propósito: al clonar este repositorio en otra computadora se descargará la misma versión y no una actualización inesperada.

## Versiones incluidas

| Base de datos | Versión | Imagen |
|---|---:|---|
| MySQL | 8.4.11 | `mysql:8.4.11` |
| PostgreSQL | 18.6 | `postgres:18.6` |
| MongoDB | 8.0.32 | `mongo:8.0.32` |
| Apache Cassandra | 5.0.9 | `cassandra:5.0.9` |
| Oracle Database Free | 23.26.3 | `gvenzl/oracle-free:23.26.3` |

> MongoDB 8.0 se usa aquí como rama estable/conservadora para cursos. Cassandra 6.0 no se usa porque actualmente sigue siendo una versión alpha. PostgreSQL 19 tampoco se usa porque actualmente está en beta.

---

# Comandos Docker básicos

Ejecuta estos comandos **dentro de la carpeta de la base de datos** que quieras usar, por ejemplo `mysql/` o `oracle/`.

## Levantar en segundo plano

Es el comando normal para estudiar. La terminal queda libre.

```bash
docker compose up -d
```

La primera vez descargará la imagen y creará el volumen de datos automáticamente.

## Levantar viendo los logs

Útil si algo falla durante el arranque.

```bash
docker compose up
```

Para salir sin borrar los datos, presiona `Ctrl + C` y después puedes volver a levantarla normalmente.

## Ver estado

```bash
docker compose ps
```

## Ver logs

```bash
docker compose logs -f
```

Salir de los logs con `Ctrl + C` no apaga la base de datos si fue levantada con `-d`.

## Detener para continuar otro día

Detiene el contenedor y conserva absolutamente todos los datos.

```bash
docker compose stop
```

Para volver a encender el mismo contenedor:

```bash
docker compose start
```

## Bajar el entorno conservando los datos

Elimina el contenedor y la red de Compose, pero **conserva el volumen con la base de datos**.

```bash
docker compose down
```

La próxima vez:

```bash
docker compose up -d
```

Docker reconstruirá el contenedor y reutilizará los mismos datos.

## Borrar completamente la base y empezar desde cero

**Cuidado:** esto elimina también el volumen y todos los datos de esa base.

```bash
docker compose down -v
```

Después, `docker compose up -d` creará una base completamente limpia.

## Reiniciar

```bash
docker compose restart
```

---

# MySQL

**Versión:** 8.4.11  
**Contenedor:** `mysql-personal-8-4-11`  
**Volumen:** `mysql-personal-8-4-11-data`

### Conexión normal

- Host: `localhost`
- Puerto: `3306`
- Base de datos: `mysql_personal`
- Usuario: `user`
- Password: `pass`

### Administrador

- Usuario: `root`
- Password: `root_pass`

`root` se conserva porque es el usuario administrativo real/default de MySQL.

### JDBC

```text
jdbc:mysql://localhost:3306/mysql_personal
```

### Consola dentro del contenedor

```bash
docker exec -it mysql-personal-8-4-11 mysql -uuser -ppass mysql_personal
```

---

# PostgreSQL

**Versión:** 18.6  
**Contenedor:** `postgresql-personal-18-6`  
**Volumen:** `postgresql-personal-18-6-data`

### Conexión

- Host: `localhost`
- Puerto: `5432`
- Base de datos: `postgres_personal`
- Usuario: `user`
- Password: `pass`

El usuario `user` es el propietario/administrador inicial de esta instancia local.

### JDBC

```text
jdbc:postgresql://localhost:5432/postgres_personal
```

### Consola dentro del contenedor

```bash
docker exec -it postgresql-personal-18-6 psql -U user -d postgres_personal
```

---

# MongoDB

**Versión:** 8.0.32  
**Contenedor:** `mongodb-personal-8-0-32`  
**Volumen:** `mongodb-personal-8-0-32-data`

MongoDB se deja deliberadamente como una instalación local simple, **sin autenticación**, equivalente al uso básico típico de cursos y desarrollo local.

### Conexión

- Host: `localhost`
- Puerto: `27017`
- Usuario: no aplica
- Password: no aplica

### MongoDB Compass

```text
mongodb://localhost:27017
```

### URI para una base llamada `mongodb_personal`

MongoDB crea la base realmente cuando guardas el primer dato.

```text
mongodb://localhost:27017/mongodb_personal
```

### Consola dentro del contenedor

```bash
docker exec -it mongodb-personal-8-0-32 mongosh
```

---

# Apache Cassandra

**Versión:** 5.0.9  
**Contenedor:** `cassandra-personal-5-0-9`  
**Volumen:** `cassandra-personal-5-0-9-data`

Cassandra se deja con la autenticación local por defecto de la imagen, sin usuario ni contraseña, para evitar configuración innecesaria durante los cursos.

### Conexión

- Host / Contact Point: `localhost`
- Puerto CQL: `9042`
- Datacenter: `datacenter1`
- Usuario: no aplica
- Password: no aplica

### Java / DataStax Driver

- Contact point: `127.0.0.1:9042`
- Local datacenter: `datacenter1`

El keyspace normalmente lo crearás según el ejercicio del curso.

### Consola CQL

```bash
docker exec -it cassandra-personal-5-0-9 cqlsh
```

> Cassandra puede tardar un poco más que MySQL/PostgreSQL en quedar lista después de arrancar el contenedor.

---

# Oracle Database Free

**Versión:** 23.26.3  
**Contenedor:** `oracle-personal-23-26-3`  
**Volumen:** `oracle-personal-23-26-3-data`

Esta es una instancia de **Oracle Database Free real**, no H2, no un emulador y no una base que solamente imite SQL de Oracle.

### Conexión normal para cursos

- Host: `localhost`
- Puerto: `1521`
- Service name: `FREEPDB1`
- Usuario: `user`
- Password: `pass`

### Administrador Oracle

Oracle trae sus propios usuarios administrativos y se conservan con sus nombres reales:

- Usuario: `SYSTEM`
- Password: `pass_admin`
- Usuario: `SYS`
- Password: `pass_admin`

Para estudiar aplicaciones normalmente usa `user/pass`, no `SYS` ni `SYSTEM`.

### JDBC

```text
jdbc:oracle:thin:@//localhost:1521/FREEPDB1
```

### DataGrip

- Driver: Oracle
- Host: `localhost`
- Port: `1521`
- Authentication: User & Password
- User: `user`
- Password: `pass`
- Connection type: Service name
- Service: `FREEPDB1`

### Consola SQL*Plus dentro del contenedor

```bash
docker exec -it oracle-personal-23-26-3 sqlplus user/pass@FREEPDB1
```

> El primer arranque de Oracle tarda bastante más que las otras bases porque debe inicializar la base. Los siguientes arranques reutilizan el volumen y son más sencillos.

---

# Uso rápido

Si hoy tu curso usa PostgreSQL:

```bash
cd postgresql
docker compose up -d
```

Trabajas normalmente durante el curso y al terminar el día:

```bash
docker compose stop
```

Mañana:

```bash
docker compose start
```

Si terminaste ese curso y quieres eliminar absolutamente todo lo que creó esa base:

```bash
docker compose down -v
```

Con esto no necesitas instalar MySQL, PostgreSQL, MongoDB, Cassandra ni Oracle directamente en Windows. Solo necesitas Docker/Docker Desktop.
