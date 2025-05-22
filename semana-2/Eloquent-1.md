# Eloquent
Abrir la ayuda de partisan
`php partisan`

Muestra info de la db
`php artisan db:show`

Cuando se hizo `laravel new` una de las cosas que se hicieron fue hacer las migraciones, construir la base de datos y sus correspondientes tablas.

Si se creó la app sin el comando `laravel new` se tendrá que hacer manualmente la migración:
`php artisan migrate`

Podemos borrar la base de datos, la volvemos a restaurar con:
`php artisan migrate`

Parece que la lógica para crear tablas está escrita en PHP. Después de modificar el contenido de una migración, por ejemplo, cambiando el nombre de una  columna, o agregando más:

```php
$table->string('first_name');  
$table->string('last_name');
```

hay que hacer la migración para que se apliquen los cambios. 
Para que se eliminen todas las tablas y se reconstruyan desde cero, hay que ejecutar
`php artisan migrate:refresh`

### Primer migración

Vamos a hacer una primera migración creando una tabla llamada job_listings:
`php artisan make:migration`

Luego de eso nos pide el nombre de la tabla, el cual creamos con:
`create_job_listings_table`

Nos genera dos métodos: **up** y **down**

```php
return new class extends Migration  
{  
    /**  
     * Run the migrations.     */    
	public function up(): void  
    {  
        Schema::create('job_listings', function (Blueprint $table) {  
            $table->id();  
            $table->timestamps();  
        });  
    }  
  
    /**  
     * Reverse the migrations.     */    
    public function down(): void  
    {  
        Schema::dropIfExists('job_listings');  
    }  
};
```

Up representa lo que quiero cambiar: add o remove,  y down hace lo inverso, lo revierte.

Podemos agregar columnas a dicha tabla:

```php
$table->string('title');  
$table->string('salary');
```

Para aplicar dichos cambios debemos aplicar la migración con `php artisan migrate`, esto hará el cambio en nuestra base de datos:

![[Pasted image 20241013201111.png]]
