# Eloquent: relaciones

Si tengo un objeto jod, puedo acceder a su title y a su salary, pero ¿qué hay si quiero acceder a la información de su employer?

Tendremos que editar el modelo Job.

## Editando el modelo con relaciones

### `belongsTo`

Si tenemos un Job y queremos información de un Employer, entonces creamos un método llamado `employer`, el cuál retornará la relación que evidencia que un Job perteneceA (belongsTo) a un Employer.

```php
public function employer()  
{  
    return $this->belongsTo(Employer::class);  
}
```

Vamos a revisarlo. Con tinker obtenemos el primer job y lo metemos en una variable para manipularlo mejor más tarde:
`$job = App\Models\Job::first()`

```shell
= App\Models\Job {#5190
    id: 1,
    employer_id: 11,
    title: "Marine Oiler",
    salary: "$4.000.000 COP",
    created_at: "2024-10-15 04:33:16",
    updated_at: "2024-10-15 04:33:16",
  }
```

Para acceder a los datos del empleador, se escribe el nombre del mètodo pero no como un método sino como una propiedad
`$job->employer`

```shell
> $job->employer
= App\Models\Employer {#5598
    id: 11,
    name: "Kling-Weissnat",
    created_at: "2024-10-15 04:33:16",
    updated_at: "2024-10-15 04:33:16",
  }
```

Ahora, queremos acceder a su nombre:
`$job->employer->name`

```shell
> $job->employer->name
= "Kling-Weissnat"
```

¿Qué si lo hacemos al revés?

```shell
$employer = App\Models\Employer::first()
```

```shell
= App\Models\Employer {#6269
    id: 11,
    name: "Kling-Weissnat",
    created_at: "2024-10-15 04:33:16",
    updated_at: "2024-10-15 04:33:16",
  }
```

### `hasMany`

¿Cómo hacemos para ver los Jobs que tiene un employer?

Suena a que necesitamos un método en Employer, tal vez algo llamado jobs.

```php
public function jobs()  
{  
    return $this->hasMany(Job::class);  
  
}
```

Ahora vamos a ver, obtenemos el primer employer:

```shell
$employer = App\Models\Employer::first()
= App\Models\Employer {#6269
    id: 11,
    name: "Kling-Weissnat",
    created_at: "2024-10-15 04:33:16",
    updated_at: "2024-10-15 04:33:16",
  }
```

Y ahora, averiguemos los jobs de ese employer:

```shell
$employer->jobs
= Illuminate\Database\Eloquent\Collection {#5203
    all: [
      App\Models\Job {#6021
        id: 1,
        employer_id: 11,
        title: "Marine Oiler",
        salary: "$4.000.000 COP",
        created_at: "2024-10-15 04:33:16",
        updated_at: "2024-10-15 04:33:16",
      },
    ],
  }
```

Como vemos, nos retorna una collection, que es un array con esteroides. 

## Otras relaciones

### Tablas Pivot


¿Qué hay cuando, por ejemplo, un Post hasMany tags pero una Tag no pertenece a un Post?

En este caso hay que hacer una tabla pivot. 

### Creemos el modelo Tag

php artisan make:model Tag -mf

```shell
php artisan make:model Tag -mf
```

Se crea el modelo, la migración y la factory.

Editamos el archivo de migración de Tag para agregar la columna `name`:

```php
$table->string('name');
```


Cuando un **Post** tiene muchas **Tags**, pero una **Tag** no pertenece directamente a un **Post**, **es necesario usar una tabla pivot** para gestionar la relación muchos a muchos. Este patrón es fundamental cuando trabajas con datos que deben relacionarse entre sí, pero no necesariamente dependen uno del otro.

Ahora, tenemos nuestra tabla Jobs y nuestra tabla Tags. Debe haber algo entre ellas que las conecte, y esta es una tabla pivot.

Yo podría guardar el `tag_id` también como el `job_id`

Tenemos dos formas de avanzar: 

1. Podría crear una nueva migración, pero ten en cuenta que no hay una regla fuerte en la que una clase migración pueda contener solamente una llamada a create un Schema.
3. Así que si yo quisiera agrupar esas juntas  puedo hacerlo en la misma migración de tags.

Podemos copiar el mismo código de la creación del schema tags:

```php
Schema::create('tags', function (Blueprint $table) {  
    $table->id();  
    $table->string('name');  
    $table->timestamps();  
});
```

Y modificarlo, ya que una tabla pivot consiste en llaves foráneas, mayormente.

```php
Schema::create('job_tag', function (Blueprint $table) {  
    $table->id();  
    $table->foreignIdFor(\App\Models\Job::class, 'job_listing_id');  
    $table->foreignIdFor(\App\Models\Tag::class);  
    $table->timestamps();  
});
```

Hacemos la migración

```shell
php artisan migrate
```


Luego creamos una etiqueta, por ejemplo Programación con el id 1, si relacionamos eso en la tabla, por ejemplo, con el id 10 de jobs; pero si luego elimino esa etiqueta, aun permanece, así que tenemos un huérfano: tenemos un registro que apunta a una tag que no existe. Y eso es algo que no queremos permitir.

Podemos entonces añadir una foreign constraint. Y esto es muy importante porque SQLite por defecto es un poquito distinta de MySQL.

Vamos a nuestra migración de tags, a nuestra tabla de job_tag, y luego un 
`constrained()->cascadaOnDelete` en ambas llaves foreign, lo que implica que si yo elimino cualquiera de los registros, y estos están en la tabla de pivot, ese registro en esa tabla se elimina.

```php
Schema::create('job_tag', function (Blueprint $table) {  
    $table->id();  
    $table->foreignIdFor(\App\Models\Job::class, 'job_listing_id')->constrained()->cascadeOnDelete();  
    $table->foreignIdFor(\App\Models\Tag::class)->constrained()->cascadeOnDelete();  
    $table->timestamps();  
});
```

Luego hacemos esto para devolver los cambios en la última migración y luego hacer la migración.
Esto ya debería funcionar, pero... si borramos uno de los dos registros en cualquiera de las tablas que se relacionan con la tabla pivot, vamos a encontrar que no funciona, se elimina ese pero en la tabla pivot sigue todo intacto.

¿Qué pasó?

>[!NOTE]
>SQLite por defecto dice que el constrained no es enforced, pero en Laravel App está ENFORCED. Lo que quiere decir que si lo hago con la GUI que uso para la base de datos no va a funcionar a menos que lo habilite, y si lo hago con Laravel, sí que funcionará por default.

Podemos activarlo en nuestro SQL ejecutando lo siguiente:

```SQL
PRAGMA foreign_keys=on
```



### `belongsToMany`

Todavía no hemos establecido relación entre un Job y Tag. Y lo cierto es que un Job puede tener muchas Tag. 

Así que en la clase `Job` deberíamos agregar esto:

```php
public function tags(){  
    return $this->belongsToMany(Tag::class);  
}
```

y en la clase `Tag` esto:

```php
public function jobs(){  
    return $this->belongsToMany(Job::class);  
}
```

Ahora, establecemos algunas relaciones en la tabla pivot y vamos a comprobarlas en tinker.

Como el Modelo Job no lo creamos JobListing, sino Job apuntando a job_listings, debemos manualmente decirle a Laravel cuál es la llave foránea en la tabla pivot, de esta manera:

Hemos asignado la etiqueta `científico` al empleo 3. Comprobemos:

```shell
$job = App\Models\Job::find(3)
= App\Models\Job {#5205
    id: 3,
    employer_id: 3,
    title: "Automotive Body Repairer",
    salary: "$4.000.000 COP",
    created_at: "2024-10-15 07:46:02",
    updated_at: "2024-10-15 07:46:02",
  }
```


```shell
$job->tags
= Illuminate\Database\Eloquent\Collection {#6007
    all: [
      App\Models\Tag {#6223
        id: 1,
        name: "Cientifico",
        created_at: null,
        updated_at: null,
        pivot: Illuminate\Database\Eloquent\Relations\Pivot {#6222
          job_listing_id: 3,
          tag_id: 1,
        },
      },
    ],
  }
```

Como es un caso particular, entonces desde el modelo `Tag` debo señalar la llave foránea relacionada, así:

```php
public function jobs(){  
    return $this->belongsToMany(Job::class, relatedPivotKey: 'job_listing_id');  
}
```

Hemos asociado la profesion 5 con la etiqueta 3. Vamos a cprobarlo con tinker:

```php
$tag = App\Models\Tag::find(3)
= App\Models\Tag {#5902
    id: 3,
    name: "Construccion",
    created_at: null,
    updated_at: null,
  }
```

```php
$tag->jobs
= Illuminate\Database\Eloquent\Collection {#5203
    all: [
      App\Models\Job {#5207
        id: 5,
        employer_id: 5,
        title: "Sociologist",
        salary: "$4.000.000 COP",
        created_at: "2024-10-15 07:46:02",
        updated_at: "2024-10-15 07:46:02",
        pivot: Illuminate\Database\Eloquent\Relations\Pivot {#5212
          tag_id: 3,
          job_listing_id: 5,
        },
      },
    ],
  }
```

¡Funciona!

## ¿Cómo agregar nuevos registros con tinker?

```shell
$tag->jobs()->attach(7)
= null
```

```shell
$tag->jobs()->attach(App\Models\Job::find(7));
= null
```

Si volvemos a hacer la consulta después de haber salido y vuelto a entrar, tendremos esto:

```shell
$tag->jobs
= Illuminate\Database\Eloquent\Collection {#5199
    all: [
      App\Models\Job {#5185
        id: 5,
        employer_id: 5,
        title: "Sociologist",
        salary: "$4.000.000 COP",
        created_at: "2024-10-15 07:46:02",
        updated_at: "2024-10-15 07:46:02",
        pivot: Illuminate\Database\Eloquent\Relations\Pivot {#5206
          tag_id: 3,
          job_listing_id: 5,
        },
      },
      App\Models\Job {#5544
        id: 7,
        employer_id: 7,
        title: "Locksmith",
        salary: "$4.000.000 COP",
        created_at: "2024-10-15 07:46:02",
        updated_at: "2024-10-15 07:46:02",
        pivot: Illuminate\Database\Eloquent\Relations\Pivot {#5209
          tag_id: 3,
          job_listing_id: 7,
        },
      },
      App\Models\Job {#5208
        id: 8,
        employer_id: 8,
        title: "Coil Winders",
        salary: "$4.000.000 COP",
        created_at: "2024-10-15 07:46:02",
        updated_at: "2024-10-15 07:46:02",
        pivot: Illuminate\Database\Eloquent\Relations\Pivot {#6022
          tag_id: 3,
          job_listing_id: 8,
        },
      },
    ],
  }
```

Me muestra los registros que hemos agregado a la tabla Pivot.

La misma salida nos muestra aplicando el método get.

```shell
$tag->jobs()->get()
= Illuminate\Database\Eloquent\Collection {#5215
    all: [
      App\Models\Job {#6290
        id: 5,
        employer_id: 5,
        title: "Sociologist",
        salary: "$4.000.000 COP",
        created_at: "2024-10-15 07:46:02",
        updated_at: "2024-10-15 07:46:02",
        pivot: Illuminate\Database\Eloquent\Relations\Pivot {#5954
          tag_id: 3,
          job_listing_id: 5,
        },
      },
      App\Models\Job {#6288
        id: 7,
        employer_id: 7,
        title: "Locksmith",
        salary: "$4.000.000 COP",
        created_at: "2024-10-15 07:46:02",
        updated_at: "2024-10-15 07:46:02",
        pivot: Illuminate\Database\Eloquent\Relations\Pivot {#5201
          tag_id: 3,
          job_listing_id: 7,
        },
      },
      App\Models\Job {#5210
        id: 8,
        employer_id: 8,
        title: "Coil Winders",
        salary: "$4.000.000 COP",
        created_at: "2024-10-15 07:46:02",
        updated_at: "2024-10-15 07:46:02",
        pivot: Illuminate\Database\Eloquent\Relations\Pivot {#5952
          tag_id: 3,
          job_listing_id: 8,
        },
      },
    ],
  }
```

U obtener un solo atributo para cada uno de los registros, usando pluck:

```shell
$tag->jobs()->get()->pluck('title')
= Illuminate\Support\Collection {#5220
    all: [
      "Sociologist",
      "Locksmith",
      "Coil Winders",
    ],
  }
```

