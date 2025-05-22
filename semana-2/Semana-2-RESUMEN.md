**Fase 1: Modelos, Migraciones, y Bases de Datos**

En esta primera fase, se instaló Laravel, creó el esqueleto del proyecto, se definió la estructura y las relaciones de la base de datos. Se crearon migraciones para las tablas `users`, `jobs`, `employers` y `tags`. Se definieron las relaciones entre entidades para asegurar que los datos estuvieran conectados de manera lógica.

- **User**: Se definió el modelo `User` con campos como `first_name`, `last_name`, `email` y `password`. Se configuraron `$fillable` para proteger los datos masivos y `$hidden` para evitar exponer información sensible.
- **Job**: Se creó el modelo `Job` con campos como `title`, `description` y `salary`. Se definió la relación con `Employer`, donde un empleador puede tener múltiples trabajos.
- **Employer**: Se creó el modelo `Employer` con atributos como `name` e `industry`. Se estableció una relación one-to-many con `Job`, indicando que un empleador puede tener varios trabajos asociados.
- **Tag**: Se definió el modelo `Tag` para permitir la clasificación de los trabajos con múltiples etiquetas (many-to-many), proporcionando flexibilidad para buscar y categorizar trabajos según diferentes criterios.

**Fase 2: API y CRUD**

En la segunda fase, se creó la API y las operaciones CRUD. Se implementó el controlador `JobController` para gestionar las acciones relacionadas con los trabajos.

- **Rutas de CRUD**: Se utilizó `Route::apiResource()` para definir las rutas automáticamente para el controlador `JobController`. Esto permitió realizar operaciones CRUD (crear, leer, actualizar y eliminar).

**Fase 3: Autenticación, Protección de Rutas y Seguridad**

En la última fase, se añadió la autenticación y protección de rutas para asegurar un acceso seguro a la API.

- **Autenticación Inicial**: Se creó el controlador `AuthController` con métodos como `register` y `login` para permitir el registro y autenticación de usuarios.
- **Middleware de Autenticación**: Se utilizó Laravel Sanctum para implementar la autenticación mediante tokens. Se aplicó el middleware `auth:sanctum` a las rutas que requerían autenticación, como la de obtener información del usuario autenticado (`/user`) y cerrar sesión (`/logout`). Esto garantizó que solo los usuarios autenticados pudieran interactuar con las rutas protegidas.
- **Pruebas de Protección de Rutas**: Se probaron las rutas protegidas utilizando Postman, verificando que el acceso requería el token de autenticación generado durante el proceso de login, añadiendo el encabezado `Authorization (Bearer <token>)`.
- **Gestión Segura de Tokens**: Durante el proceso de autenticación, se implementó la generación y eliminación de tokens para los usuarios, incluyendo el uso de `createToken()` para el login y `currentAccessToken()->delete()` para el logout, asegurando un control seguro de la sesión y acceso a los recursos.