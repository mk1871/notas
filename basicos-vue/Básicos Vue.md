
## Reactividad con Vue

`v-model`  va en dos vías, actualizando el valor en la data.
`mounted()` se ejecuta cuando el componente se cargue.
`v-bind` se abrevia con `:` 
`methods` es para crear los métodos, valga la redundancia.
`v-on` para eventos, se abrevia con `@`

## Separando el `app`

```Javascript
let app = {  
    data() {  
        return {  
             
        }  
    }  
}  
  
Vue.createApp({app}).mount('#app');
```

## `v-for`

```html
<div id="app">  
    <section>
	    <h2 class="font-bold mb-2">Tareas</h2>  
        <ul>
	        <li v-for="assignment in assignments">  
                <label>                    {{ assignment.name }}  
                    <input type="checkbox" v-model="assignment.complete">  
                </label>            </li>        </ul>    </section>  
</div>  
<script>  
    let app = {  
        data() {  
            return {  
                assignments: [  
                    { name: 'Terminar el proyecto', complete: false },  
                    { name: 'Leer sobre Pinia', complete: false },  
                    { name: 'Empezar segundo mes', complete: false },  
                ]  
            }  
        }  
    };  
  
    Vue.createApp(app).mount('#app');  
</script>
```




`v-show` mostrar si se cumple con x condición, parecido al `v-if`

## Propiedades computadas

Funciones que rastrean el estado de una variable. En lugar de ponerlas en el código del HTML, las agrupamos en el objeto `computed`

