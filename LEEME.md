# EcoGasto – Backend (Spring Boot 3 + Java 17 + PostgreSQL)

Curso 1ASI0705 Arquitectura de Aplicaciones Web – UPC 2026-20.

## 1. Requisitos
- IntelliJ IDEA (Community o Ultimate). Trae Maven incluido, no hace falta instalarlo.
- JDK 17 o superior (IntelliJ lo puede descargar: *File > Project Structure > SDK > Download JDK*).
- PostgreSQL 16 + pgAdmin 4.

## 2. Crear la base de datos (pgAdmin 4)
1. Clic derecho en *Databases > Create > Database…* → nombre: `ecogasto_db`.
2. Clic derecho en `ecogasto_db` → *Query Tool*.
3. Abrir el archivo `ecogasto_db.sql` (icono de carpeta) y ejecutar con **F5**.
4. Verificar: `SELECT COUNT(*) FROM consumos;` debe devolver 150.
5. Diagrama: clic derecho en `ecogasto_db` → *ERD For Database*.

## 3. Abrir y ejecutar en IntelliJ IDEA
1. *File > Open…* → seleccionar la carpeta `ecogasto-backend` (la que contiene `pom.xml`).
2. Esperar a que IntelliJ descargue las dependencias de Maven (barra inferior).
3. Abrir `src/main/resources/application.properties` y colocar la contraseña de tu PostgreSQL en `spring.datasource.password`.
4. Ejecutar `EcogastoApplication.java` (botón verde ▶).
5. Abrir Swagger: http://localhost:8080/swagger-ui.html

## 4. Probar la API en Swagger
1. `POST /api/auth/login` con `{"username":"admin","password":"EcoGasto2025"}` (o `jdelgado` / `Demo1234`).
2. Copiar el `token` de la respuesta.
3. Botón **Authorize** (candado) → pegar el token → *Authorize*.
4. Ya puedes probar los demás endpoints.

Datos útiles para la sustentación:
- Hogar 1 (`jdelgado`): la lectura de agua de agosto 2026 es anómala → `GET /api/notificaciones/usuario/2` muestra la alerta.
- Probar la detección: `POST /api/consumos` con un valor muy alto genera una notificación nueva.
- `GET /api/admin/reportes/carencias-zona` y `/nivel-consumo` alimentan los gráficos del panel Admin.

## 5. Inteligencia Artificial (HU07)
`GET /api/recomendaciones/personalizada/usuario/{id}` llama a la API de Google Gemini con los consumos reales del usuario.
1. Crear una API key gratuita en https://aistudio.google.com/apikey
2. Pegarla en `ecogasto.ia.api-key` de `application.properties` (o crear la variable de entorno `GEMINI_API_KEY`).
3. Si no hay key o la IA no responde, el sistema devuelve un tip del catálogo (`origen = "CATALOGO"`), así la app nunca se cae.

## 6. Estructura de paquetes (`pe.edu.upc.ecogasto`)
| Paquete | Contenido |
|---|---|
| `entities` | 18 clases @Entity (una por tabla) |
| `dtos` | Objetos que viajan en el JSON, con validaciones |
| `repositories` | Interfaces JpaRepository y consultas SQL nativas de reportes |
| `serviceinterfaces` | Interfaces `I…Service` |
| `serviceimplements` | Lógica de negocio (`…ServiceImplement`) |
| `controllers` | Endpoints REST (EP01–EP63) |
| `securities` | JWT + Spring Security (roles ADMIN y USUARIO) |
| `exceptions` | Manejo global de errores (400, 404, 409) |
| `config` | Configuración de Swagger |

## 7. Nota sobre los tests
`EcogastoApplicationTests` levanta el contexto completo, así que necesita PostgreSQL encendido.
Si solo quieres compilar sin base de datos: *Maven > Lifecycle > package* con “Skip Tests” activado.
