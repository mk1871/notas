
Antes, breve resumen de lo que es CRUD y los métodos que en con el controller se usan para gestioanrlo:

## Resumen de las Correspondencias

|CRUD|Método del Controlador|Tipo de Solicitud HTTP|
|---|---|---|
|**Create**|`store()`|**POST**|
|**Read**|`index()`, `show($id)`|**GET**|
|**Update**|`update($id)`|**PUT** / **PATCH**|
|**Delete**|`destroy($id)`|**DELETE**|

Estas son las operaciones CRUD básicas que cualquier sistema basado en datos debería soportar, y cada uno de estos métodos se corresponde con una operación específica en el `JobController`.

## Instalar el soporte a API

`php artisan install:api`

Lo anterior...
- **Crea el archivo `routes/api.php`** que se utiliza para definir las rutas de la API. Estas rutas son asignadas al grupo de middleware `api`, lo que permite manejar peticiones sin estado.
- **Registra automáticamente el archivo `api.php` en `bootstrap/app.php`**, asegurando que la aplicación reconozca y cargue las rutas de la API.
- **Instala Laravel Sanctum**, que es un sistema de autenticación basado en tokens para proteger los endpoints de la API.
- **Configura una migración para manejar tokens de acceso personal**, utilizados para la autenticación de usuarios en la API.

Debemos aceptar las migraciones que muestra el prompt en la terminal, ya que en ese punto al ejecutarse el comando también se publicó una nueva migración para manejar tokens de acceso personal, Laravel está pidiendo que decidas quiero ejecutar todas las migraciones pendientes.

Luego, al finalizar estas operaciones, se pide en terminal que agregue unas líneas al Model User, esto es lo que agregaremos: `HasApiTokens`

De modo que la línea se vea así:

```php
class User extends Authenticatable  
{  
    /** @use HasFactory<\Database\Factories\UserFactory> */  
    use HasFactory, Notifiable, HasApiTokens;
```

## 1. Crear controladores

Lo vamos a hacer para job, pues me piden que sea en una entidad.

`php artisan make:controller JobController --api`

La opción `--api` generará un controlador sin métodos para vistas, solo los necesarios para la API (`index`, `store`, `show`, `update`, `destroy`).

## 2. Definir las Rutas CRUD en `api.php`

Vamos a definir las rutas de la API para `JobController` en el archivo `routes/api.php`:

**Agregar las Rutas CRUD para `JobController`**:

- Define las rutas CRUD usando el recurso `apiResource`. Esto permite que todas las rutas necesarias (como `GET`, `POST`, `PUT`, `DELETE`) sean generadas automáticamente para la entidad `Job`.

- Agrega la siguiente línea al archivo `api.php`:
	```php
Route::apiResource('jobs', JobController::class);
```

Esto crea automáticamente las rutas CRUD para la entidad `Job`, que se pueden acceder en `/api/jobs`.

## 3. Comprobar las rutas generadas

**Ejecutar el siguiente comando** para listar todas las rutas de tu proyecto y verificar que las rutas CRUD para `Job` se hayan registrado correctamente:

```php
php artisan route:list
```

Se debe generar algo así:

![[Pasted image 20241015124707.png]]

## Implementar métodos

### Método `index()`

Abrir `JobController.php`, agregar el método index:

```php
public function index()
{
    return response()->json(Job::all());
}
```
Utiliza la función `response()` para crear una respuesta HTTP explícita y la transforma en JSON. Esta forma es un poco más explícita sobre lo que está ocurriendo.

### Método `store()`

Abrir `JobController.php`, agregar el método `store`:

```php
public function store(Request $request): JsonResponse
{
    $validated = $request->validate([
        'title' => 'required|string|max:255',
        'salary' => 'required|string|max:255',
        'employer_id' => 'required|exists:employers,id',
    ]);

    $job = Job::create($validated);
    return response()->json($job, 201);
}
```

Este método valida los datos del trabajo, lo crea y devuelve una respuesta JSON con código `201` (creado).

### Método `show()`

Abrir `JobController.php`, agregar el método `show`:

```php
public function show(int $id): JsonResponse
{
    $job = Job::find($id);

    if (!$job) {
        return response()->json(['message' => 'Job not found'], 404);
    }

    return response()->json($job);
}
```

Este método busca un trabajo por su ID y devuelve una respuesta JSON. Si no se encuentra, devuelve un error `404` con un mensaje.

### Método `update()`

Abrir `JobController.php`, agregar el método `update`:

```php
public function update(Request $request, int $id): JsonResponse
{
    $job = Job::find($id);

    if (!$job) {
        return response()->json(['message' => 'Job not found'], 404);
    }

    $validated = $request->validate([
        'title' => 'sometimes|required|string|max:255',
        'salary' => 'sometimes|required|string|max:255',
        'employer_id' => 'sometimes|required|exists:employers,id',
    ]);

    $job->update($validated);

    return response()->json($job);
}
```

Este método busca un trabajo por su ID, valida los datos proporcionados y actualiza el registro con los datos nuevos. Si no se encuentra, devuelve un error `404` con un mensaje.

### Método `update()`

Abrir `JobController.php`, agregar el método `update`:

```php
public function update(Request $request, int $id): JsonResponse
{
    try {
        $job = Job::find($id);

        if (!$job) {
            return response()->json(['message' => 'Job not found'], 404);
        }

        $validated = $request->validate([
            'title' => 'sometimes|required|string|max:255',
            'salary' => 'sometimes|required|string|max:255',
            'employer_id' => 'sometimes|required|exists:employers,id',
        ]);

        $job->update($validated);

        return response()->json($job, 200);
    } catch (\Illuminate\Validation\ValidationException $e) {
        return response()->json(['error' => 'Validación fallida', 'details' => $e->errors()], 422);
    } catch (\Exception $e) {
        return response()->json(['error' => 'Ha ocurrido un error inesperado', 'details' => $e->getMessage()], 500);
    }
}
```

Este método actualiza un trabajo específico y maneja de forma explícita las posibles excepciones que puedan ocurrir durante el proceso. Incluye un manejo de errores detallado, mejorando la capacidad de depuración y la experiencia del cliente que consume el API.

- **Lógica del Método**:
    - **Buscar el Trabajo**: Primero intenta encontrar el trabajo con `Job::find($id)`. Si no se encuentra, devuelve un error `404` con el mensaje `"Job not found"`.
    - **Validación de los Datos**:
        - Utiliza `sometimes|required` para validar campos opcionales. Esto permite actualizar solo algunos campos sin necesidad de enviar todos.
        - La regla `exists:employers,id` asegura que el `employer_id` referenciado exista en la base de datos, manteniendo la integridad de las relaciones.
    - **Manejo de Excepciones (`try-catch`)**:
        - **`ValidationException`**: Si los datos no cumplen con las reglas de validación, se lanza una excepción de tipo `ValidationException`. Esta excepción es capturada, y se devuelve un error `422` indicando los detalles específicos de la validación fallida.
        - **Errores Genéricos**: Cualquier otro error es capturado por el segundo bloque `catch`, y devuelve un error `500` con detalles del error ocurrido. Esto permite manejar problemas inesperados sin causar una falla completa del servidor.
    - **Respuesta Exitosa**: Si todo sale bien, actualiza el registro y devuelve el trabajo actualizado con un código HTTP `200`.

#### Casos de Prueba 

- **Actualizar Todos los Campos**:
    - Proporcionar todos los campos (`title`, `salary`, `employer_id`) y comprobar que la respuesta tenga código `200` y que los datos sean actualizados correctamente.
- **Actualizar Solo Algunos Campos**:
    - Por ejemplo, actualizar solo el campo `salary`. Comprobar que el cambio se aplique y que los demás campos permanezcan sin cambios.
- **Errores de Validación**:
    - Enviar un `employer_id` que no exista y comprobar que el código de respuesta sea `422` con el mensaje `"Validation failed"`.
- **Errores Genéricos**:
    - Probar situaciones inesperadas (por ejemplo, problemas de conectividad a la base de datos) y verificar que se manejen correctamente con un mensaje genérico y un código `500`.

#### Ventajas de Este Enfoque

- **Manejo de Errores Claros y Específicos**: Los errores de validación y los errores inesperados se manejan de forma separada, proporcionando mensajes detallados para cada situación.
- **Flexibilidad en la Actualización**: La validación con `sometimes|required` permite que los clientes puedan actualizar solo los campos necesarios, haciendo la API más flexible y fácil de usar.
- **Integridad Referencial**: Utilizar `exists` para validar `employer_id` asegura que la referencia siempre sea válida, evitando errores lógicos en la base de datos.

### Método `destroy()`

Abrir `JobController.php` y agregar el método `destroy`:
```php
public function destroy(string $id): JsonResponse  
    {  
        try {  
            $job = Job::find($id);  
  
            if (!$job) {  
                return response()->json(['message' => 'Empleo no encontrado'], 404);  
            }  
  
            $job->delete();  
  
            return response()->json(['message' => 'Empleo borrado satisfactoriamente'], 200);  
        } catch (\Exception $e) {  
            return response()->json(['error' => 'Ha ocurrido un error inesperado', 'details' => $e->getMessage()], 500);  
        }  
    }  
}
```

Este método se encarga de eliminar un trabajo específico de la base de datos. Si el trabajo no existe, devuelve un error `404`. Si se elimina correctamente, devuelve un mensaje de éxito con un código `200`. Se utiliza un bloque `try-catch` para capturar y manejar errores inesperados.