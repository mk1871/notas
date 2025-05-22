## 1. Instalar Sanctum

```shell
php artisan install:api
```

## 2. Agregar el Trait en el Modelo de Usuario**

Abrir el archivo `User.php` que se encuentra en `app/Models/`.
Asegúrate de haber agregado el trait `Laravel\Sanctum\HasApiTokens`:

```php
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;
    // ...
}
```

## 3. Crear el `AuthController`

Primero, crear el controlador para gestionar la autenticación. Ejecutar el siguiente comando en la terminal para crear un controlador llamado `AuthController`:

```php
php artisan make:controller AuthController
```

Una vez creado, el controlador debe ubicarse en `app/Http/Controllers/AuthController.php`. En este archivo, añadiremos las funciones necesarias para registro (`register`), inicio de sesión (`login`), y cierre de sesión (`logout`).

## 4. Definir métodos en el `AuthController`

Edita el archivo `AuthController.php` y agregar lo siguiente:

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;

class AuthController extends Controller
{
    /**
     * Register a new user
     */
    public function register(Request $request): JsonResponse
    {
        $validated = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8|confirmed',
        ]);

        $user = User::create([
            'name' => $validated['name'],
            'email' => $validated['email'],
            'password' => Hash::make($validated['password']),
        ]);

        return response()->json(['message' => 'Usuario registrado exitosamente'], 201);
    }

    /**
     * Log in a user
     */
    public function login(Request $request): JsonResponse
    {
        $credentials = $request->validate([
            'email' => 'required|string|email',
            'password' => 'required|string',
        ]);

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
    public function logout(Request $request): JsonResponse
    {
        $request->user()->currentAccessToken()->delete();

        return response()->json(['message' => 'Cierre de sesión exitoso'], 200);
    }
}
```

#### Explicación:

1. **`register()`**:
    
    - Valida los datos ingresados (`name`, `email`, `password`).
    - Crea un nuevo usuario utilizando el modelo `User`.
    - Retorna un mensaje de éxito.
2. **`login()`**:
    
    - Valida las credenciales ingresadas.
    - Verifica si el usuario y la contraseña coinciden.
    - Si la autenticación es exitosa, crea un token y lo devuelve en la respuesta.
3. **`logout()`**:
    
    - Elimina el token de acceso actual del usuario.
    - Retorna un mensaje de cierre de sesión exitoso.


## 5. Añadir rutas de autenticación al archivo `api.php`

Abre el archivo `routes/api.php` y agrega las siguientes rutas:

```php
use App\Http\Controllers\AuthController;
use Illuminate\Support\Facades\Route;

Route::post('/register', [AuthController::class, 'register']);
Route::post('/login', [AuthController::class, 'login']);
Route::post('/logout', [AuthController::class, 'logout'])->middleware('auth:sanctum');
```


A estas alturas, el archivo de `routes/api.php` debería lucir así:

```php
<?php  
  
use App\Http\Controllers\JobController;  
use Illuminate\Http\Request;  
use App\Http\Controllers\AuthController;  
use Illuminate\Support\Facades\Route;  
  
// Ruta para obtener información del usuario autenticado  
Route::get('/user', function (Request $request) {  
    return $request->user();  
})->middleware('auth:sanctum');  
  
// Ruta para "JobController"  
Route::apiResource('jobs', JobController::class);  
  
// Rutas de autenticación  
Route::post('/register', [AuthController::class, 'register']);  
Route::post('/login', [AuthController::class, 'login']);  
Route::post('/logout', [AuthController::class, 'logout'])->middleware('auth:sanctum');
```

## 6. Algunas pruebas sin proteger las rutas

>[!NOTE]
>Verificar que los campos que estemos usando tanto en el AuthController como en el modelo User y las tablas de la base de datos existan.

**Prueba de registro (POST a `/register`):**

En Postman, realizar una solicitud POST a `/register` y usar los siguientes datos en la pestaña "Body", seleccionando "raw" y "JSON":

```php
{
  "first_name": "Pepito",
  "last_name": "Pérez",
  "email": "pepito.perez@example.com",
  "password": "contraseñaSegura123",
  "password_confirmation": "contraseñaSegura123"
}
```

Esto debería registrar un nuevo usuario. Si la respuesta es un mensaje de éxito, el registro se realizó correctamente.

**Prueba de inicio de sesión (POST a `/login`):**

```php
{
  "email": "pepito.perez@example.com",
  "password": "contraseñaSegura123"
}
```

Debería recibir un token de autenticación si el inicio de sesión es exitoso. Copiar este token porque se necesitará para el paso siguiente.

## 7. Proteger las rutas de la API

Envolver las rutas del `JobController` con el middleware `auth:sanctum`, quedando el archivo así:

```php
Route::middleware('auth:sanctum')->group(function () {
    // Ruta para obtener información del usuario autenticado
    Route::get('/user', function (Request $request) {
        return $request->user();
    });

    // Ruta para "JobController"
    Route::apiResource('jobs', JobController::class);

    // Ruta para cerrar sesión
    Route::post('/logout', [AuthController::class, 'logout']);
});

// Rutas de autenticación (sin autenticación requerida)
Route::post('/register', [AuthController::class, 'register']);
Route::post('/login', [AuthController::class, 'login']);
```

