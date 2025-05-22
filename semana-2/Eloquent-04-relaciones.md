# Eloquent: relaciones

Tiene sentido que un Job dado tenga o corresponda a una relación con un employer dado. Vamos a mirar el archivo de migración de `job_listings`. Concretamente su función up.

```php
public function up(): void  
{  
    Schema::create('job_listings', function (Blueprint $table) {  
        $table->id();  
        $table->string('title');  
        $table->string('salary');  
        $table->timestamps();  
    });  
}
```

Probablemente queramos algún tipo de llave foránea llamada employer_id. Pero para eso necesitamos una table de employers. Vamos a crearla, y además su correspondiente migración:

`php artisan make:model Employer -m`

En el archivo de migración deberíamos crear un campo para `name` así: `$table->string('name');`

## Agregar lleve foránea

Después deberíamos de ir al archivo de migración de `job_listings`, allí deberíamos crear un campo que apunte al id del employer. 

Esto lo haremos con un tipo `unsignedBigInteger` ¿Por qué este tipo?  Esto se debe a que cada vez que se llama a este método ID dentro de una migración, se está creando una columna entera que se incrementa automáticamente. Así que cuando usted genera sus claves externas, usted quiere asegurarse de que el tipo de la clave externa es idéntica.

```php
$table->unsignedBigInteger('employer_id');
```

O podemos hacerlo usando `foreignIdFor`.

```php
$table->foreignIdFor(\App\Models\Employer::class);
```

Como aun es temprano en el proyecto, podemos hacer un migrate:fresh.
`php artisan migrate:fresh` y así todas las tablas han sido reseteadas.

Luego de esto necesitamos 

## Actualizar factory con el nuevo campo

Ahora que hemos creado un nuevo campo que sirva como llave foránea, debemos actualizar la factory para dicho modelo. Modificaremos entonces el archivo correspondiente a `JobFactory` para agregar el nuevo campo.

Y aquí podemos hardcodear el valor, pero sería interesante asignarle a cada Job un employer generado por la factory de employer:

```php
employer_id' => Employer::factory()
```

Eso generará un único ID como el correspondiente employer.

## Crear la factory para el Employer

Además de crear el modelo y la migración, podemos crear la factory al mismo tiempo. Para eso, vamos a eliminar el archivo del modelo Employer y lo crearemos de nuevo junto a la migración y la factory.

`php artisan make:model Employer -f

Esto crea el model y el factory. Ahora debemos modificar el archivo factory correspondiente a este modelo. Agregando el campo `name` el cuál sí está en nuestra migración para Employer

Luego de esto creamos los datos de ejemplo de Jobs teniendo en cuenta que en su id_employer creará un nuevo employer.

`App\Models\Job::factory(10)->create()`




