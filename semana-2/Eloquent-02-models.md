# Eloquent: Models

Esto no es de Eloquent pero vale para la visualización:. Por ejemplo, la siguiente línea nos mostraría la columna `title` del registro `1` de  la tabla `jobs`
```php
<?php  
  
use App\Models\Job;  
use Illuminate\Support\Facades\Route;  
  
Route::get('/', function () {  
    $jobs = Job::all();  
  
    dd($jobs[1]->title);  
  
  
    //return view('welcome');  
});
```

## Tablas y modelos

Si tu tabla se llama `posts` tu modelo se llamará `Post`.
Tablas en minúscula y plural, y modelos primera letra mayúscula, y modelo en singular.

Así esto, si creé el archivo Job.php manualmente, tengo dos opciones para que funcione como modelo:
- Renombrar el archivo de manera que tenga la forma singular de la tabla a la que nos referimos. Por ejemplo, si la tabla se llama `job_listings` el modelo debería llamarse `JobListing`.
	```php
	class JobListing extends Model
```

- Llamarle como quiera, pero incluir al inicio a cuál tabla se refiere:
	```php
	protected $table = 'job_listings';
```

## Tinker

```bash
php artisan tinker
```
### Crear un registro
```shell
App\Models\Job::create(['title' => 'Director Acme', 'salary' => '1.000.000']);
```
Cuando se asignan de esta manera los atributos, se está haciendo lo que para Laravel es un mass assignment, por lo cual nos salta una alerta, ya que esto puede ser usado por atacantes en formularios. Para permitir este tipo de asignación, debe editarse el archivo del modelo con la siguiente línea donde se indica las columnas que se permiten asignar de esa manera:

```php
protected $fillable = ['title', 'salary'];
```
Así, si un atacante intenta modificar el user ID, no podrá, ya que le estoy diciendo que sólo se aplique para los campos mencionados. 


### Mostrar todos los registros
`App\Models\Job::all();`

### Mostrar un registro

`App\Models\Job::find(3);`

No es mágico. Claro que una query SQL se ejecuta tras bambalinas.

### Borrar un registro

Podemos asignar un registro a una variable:
`$job = App\Models\Job::find(6)`

y luego eliminarlo:
`job->delete();`

Esto nos devuelve `true` cuando se ha eliminado correctamente.

## Crear modelos con Eloquent

Vamos a ver todo lo que ofrece el comando `make`
`php artisan make`

Con esto encontramos la multitud de parámetros y opciones que se nos ofrece con `make`. Nos interesa la que tiene que ver con los modelos, es decir, `model`.

Podemos obtener la ayuda del comando `make:model` de la siguiente manera
`php artisan help make:model`

Probablemente **empezarías creando el modelo**. Y entonces el modelo crearía la migración como parte de, eh, como parte de su lógica. Eso es algo para pensar.

### Creando el modelo `Comment`

`php artisan make:model Comment`

Esto crea un archivo como este:

```php
<?php  
  
namespace App\Models;  
  
use Illuminate\Database\Eloquent\Factories\HasFactory;  
use Illuminate\Database\Eloquent\Model;  
  
class Comment extends Model  
{  
    use HasFactory;  
}
```

Más adelante hablaremos de lo que significa e implica `use HasFactory;`.


### Creando el modelo `Post` y su migración correspondiente

Se puede pasar parámetros cuando se crea un modelo, por ejemplo, crear de una vez la migración de este:

`php artisan make:model Post -m`

Ahora no sólo se creo el archivo del model en la carpeta Models, sino que también se creó su migración en la carpeta migrations.

```php
<?php  
  
use Illuminate\Database\Migrations\Migration;  
use Illuminate\Database\Schema\Blueprint;  
use Illuminate\Support\Facades\Schema;  
  
return new class extends Migration  
{  
    /**  
     * Run the migrations.     */    public function up(): void  
    {  
        Schema::create('posts', function (Blueprint $table) {  
            $table->id();  
            $table->timestamps();  
        });  
    }  
  
    /**  
     * Reverse the migrations.     */    public function down(): void  
    {  
        Schema::dropIfExists('posts');  
    }  
};
```

>[!NOTE]
>Esos generadores nos dan una forma rápida de empezar a construir algo.

#### Agregando atributos a la tabla `posts`.

Para agregar atributos, debemos editar el archivo de migración, y agregar o cambiar los atributos que deseemos. En este caso agregaremos dos atributos:

```php
$table->string('title');  
$table->text('body');
```

Luego de eso debemos hacer la migración para que los cambios se apliquen a la base de datos.

`php artisan migrate`


