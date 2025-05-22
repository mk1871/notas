# Eloquent: Model Factories

Las Factories se pueden usar para varias cosas. Una de ellas es para generar ràpidamente datos de ejemplo para llenar los campos de las tablas de la base de datos.

Muy útil, imagina, para un test, donde quieres probar que bla, bla, bla, etc. Podríamos usar una factory para llenar con datos de ejemplo rápidamente.

Otro caso podría ser mientras practicamos en nuestro entorno local.

En nuestro caso vamos a usarlo para crear cuentas de usuario. 

Vamos a ver que nuestro modelo User tiene en su cuerpo `use HasFactory`. Esto le agrega a la clase User una serie de métodos para generar datos de ejemplo.

> [!NOTE]
> Si hemos modificado las tablas en las migraciones de cada una de ellas, agregando atributos, entonces debe actualizarse la respectiva factory.

Por ejemplo, en el cuerpo de la migración correspondiente a la tabla users, tenemos esto:

```php
Schema::create('users', function (Blueprint $table) {  
    $table->id();  
    $table->string('first_name');  
    $table->string('last_name');  
    $table->string('email')->unique();  
    $table->timestamp('email_verified_at')->nullable();  
    $table->string('password');  
    $table->rememberToken();  
    $table->timestamps();  
});
```

El archivo `UserFactory` debería de lucir así:

```php
public function definition(): array  
{  
    return [  
        'first_name' => fake()->firstName(),  
        'last_name' => fake()->lastName(),  
        'email' => fake()->unique()->safeEmail(),  
        'email_verified_at' => now(),  
        'password' => static::$password ??= Hash::make('password'),  
        'remember_token' => Str::random(10),  
    ];  
}
```

> [!NOTE]
> Si con Tinker tenemos un error y luego de arreglarlo, lo ejecutamos y aun aparece el error, es necesario reiniciar Tinker para que lo cambiado tenga efecto.

## ¿Cómo creamos datos de ejemplo?

Ejecutamos tinker

`php artisan tinker`

Luego vamos a la carpeta de nuestro modelo y aplicamos el método factory y luego create, así:

`App\Model\User::factory()->create()`

Para crear varios usuarios, simplemente pasamos el número como parámetro al método factory.

`App\Model\User::factory(100)->create()`

Para ver la ayuda de factory, ejecutamos:
`php artisan help make:factory`

## Crear un factory a un model

`php artisan make:factory Job`

Donde Job es el nombre del modelo. El comando anterior ha creado la factory para ese modelo.

Nos creará un archivo así:

```php
<?php  
  
namespace Database\Factories;  
  
use Illuminate\Database\Eloquent\Factories\Factory;  
  
/**  
 * @extends \Illuminate\Database\Eloquent\Factories\Factory<\App\Models\Job>  
 */class JobFactory extends Factory  
{  
    /**  
     * Define the model's default state.     *     * @return array<string, mixed>  
     */    public function definition(): array  
    {  
        return [  
  
        ];  
    }  
}
```

Como vemos, no tiene valores de retorno, ahí escribiremos las columnas que en nuestra tabla queremos llenar, podemos utilizar el método `fake()` para crear diversos datos de ejemplo, o también podemos poner datos hardcodeados, como veremos aquí:

```php
public function definition(): array  
{  
    return [  
  
        'title' => fake()->jobTitle(),  
        'salary'=> '4.000.000 COP'  
  
    ];  
}
```

>[!NOTE]
>Para que pueda funcionar la factory, el modelo debe usar factory teniendo en su cuerpo el llamado a HasFactory así:
>```php
>use HasFactory

Una vez verificamos que nuestro modelo tiene ese código, entonces creamos los datos de ejemplo:

`App\Models\Job::factory()->create()`

Podemos crear muchos, por ejemplo, 300:

`App\Models\Job::factory(300)->create()`

## Activar `unverified mail`

Quizás queramos activar el mal sin verificar, tal vez para hacer test u otro tipo de tareas.

`App\Models\User::factory()->unverified()->create()`




