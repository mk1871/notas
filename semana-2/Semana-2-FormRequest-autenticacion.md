
¡Claro que sí! Me encantaría ayudarte a implementar la autenticación en tu aplicación Laravel utilizando **Sanctum** y aplicar buenas prácticas con **Form Requests** para la validación. También te proporcionaré los casos de prueba en Postman para verificar que tus rutas están protegidas correctamente.

---

## **1. Implementar Form Requests para la Autenticación**

Primero, vamos a crear **Form Requests** para las acciones de registro y login en tu `AuthController`. Esto ayudará a mantener tu controlador limpio y a centralizar la lógica de validación.

### **1.1. Crear el Form Request para Registro**

Ejecuta el siguiente comando para generar el Form Request:

```bash
php artisan make:request RegisterRequest
```

**Contenido de `app/Http/Requests/RegisterRequest.php`:**

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class RegisterRequest extends FormRequest
{
    public function authorize()
    {
        return true; // Permitir que cualquier usuario no autenticado pueda registrarse
    }

    public function rules()
    {
        return [
            'first_name' => 'required|string|max:255',
            'last_name'  => 'required|string|max:255',
            'email'      => 'required|string|email|max:255|unique:users',
            'password'   => 'required|string|min:8|confirmed',
        ];
    }
}
```

**Notas:**

- **`authorize()`:** Retorna `true` para permitir el acceso a esta solicitud.
- **`rules()`:** Contiene las reglas de validación para el registro de usuario.

### **1.2. Crear el Form Request para Login**

Ejecuta el siguiente comando para generar el Form Request:

```bash
php artisan make:request LoginRequest
```

**Contenido de `app/Http/Requests/LoginRequest.php`:**

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class LoginRequest extends FormRequest
{
    public function authorize()
    {
        return true; // Permitir que cualquier usuario no autenticado pueda iniciar sesión
    }

    public function rules()
    {
        return [
            'email'    => 'required|string|email',
            'password' => 'required|string',
        ];
    }
}
```

### **1.3. Actualizar el `AuthController` para Usar los Form Requests**

Modifica tu `AuthController` para utilizar los nuevos Form Requests.

**Contenido actualizado de `app/Http/Controllers/AuthController.php`:**

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use App\Http\Requests\RegisterRequest;
use App\Http\Requests\LoginRequest;
use Illuminate\Http\JsonResponse;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;

class AuthController extends Controller
{
    /**
     * Register a new user
     */
    public function register(RegisterRequest $request): JsonResponse
    {
        $validated = $request->validated();

        $user = User::create([
            'first_name' => $validated['first_name'],
            'last_name'  => $validated['last_name'],
            'email'      => $validated['email'],
            'password'   => Hash::make($validated['password']),
        ]);

        return response()->json(['message' => 'Usuario registrado exitosamente'], 201);
    }

    /**
     * Log in a user
     */
    public function login(LoginRequest $request): JsonResponse
    {
        $credentials = $request->validated();

        $user = User::where('email', $credentials['email'])->first();

        if (!$user || !Hash::check($credentials['password'], $user->password)) {
            throw ValidationException::withMessages([
                'email' => ['Las credenciales no coinciden.'],
            ]);
        }

        $token = $user->createToken('auth_token')->plainTextToken;

        return response()->json(['token' => $token, 'message' => 'Inicio de sesión exitoso'], 200);
    }

    /**
     * Log out a user
     */
    public function logout(): JsonResponse
    {
        auth()->user()->currentAccessToken()->delete();

        return response()->json(['message' => 'Cierre de sesión exitoso'], 200);
    }
}
```

**Notas:**

- **Importación de los Form Requests:** Se agregan `use App\Http\Requests\RegisterRequest;` y `use App\Http\Requests<LoginRequest;`.
- **Uso de `$request->validated()`:** Obtiene los datos validados.
- **Eliminación de la dependencia del objeto `Request`:** Ahora usamos los Form Requests en lugar de `Request`.
- **Simplificación del método `logout()`:** No es necesario pasar el `$request` si no se usa.

---

## **2. Configurar Sanctum en la Aplicación**

Asegúrate de que Sanctum está instalado y configurado correctamente en tu aplicación.

### **2.1. Instalar Sanctum (si aún no lo has hecho)**

Ejecuta el siguiente comando:

```bash
composer require laravel/sanctum
```

### **2.2. Publicar la Configuración y las Migraciones de Sanctum**

```bash
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

### **2.3. Ejecutar las Migraciones**

```bash
php artisan migrate
```

### **2.4. Configurar el Middleware de Sanctum**

En el archivo `config/sanctum.php`, verifica que la opción `stateful` incluye tus dominios locales:

```php
'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', 'localhost,127.0.0.1')),
```

### **2.5. Añadir el Middleware de Autenticación de Sanctum**

En el archivo `app/Http/Kernel.php`, en el grupo de middleware `api`, añade `'auth:sanctum'` si aún no está incluido.

```php
protected $middlewareGroups = [
    'api' => [
        // Otros middlewares...
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
];
```

**Nota:** Si solo deseas proteger rutas específicas, puedes aplicar el middleware `'auth:sanctum'` directamente en las rutas o controladores.

---

## **3. Proteger las Rutas de la API**

Actualiza tus rutas en `routes/api.php` para proteger las rutas de `JobController` y definir claramente las rutas públicas y protegidas.

**Contenido actualizado de `routes/api.php`:**

```php
<?php

use App\Http\Controllers\JobController;
use App\Http\Controllers\AuthController;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

// Rutas públicas (no requieren autenticación)
Route::post('/register', [AuthController::class, 'register']);
Route::post('/login', [AuthController::class, 'login']);

// Rutas protegidas (requieren autenticación)
Route::middleware('auth:sanctum')->group(function () {
    // Ruta para obtener información del usuario autenticado
    Route::get('/user', function (Request $request) {
        return $request->user();
    });

    // Ruta para cerrar sesión
    Route::post('/logout', [AuthController::class, 'logout']);

    // Rutas de "JobController"
    Route::apiResource('jobs', JobController::class);
});
```

**Notas:**

- **Rutas públicas:** Las rutas de registro y login permanecen fuera del middleware `auth:sanctum`.
- **Rutas protegidas:** Todas las rutas dentro del grupo middleware `auth:sanctum` requieren autenticación con un token válido.
- **Ahora, las rutas de `JobController` están protegidas.**

---

## **4. Actualizar el `JobController` para Manejar la Autenticación**

Si es necesario, puedes actualizar el `JobController` para utilizar el usuario autenticado en ciertas operaciones.

**Ejemplo:**

```php
public function store(StoreJobRequest $request)
{
    $validated = $request->validated();

    $job = Job::create([
        'title'       => $validated['title'],
        'salary'      => $validated['salary'],
        'employer_id' => $validated['employer_id'],
        'user_id'     => auth()->id(), // Si deseas asociar el job al usuario autenticado
    ]);

    // Manejo de etiquetas si corresponde
    if (isset($validated['tags'])) {
        $job->tags()->attach($validated['tags']);
    }

    return response()->json($job, 201);
}
```

---

## **5. Archivos Modificados y Nuevos**

- **Nuevos archivos:**
  - `app/Http/Requests/RegisterRequest.php`
  - `app/Http/Requests/LoginRequest.php`
- **Archivos modificados:**
  - `app/Http/Controllers/AuthController.php`
  - `routes/api.php`
  - `app/Http/Kernel.php` (si añadiste middleware)
  - `config/sanctum.php` (si modificaste la configuración)

---

## **6. Casos de Prueba en Postman para Rutas Protegidas**

Ahora que las rutas de `JobController` están protegidas, necesitarás un token de autenticación para acceder a ellas. A continuación, te proporciono casos de prueba para verificar que las rutas están protegidas y funcionan correctamente.

### **6.1. Registrar un Nuevo Usuario**

- **Método:** `POST`
- **URL:** `/register`
- **Headers:**
  - `Accept: application/json`
  - `Content-Type: application/json`
- **Body (raw, JSON):**

  ```json
  {
    "first_name": "Juan",
    "last_name": "Pérez",
    "email": "juan.perez@example.com",
    "password": "password123",
    "password_confirmation": "password123"
  }
  ```

- **Esperado:**
  - Código de estado `201 Created`
  - Mensaje: `Usuario registrado exitosamente`

### **6.2. Iniciar Sesión y Obtener Token**

- **Método:** `POST`
- **URL:** `/login`
- **Headers:**
  - `Accept: application/json`
  - `Content-Type: application/json`
- **Body (raw, JSON):**

  ```json
  {
    "email": "juan.perez@example.com",
    "password": "password123"
  }
  ```

- **Esperado:**
  - Código de estado `200 OK`
  - Respuesta con `token` y mensaje `Inicio de sesión exitoso`

### **6.3. Acceder a una Ruta Protegida sin Token (Debería Fallar)**

- **Método:** `GET`
- **URL:** `/jobs`
- **Headers:**
  - `Accept: application/json`
- **Esperado:**
  - Código de estado `401 Unauthorized`
  - Mensaje de error indicando que no está autenticado

### **6.4. Acceder a una Ruta Protegida con Token (Debería Funcionar)**

- **Método:** `GET`
- **URL:** `/jobs`
- **Headers:**
  - `Accept: application/json`
  - `Authorization: Bearer {token}`
    - Reemplaza `{token}` con el token obtenido en el paso 6.2
- **Esperado:**
  - Código de estado `200 OK`
  - Lista de empleos en formato JSON

### **6.5. Crear un Nuevo Job con Autenticación**

- **Método:** `POST`
- **URL:** `/jobs`
- **Headers:**
  - `Accept: application/json`
  - `Content-Type: application/json`
  - `Authorization: Bearer {token}`
- **Body (raw, JSON):**

  ```json
  {
    "title": "Desarrollador Backend",
    "salary": "80000",
    "employer_id": 1,
    "tags": [1, 2]
  }
  ```

- **Esperado:**
  - Código de estado `201 Created`
  - Detalles del empleo creado

### **6.6. Cerrar Sesión**

- **Método:** `POST`
- **URL:** `/logout`
- **Headers:**
  - `Accept: application/json`
  - `Authorization: Bearer {token}`
- **Esperado:**
  - Código de estado `200 OK`
  - Mensaje: `Cierre de sesión exitoso`

### **6.7. Intentar Acceder a Ruta Protegida Después de Cerrar Sesión**

- **Método:** `GET`
- **URL:** `/jobs`
- **Headers:**
  - `Accept: application/json`
  - `Authorization: Bearer {token}` (el mismo token de antes)
- **Esperado:**
  - Código de estado `401 Unauthorized`
  - Mensaje de error indicando que no está autenticado

---

## **Notas Adicionales para las Pruebas en Postman**

- **Almacenar el Token en una Variable de Entorno:**

  - Después de obtener el token en el paso 6.2, puedes guardarlo en una variable de entorno en Postman llamada `auth_token`.
  - En las solicitudes subsiguientes, utiliza `{{auth_token}}` en el header `Authorization`.

- **Headers Comunes para Rutas Protegidas:**

  - `Accept: application/json`
  - `Content-Type: application/json` (si envías un body)
  - `Authorization: Bearer {{auth_token}}`

- **Manejo de Errores:**

  - Verifica que al intentar acceder a rutas protegidas sin autenticación, la API responde con códigos de estado y mensajes de error apropiados.

---

## **Implementación de Buenas Prácticas**

- **Uso de Form Requests:**

  - Centraliza la validación y mantiene los controladores limpios.
  - Facilita la reutilización y el mantenimiento del código.

- **Protección de Rutas:**

  - Aplica el middleware `auth:sanctum` solo a las rutas que deben estar protegidas.
  - Mantén las rutas públicas separadas y claramente definidas.

- **Manejo Adecuado de Respuestas:**

  - Utiliza códigos de estado HTTP correctos (`201 Created`, `200 OK`, `401 Unauthorized`, etc.).
  - Proporciona mensajes claros y consistentes en las respuestas.

- **Seguridad:**

  - No expongas información sensible en las respuestas de error.
  - Asegúrate de que los tokens se invalidan correctamente al cerrar sesión.

---

## **Conclusión**

Con estos cambios, has implementado la autenticación en tu aplicación utilizando Sanctum, aplicando buenas prácticas con Form Requests para la validación, y protegiendo tus rutas de API. Además, tienes una serie de casos de prueba en Postman para verificar que todo funciona correctamente.

---

¡Espero que esta guía te sea de gran ayuda! Si tienes más preguntas o necesitas asistencia adicional, no dudes en decírmelo. ¡Estoy aquí para ayudarte en lo que necesites!