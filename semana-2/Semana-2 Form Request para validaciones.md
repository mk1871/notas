
# Semana-2 Form Request para validaciones


## 1. Crear `RegisterRequest`


```shell
php artisan make:request RegisterRequest
```

Luego, en `app/Http/Requests/RegisterRequest.php`, definir las reglas de validación:

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class RegisterRequest extends FormRequest
{
    public function authorize(): bool
    {
        // Permite que cualquier usuario realice esta solicitud
        return true;
    }

    public function rules(): array
    {
        return [
            'first_name'            => 'required|string|max:255',
            'last_name'             => 'required|string|max:255',
            'email'                 => 'required|string|email|max:255|unique:users',
            'password'              => 'required|string|min:8|confirmed',
            'password_confirmation' => 'required|string|min:8',
        ];
    }

    public function messages(): array
    {
        return [
            'first_name.required' => 'El nombre es obligatorio.',
            'last_name.required'  => 'El apellido es obligatorio.',
            'email.required'      => 'El correo electrónico es obligatorio.',
            'email.email'         => 'Debe proporcionar un correo electrónico válido.',
            'email.unique'        => 'Este correo electrónico ya está registrado.',
            'password.required'   => 'La contraseña es obligatoria.',
            'password.min'        => 'La contraseña debe tener al menos 8 caracteres.',
            'password.confirmed'  => 'Las contraseñas no coinciden.',
        ];
    }
}
```


## 2. Crear `LoginRequest`

```shell
php artisan make:request LoginRequest
```

En `app/Http/Requests/LoginRequest.php`, definir las reglas:

```php
<?php  
  
namespace App\Http\Requests;  
  
use Illuminate\Foundation\Http\FormRequest;  
  
class LoginRequest extends FormRequest  
{  
    public function authorize(): bool  
    {  
        // Permite que cualquier usuario realice esta solicitud  
        return true;  
    }  
  
    public function rules(): array  
    {  
        return [  
            'email'    => 'required|string|email',  
            'password' => 'required|string',  
        ];  
    }  
  
    public function messages(): array  
    {  
        return [  
            'email.required'    => 'El correo electrónico es obligatorio.',  
            'email.email'       => 'Debe proporcionar un correo electrónico válido.',  
            'password.required' => 'La contraseña es obligatoria.',  
        ];  
    }  
}
```

## 3. Modificar el Controlador de Autenticación

### Modificar el Método `register`

```php
public function register(RegisterRequest $request): JsonResponse  
{  
    try {  
        $validated = $request->validated();  
  
        $user = User::create([  
            'first_name' => $validated['first_name'],  
            'last_name'  => $validated['last_name'],  
            'email'      => $validated['email'],  
            'password'   => Hash::make($validated['password']),  
        ]);  
  
        $token = $user->createToken('auth_token')->plainTextToken;  
  
        return response()->json([  
            'token'   => $token,  
            'message' => 'Usuario registrado exitosamente',  
        ], 201);  
    } catch (\Exception $e) {  
        // Registro de errores y retorno de respuesta genérica  
        \Log::error('Error en registro: ' . $e->getMessage());  
  
        return response()->json([  
            'error' => 'Ocurrió un error al registrar el usuario.',  
        ], 500);  
    }  
}
```

## 4. Modificar el Método `login`

Importa el `LoginRequest` y actualiza el método:

```php

public function login(LoginRequest $request): JsonResponse
{
    try {
        $credentials = $request->validated();

        if (!Auth::attempt($credentials)) {
            throw ValidationException::withMessages([
                'email' => ['Las credenciales no coinciden.'],
            ]);
        }

        $user = Auth::user();

        $token = $user->createToken('auth_token')->plainTextToken;

        return response()->json([
            'token'   => $token,
            'message' => 'Inicio de sesión exitoso',
        ], 200);
    } catch (ValidationException $e) {
        return response()->json([
            'errors' => $e->errors(),
        ], 422);
    } catch (\Exception $e) {
        \Log::error('Error en inicio de sesión: ' . $e->getMessage());

        return response()->json([
            'error' => 'Ocurrió un error al iniciar sesión.',
        ], 500);
    }
}
```


## 5. Modificar el Método `logout`

Asegurarse de que el método `logout` esté protegido por middleware `auth:sanctum`.

```php
public function logout(Request $request): JsonResponse
{
    try {
        $request->user()->currentAccessToken()->delete();

        return response()->json([
            'message' => 'Cierre de sesión exitoso',
        ], 200);
    } catch (\Exception $e) {
        \Log::error('Error en cierre de sesión: ' . $e->getMessage());

        return response()->json([
            'error' => 'Ocurrió un error al cerrar sesión.',
        ], 500);
    }
}

```