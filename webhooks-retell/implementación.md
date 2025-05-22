
## 1. Instalar soporte para API o crearlo manualmente
`php artisan install:api`

Nota de la ejecución:  

`INFO  API scaffolding installed. Please add the [Laravel\Sanctum\HasApiTokens] trait to your User model.  `

commit: Crear ruta api para el webhook en api.php, configurar API Key  en services.php


## 2. Crear Middleware y Helper para verificar la firma

commit: Crear Helper y Middleware de Verificación de Firma



## 3. Crear controlador

En PHP hay un método mágico llamado `__invoke()` que permite a un objeto ser llamado como una función. Entonces, si una clase tiene este método, puedes crear una instancia de la clase y luego llamarla como si fuera una función.

En el contexto de Laravel, los controladores usualmente tienen métodos como `index()`, `store()`, `show()`, etc., que corresponden a las rutas definidas. Pero en el ejemplo  de este proyecto, el controlador `WebhookController` usa `__invoke()`, lo cual es un poco diferente.

Pienso que al usar `__invoke()`, el controlador se convierte en un "invocable", lo que significa que la clase misma puede ser tratada como un método. Esto es útil cuando el controlador solo necesita manejar una única acción, como en este caso, recibir y procesar un webhook. En lugar de definir un método específico como `handleWebhook()`, se usa `__invoke()` para simplificar la estructura.

También recuerdo que en Laravel, cuando defines una ruta que apunta a un controlador, puedes especificar el método a utilizar. Pero si el controlador es invocable, no necesitas especificar el método, solo la clase. Por ejemplo:

```php

Route::post('/webhook', WebhookController::class);

```

Esto hace el código más limpio y directo, especialmente cuando el controlador solo tiene una responsabilidad. Además, sigue el principio de responsabilidad única, ya que el controlador solo se encarga de manejar un tipo de solicitud.
