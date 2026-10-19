# NicaMarket API (proyecto base)

API REST de **productos** y **categorías** para la Práctica guiada n.º 9 (Servicios Web, UAM).
Es el punto de partida **sin seguridad** y **sin base de datos**: los datos viven en memoria
y se reinician al detener la aplicación.

- Java 21 · Spring Boot 4.1.1 (Spring Framework 7, Jakarta EE 11) · Maven
- Paquete base: `com.example.factura`

## Ejecutar

```bash
mvn spring-boot:run
```

o abrir el proyecto en IntelliJ IDEA y ejecutar `FacturaApplication`. Servidor en `http://localhost:8080`.

# NicaMarket API - Seguridad con Spring Security y JWT

API REST para la gestión de productos y categorías de Comercial NicaMarket, S. A., desarrollada como parte de la Práctica Guiada N.º 9 de la asignatura Servicios Web de la Universidad Americana (UAM).

El proyecto incorpora autenticación mediante JSON Web Token (JWT) y autorización basada en roles utilizando Spring Security, con el objetivo de controlar el acceso a los recursos y restringir las operaciones de modificación a usuarios autorizados.

## 1. Tecnologías utilizadas

- Java 21
- Spring Boot 4.1.1
- Spring Security
- JWT (JSON Web Token)
- Maven
- IntelliJ IDEA
- Postman

El proyecto utiliza almacenamiento en memoria, por lo que los datos se reinician cuando se detiene la aplicación.

## 2. Funcionalidades

La API permite:

- Consultar, registrar, actualizar y eliminar productos.
- Consultar, registrar, actualizar y eliminar categorías.
- Autenticar usuarios mediante nombre de usuario y contraseña.
- Generar tokens JWT firmados mediante HS256.
- Validar los tokens en las solicitudes protegidas.
- Restringir las operaciones según los roles USER y ADMIN.

Los tokens JWT tienen una duración de 30 minutos.

## 3. Roles y permisos

La aplicación dispone de dos roles:

### USER

Puede consultar productos y categorías, pero no tiene autorización para crear, actualizar o eliminar registros.

### ADMIN

Puede consultar, crear, actualizar y eliminar productos y categorías.

Los usuarios no autenticados solamente pueden acceder al endpoint público y al inicio de sesión.

## 4. Configuración y ejecución

### Requisitos

- JDK 21
- Maven
- IntelliJ IDEA o un IDE compatible
- Postman para las pruebas

### Configuración de JWT

Antes de ejecutar la aplicación, se debe configurar la variable de entorno:

JWT_SECRET_BASE64

Su valor debe ser una clave aleatoria de al menos 32 bytes codificada en Base64.

En IntelliJ IDEA, esta variable se configura desde:

Run > Edit Configurations > FacturaApplication > Environment variables

La clave secreta no debe incluirse en el código fuente ni publicarse en GitHub.

### Ejecutar la aplicación

Abrir el proyecto en IntelliJ IDEA y ejecutar la clase:

FacturaApplication.java

También puede ejecutarse mediante Maven:

mvn spring-boot:run

La API estará disponible en:

http://localhost:8080

## 5. Autenticación JWT

El inicio de sesión se realiza mediante:

POST /api/auth/login

Ejemplo de solicitud:

{
"username": "admin",
"password": "Admin123!"
}

Usuarios de laboratorio:

| Usuario | Contraseña | Rol |
|---------|------------|-----|
| admin | Admin123! | ADMIN |
| operador | Operador123! | USER |

Estas credenciales se utilizan únicamente con fines académicos.

Cuando las credenciales son válidas, la API devuelve un token JWT y el tipo de autenticación Bearer.

Para acceder a los endpoints protegidos, el token debe enviarse mediante el encabezado:

Authorization: Bearer <token>

## 6. Endpoints de la API

### Endpoints públicos

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | /api/publico/saludo | Consultar estado de la API |
| POST | /api/auth/login | Iniciar sesión y obtener JWT |

### Productos

| Método | Ruta | Permisos |
|--------|------|----------|
| GET | /api/productos | USER y ADMIN |
| GET | /api/productos/{id} | USER y ADMIN |
| POST | /api/productos | ADMIN |
| PUT | /api/productos/{id} | ADMIN |
| DELETE | /api/productos/{id} | ADMIN |

### Categorías

| Método | Ruta | Permisos |
|--------|------|----------|
| GET | /api/categorias | USER y ADMIN |
| GET | /api/categorias/{id} | USER y ADMIN |
| POST | /api/categorias | ADMIN |
| PUT | /api/categorias/{id} | ADMIN |
| DELETE | /api/categorias/{id} | ADMIN |

## 7. Ejemplos de solicitudes

### Crear producto

POST /api/productos

{
"nombre": "Aceite vegetal 1 litro",
"precio": 85.00,
"stock": 50,
"categoriaId": 1
}

### Crear categoría

POST /api/categorias

{
"nombre": "Lácteos",
"descripcion": "Leche, queso y productos derivados"
}

Ambas operaciones requieren un token JWT válido con el rol ADMIN.

## 8. Códigos de respuesta HTTP

| Código | Significado |
|--------|-------------|
| 200 OK | Consulta o actualización exitosa |
| 201 Created | Recurso creado correctamente |
| 204 No Content | Recurso eliminado correctamente |
| 400 Bad Request | Datos de solicitud inválidos |
| 401 Unauthorized | Autenticación ausente o inválida |
| 403 Forbidden | Usuario autenticado sin permisos suficientes |
| 404 Not Found | Recurso inexistente |

## 9. Pruebas realizadas con Postman

Se verificó el funcionamiento de la autenticación JWT y las restricciones de acceso mediante diferentes solicitudes HTTP.

Entre las pruebas realizadas se encuentran:

- Inicio de sesión de usuarios ADMIN y USER.
- Consulta de productos con token válido.
- Acceso a productos sin autenticación.
- Intento de creación de productos con USER.
- Creación de productos con ADMIN.
- Consulta de categorías con USER.
- Intentos de creación, actualización y eliminación de categorías con USER.
- Creación, actualización y eliminación de categorías con ADMIN.
- Acceso a categorías sin token.
- Acceso a categorías con un token inválido.

Las pruebas permitieron comprobar las respuestas HTTP 200, 201, 204, 401 y 403.

Las solicitudes se encuentran organizadas en la colección de Postman NicaMarket - JWT.

## 10. Estructura del proyecto

com.example.factura
|
|-- config
|   |-- SecurityConfig.java
|
|-- controllers
|   |-- AuthController.java
|   |-- PublicoController.java
|   |-- ProductoController.java
|   |-- CategoriaController.java
|
|-- dto
|   |-- LoginRequest.java
|   |-- LoginResponse.java
|
|-- services
|   |-- TokenService.java
|   |-- ProductoService.java
|   |-- CategoriaService.java
|
|-- model
|   |-- Producto.java
|   |-- Categoria.java
|
|-- exception
|   |-- RecursoNoEncontradoException.java
|   |-- ApiExceptionHandler.java
|
|-- FacturaApplication.java

## 11. Consideraciones de seguridad

La aplicación utiliza Spring Security para controlar el acceso a los recursos y tokens JWT firmados mediante HS256.

Las contraseñas de laboratorio se procesan mediante BCrypt y los tokens contienen el nombre del usuario, sus roles y un tiempo de expiración.

La autenticación se implementa sin sesiones HTTP mediante la política STATELESS.

La clave secreta JWT se obtiene desde una variable de entorno para evitar almacenarla directamente en el código.

Esta implementación corresponde a una práctica académica. Para un entorno de producción sería necesario fortalecer la gestión de usuarios, credenciales y claves, así como utilizar HTTPS.

## 12. Conclusión

La implementación de Spring Security y JWT permitió incorporar mecanismos de autenticación y autorización en la API REST de NicaMarket, restringiendo el acceso a las operaciones de productos y categorías según el rol del usuario.

Las pruebas realizadas mediante Postman demostraron que los usuarios USER pueden consultar información, mientras que las operaciones de creación, actualización y eliminación están reservadas a los administradores. Asimismo, se comprobó que las solicitudes sin autenticación o con tokens inválidos son rechazadas.

