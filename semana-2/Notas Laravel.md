## `routes`
Carpeta con archivos que definen las rutas.

## `resources/views`
Carpeta con los views. Ejemplo: `welcome.blade.php`.
Blade es el motor de plantillas de Laravel.


## Layout dentro de `Components`

Se crea la capeta Components dentro de views, ahí irán nuestros layouts.

`layout.blade.php`

- Pasamos todo el código del `home.blade.php` al archivo de layout.
Para llamar ese layout a nuestra página, entonces escribirmos la etiqueta `<x-layout></x-layout>`. De esta forma podemos ver como layout se está cargando en el archivo donde hemos puesto la etiqueta.

En el Layout debemos crear el ``{{ slot }}`` para incluir dentro de él código.

## {{ attributes }}
Para pasar atributos


## Otras líneas

```php
request()->is('/') ? 'bg-gray-900 text-white' :'text-gray-300 hover:bg-gray-700 hover:text-white'
```

consulta si la url es / y en base a eso pone uno u otro estilo.

### props con blade

En el cuerpo de un atributo lo podemos iniciar con : para indicar que se usará una expresión, no un parámetro de nombre y valor.

```php
:active="request()->is('about')"
```
aquí no se pasa la cadena de texto como un valor sino como una expresión, y todo por los dos puntos al inicio.

### rutas

Al construir una ruta y agregarle un wildcard como {}, se entiende que estoy pasando una variable y a continuación debe ir una función:

```php
Route::get('/contact/{id}', function ($id) {  
    dd($id);  
    return view('contact');  
});
```


### Clases propias de Laravel


#### `Arr`
Este código en Laravel define una ruta y usa el método `Arr::first` para encontrar un elemento específico en un array. Me enfocaré en la línea con `Arr::first`, pero primero proporcionaré un contexto general para que todo el código se entienda mejor.

#### Explicación General

1. **Definir la Ruta**: 
   ```php
   Route::get('/jobs/{id}', function ($id) {...});
   ```
   Esta ruta escucha las solicitudes GET a la URL `/jobs/{id}`, donde `{id}` es un parámetro dinámico que se recibe como `$id` en la función.

2. **Definir el Array de Trabajos (`$jobs`)**:
   ```php
   $jobs = [
       ['id' => 1, 'title' => 'Director', 'salary' => '12.000.000'],
       ['id' => 2, 'title' => 'Programador', 'salary' => '4.000.000'],
       ['id' => 3, 'title' => 'Profesor', 'salary' => '8.000.000'],
   ];
   ```
   Aquí se crea un array con información de trabajos, cada uno con un `id`, un `title`, y un `salary`. Este array actúa como un conjunto de datos de ejemplo.

#### Explicación de la Línea con `Arr::first`

```php
$job = Arr::first($jobs, fn($job) => $job['id'] == $id);
```

- **`Arr::first`**: Este es un método de Laravel que busca el primer elemento en un array que cumple con una condición específica. En este caso:
  - **Parámetro 1 (`$jobs`)**: El array sobre el que se realiza la búsqueda.
  - **Parámetro 2 (la función de flecha)**: Una función anónima (o de flecha) que se aplica a cada elemento del array para determinar si cumple con la condición dada.

- **La función de flecha (`fn($job) => $job['id'] == $id`)**:
  - Esta función toma cada trabajo (`$job`) del array y verifica si el `id` del trabajo coincide con el `id` que se recibió en la URL.
  - Si encuentra un trabajo donde `$job['id']` es igual a `$id`, lo devuelve como resultado.
  - Si no encuentra ningún trabajo que cumpla con la condición, devuelve `null`.

- **Asignación a `$job`**:
  - El resultado de `Arr::first` se guarda en la variable `$job`.
  - `$job` contendrá el primer trabajo que cumpla con la condición o `null` si no se encuentra ninguno.

#### `dd($job)`

- **`dd($job)`**: Esta función es una abreviatura de "dump and die" en Laravel. Muestra el contenido de `$job` en la pantalla y detiene la ejecución del código.
- Esto es útil para depuración, ya que permite ver si se encontró el trabajo correcto y cómo se ve su estructura.


### volviendo un array una clase

```php
class Job {  
    public static function all(): array  
    {  
        return [  
            [  
                'id' => 1,  
                'title' => 'Director',  
                'salary' => '12.000.000'  
            ],  
            [   'id' => 2,  
                'title' => 'Programador',  
                'salary' => '4.000.000'  
            ],  
            [   'id' => 3,  
                'title' => 'Profesor',  
                'salary' => '8.000.000'  
            ],  
  
  
        ];  
  
    }  
}
```

Y así lo llamamos: `Job::all()`

### Migraciones

Para hacer una migración primero la creamos

```bash
php artisan make:migration create_nombre_de_la_tabla_table
```
Luego, la personalizamos según nuestras necesidades en el archivo php que nos ha creado, y después hacemos la migración:

```bash
php artisan migrate
```

Luego llenamos nuestra tabla según los datos con los que vayamos a trabajar.



