# AlphaBackend — Taller 2 (Spring Data JPA)

Backend de finanzas personales Icesi. Spring Boot 3.5 + Spring Data JPA/Hibernate + H2, Java 21.

## Qué incluye (según el enunciado)

| # | Requisito | Dónde está |
|---|---|---|
| 1 | Entidades JPA y relaciones (13 entidades + tablas de unión `user_roles`, `role_permissions`) | `src/main/java/com/icesi/alphabackend/<modulo>/` |
| 2 | Pruebas unitarias JUnit 5 + Mockito | `src/test/java/.../{user,role,permission}/*ServiceTest.java` y `PersistenceIntegrationTest` |
| 3 | Esquema y datos iniciales | `src/main/resources/db/schema.sql` y `db/data.sql` (se ejecutan en cada arranque, son idempotentes) |
| 4 | Repositorios de todas las entidades; servicios solo de Usuarios, Roles y Permisos | `*Repository.java`, `UserService`, `RoleService`, `PermissionService` |
| 5 | JaCoCo (reporte + verificación 100 % de líneas y ramas en los `*Service`) | `pom.xml` |

Reglas de negocio de los servicios:

- No existen usuarios sin rol: `UserService.create` exige al menos un rol y `removeRole` no deja quitar el último.
- No existen roles sin permisos: `RoleService.create` exige al menos un permiso y `removePermission` no deja quitar el último.
- No se puede borrar un rol asignado a usuarios ni un permiso asignado a roles.
- Correo institucional `@u.icesi.edu.co` único y contraseña de mínimo 8 caracteres (se guarda con BCrypt).

## Requisitos

- JDK 21 o superior
- Maven 3.9+

## Ejecutar

```bash
mvn spring-boot:run
```

Crea `data/alphabackend.mv.db` (H2 embebido, archivo local), ejecuta `schema.sql` y `data.sql` y deja la aplicación lista. Al no haber capa web, el proceso arranca y termina; los servicios se consumen por inyección.

Usuarios semilla (contraseña `Password123`): `admin@u.icesi.edu.co` (ADMIN), `ana.torres@u.icesi.edu.co` y `carlos.gomez@u.icesi.edu.co` (USER).

Para reiniciar la base de datos: borrar `data/alphabackend.mv.db`. Los `MERGE INTO` de `data.sql` son idempotentes: correr la app varias veces sobre el mismo archivo no duplica datos.

## Probar y ver cobertura

```bash
mvn verify
```

Ejecuta las pruebas, genera el reporte JaCoCo en `target/site/jacoco/index.html` y falla el build si algún `*Service` baja del 100 % de líneas o ramas.

Las pruebas de integración usan una base separada (`target/test-alphabackend.mv.db`, perfil `test`).

## Modelo de datos

Ver el repositorio de diseño `AlphaBackend` (`docs/data-model.md`).

## Despliegue en IAsLab

```bash
mvn -DskipTests package
java -jar target/alphabackend-0.0.1-SNAPSHOT.jar
```
