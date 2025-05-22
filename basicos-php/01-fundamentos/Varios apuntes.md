
Para usar directamente la variable dentro de la cadena sin usar puntos las comillas que envuelven deben ser dobles. 

```php
echo "$greeting Mundo";
```


Esto: 
```php
<?php echo $message; ?>
```

Es igual a esto:

```php
<?= $message ?>
```


## Arreglos, foreach

```php
<?php  

$books = [  
    "Libro 1", "Libro 2", "Libro 3"  
];  
?>  
  
<ul>  
    <?php foreach ($books as $book): ?>  
        <li> <?= $book ?></li>  
    <?php endforeach; ?>  
</ul>
```


