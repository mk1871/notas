## Curiosidades varias

* No hay módulos.

### Variables en funciones

* Las variables definidas dentro de una función **no pueden ser accedidas fuera de la función**.
* Las variables definidas fuera de una función **no pueden ser accedidas dentro de la función**.
	Para poder usarla dentro de una función hay dos maneras:
	1. Indicar que es una variable global: 
```php
function get_data(string $url): array  
{  
   global $title;
   echo $title;
}
```

2. Pasarla como parámetro:
```php
function get_data(string $url, string $title): array  
{  
   echo $title;
}
```
## Desactivar transformación automática de tipos

Con el fin de desactivar esta característica que puede ser útil pero problemática, hay que hacer la siguiente declaración al inicio del archivo:

```php
declare(strict_types=1)
```

Lo anterior hay que hacerlo en **todos** los archivos, no funciona a nivel global de PHP.

Luego, podemos mejorarla indicando en las funciones el tipo del parámetro y el tipo de dato que retorna, de la siguiente manera:

```php
function get_data(string $url): array  
{  
    $result = file_get_contents($url);
    $data = json_decode($result, true);  
    return $data;  
  
}
```

## Consumir API con GET

Hacer `GET` a un json sin pasarle nada, basta con esto:

``` php
$result = file_get_contents(API_URL); // Si sólo queremos hacer GET
$DATA = json_decode($result, true); // Convierte a un array asociativo de PHP
```

## Importar

Se puede de varias maneras.
- Usando `require`

```php
require 'functions.php'
```

El `require` se puede hacer cuantas veces se quiera, pero nos va a dar error. Tenemos un problema porque **los métodos y funciones traídas no se pueden redeclarar**.

Eso pasa porque al importar con `require` se trae todo el contenido del archivo importado.

Para evitar eso debe hacerse
- Usando `require_once`

```php
require_once 'functions.php'
```

Esto hace que si en algún momento ya se ha requerido el archivo, no lo vuelve a requerir.

Con `require`,  ***si el archivo requerido no existe, se rompe la página., da un ´fatal error´***.

- Usando `include`

```php
include 'functions.php'
```

- Usando `include_once`
- 
```php
include_once 'functions.php'
```

Con `include`,  ***si el archivo requerido no existe, sólo da un ´warning´***.

Preferible usar `require`. Es preferible que incluso falle la app si estamos importanto cosas críticas que necesita la página. El `include` tal vez en contenido de páginas generadas dinámicamente.

Hacer el `require` varias veces tiene sentido cuando tenemos elementos que queremos usar varias veces, por ejemplo, cuando queremos poner un banner en varias partes en una misma página, pueden ser scripts también.

***OJO: aunque hagamos un `require` se va a tener acceso a las variables del archivo que se está importando


## Fragmentos de código

### foreach

```php
<?php 
foreach ($books as book): ?>
	<li><?= $book; ?>  </li>
<?php 
endforeach; ?>
```

	
### mostrar errores

```php
<?php // Mostrar todos los errores
error_reporting(E_ALL); 
// Habilitar la visualización de errores 
ini_set('display_errors', 1); 
// Asegurarse de que las advertencias, errores y otros problemas se muestren 
ini_set('display_startup_errors', 1); ?>
```

### función para filtrar en un arreglo

```php
function filterByAuthor($books, $author)  
{  
    $filteredBooks = [];  
  
    foreach ($books as $book) {  
        if ($book['author'] === $author) {  
            $filteredBooks[] = $book;  
        }  
    }  
  
    return $filteredBooks;  
}  
?>
```


Lo mostramos

```php
<ul>  
    <?php  
    foreach (filterByAuthor($books, 'pepe') as $book): ?>  
  
        <p><?= "{$book['name']} ({$book['releaseYear']}) By {$book['author']} Buy it -> {$book['purchaseUrl']}" ?></p>  
  
  
    <?php  
    endforeach; ?>  
  
</ul>
```

Pero... con esa función estamos filtrando sólo por un parámetro. ¿Qué pasaría si quiero filtrar por otro parámetro? Tendría que crear una función más. ¿Y si hay 10 o más parámetros y quiero presentar la opción de filtrar por cada uno de ellos...? ¿creamos 10 o más funciones que en términos generales hacen lo mismo pero con diferente parámetro? Eso no es muy sofisticado que digamos, así que busquemos otra solución:
#### algo más sofisticado

creamos la función, pero manejamos lo que queremos filtrar de manera separada.

```php
function filter($items, $fn)  
{  
    $filteredItems = [];  
  
    foreach ($items as $item) {  
        if ($fn($item)) {  
            $filteredItems[] = $item;  
        }  
    }  
  
    return $filteredItems;  
}  
  
$filteredBooks = filter($books, function ($book) {  
    return $book['releaseYear'] >= 2010;  
});
```

Pero hay algo más interesante, la función propia de PHP que hace eso:

### array_filter

```php
$filteredBooks = array_filter($books, function ($book) {  
    return $book['releaseYear'] >= 2010;  
});
```

### dump an die
Para visualizar el contenido de una variable y después "morir", ya que en el momento sólo nos insteresa eso y no ejecutar el resto del archivo.

```php
function dd($value)  
{  
    echo "<pre>";  
    echo var_dump($value);  
    echo "</pre>";  
    die();  
}
```

Por ejemplo: 
```php
dd($heading);
dd($_SERVER);
```

### $_SERVER([REQUEST_URI])

`$_SERVER['REQUEST_URI']` en PHP nos da acceso a la parte de la URL que sigue al nombre del dominio, es decir, la URI (Uniform Resource Identifier) solicitada por el cliente. Esto incluye la ruta al recurso solicitado, así como cualquier parámetro de consulta que esté presente.

Por ejemplo, si un usuario visita la URL:
`https://www.ejemplo.com/carpeta/pagina.php?param1=valor1&param2=valor2`

El valor de `$_SERVER['REQUEST_URI']` será:
```php
/carpeta/pagina.php?param1=valor1&param2=valor2
```
