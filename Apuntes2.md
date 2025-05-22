
## Directivas

| `v-bind`                                       | `:` seguido del atributo                                                                                                                                                                                                                                                                        |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `v-on`                                         | `@` seguido de                                                                                                                                                                                                                                                                                  |
| <br><input :value="text" @input="onInput"><br> | `v-model='text'` se envía a text lo que se ingrese en el input                                                                                                                                                                                                                                  |
| `v-if`                                         | seguido de la variable que tien                                                                                                                                                                                                                                                                 |
| `v-else`                                       | si lo a                                                                                                                                                                                                                                                                                         |
| `v-else-if`                                    | si lo a                                                                                                                                                                                                                                                                                         |
| `v-for`                                        | seguido de `elemento in elementos` de un array fuente. Se puede agregar el atributo especial `:key` para que teniendo un `elemento.id` mover con precisión cada `<li>` para que coincida con la posición de su correspondiente objeto en el array. Si el atributo que lo identifica es el `id`. |


## `methods`

Las funciones se declaran en methods.

Dentro de un método, podemos acceder a la instancia del componente usando `this`. La instancia del componente expone las propiedades de los datos declarados por `data`. Podemos actualizar el estado del componente mutando estas propiedades.

Los controladores de eventos también pueden utilizar expresiones en línea, y pueden simplificar tareas comunes con modificadores.

# Vinculación de Formularios

Usando `v-bind` y `v-on` juntos, podemos crear enlaces bidireccionales en los elementos de entrada del formulario:



```vue
<input :value="text" @input="onInput">
```


```js
methods: {
  onInput(e) {
    // un manejador v-on recibe el evento nativo
    // del DOM como argumento.
    this.text = e.target.value
  }
}
```


Prueba a escribir en la caja de entrada; deberías ver el texto en `<p>` actualizándose a medida que escribes.

Para simplificar los enlaces bidireccionales, Vue proporciona una directiva, `v-model`, que es esencialmente un azúcar sintáctico para lo anterior:


```vue
<input v-model="text">
```

`v-model` sincroniza automáticamente el valor de `<input>` con el estado vinculado, por lo que ya no es necesario utilizar un controlador de eventos para ello.

`v-model` no sólo funciona con entradas de texto, sino también con otros tipos de entradas como checkboxes, radio buttons y select desplegables. Veremos más detalles en la [Guía - Vinculación de Entradas de Formularios](https://vue3-spanish-docs.netlify.app/guide/essentials/forms.html).

A continuación, trata de refactorizar el código para utilizar `v-model` en su lugar.

```vue
<script>
export default {
  data() {
    return {
      text: ''
    }
  }
}
</script>

<template>
  <input v-model="text" placeholder="Escribe aquí" />
  <p>{{ text }}</p>
</template>
```



# Renderizado Condicional

Podemos utilizar la directiva `v-if` para renderizar condicionalmente un elemento:

template

``` vue
<h1 v-if="awesome">¡Vue es increíble!</h1>
```

Este elemento `<h1>` se renderizará sólo si el valor de `awesome` es [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy). En caso de que `awesome` cambie a un valor [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy), será removido del DOM.

También podemos utilizar `v-else` y `v-else-if` para denotar otras ramas de la condición:

```vue
<h1 v-if="awesome">¡Vue es increíble!</h1>
<h1 v-else>Oh no 😢</h1>
```

En este momento, la demo está mostrando los dos `<h1>` al mismo tiempo, y el botón no hace nada. Prueba a añadirles las directivas `v-if` y `v-else`, e implementa el método `toggle()` para que podamos usar el botón para alternar entre ellas.

Más detalles sobre `v-if`: [Guía - Renderizado Condicional](https://vue3-spanish-docs.netlify.app/guide/essentials/conditional.html)

```vue
<script>
export default {
  data() {
    return {
      awesome: true
    }
  },
  methods: {
    toggle() {
      this.awesome = !this.awesome
    }
  }
}
</script>

<template>
  <button @click="toggle">alternar</button>
  <h1 v-if="awesome">¡Vue es increíble!</h1>
  <h1 v-else>Oh no 😢</h1>
</template>
```

# Renderizado de Listas

Podemos utilizar la directiva `v-for` para renderizar una lista de elementos basada en un array fuente:


``` vue
<ul>
  <li v-for="todo in todos" :key="todo.id">
    {{ todo.text }}
  </li>
</ul>
```

Aquí `todo` es una variable local que representa el elemento de la matriz que se está iterando actualmente. Solo es accesible en o dentro del elemento `v-for`, similar al ámbito de una función.

Observa que también estamos dando a cada objeto `todo` un `id` único, y lo vinculamos como el [atributo especial `key`](https://vue3-spanish-docs.netlify.app/api/built-in-special-attributes.html#key) para cada `<li>`. La `key` (_clave_, en español) permite a Vue mover con precisión cada `<li>` para que coincida con la posición de su correspondiente objeto en el array.

Hay dos maneras de actualizar la lista:

1. Llamar a los [métodos de mutación](https://stackoverflow.com/questions/9009879/which-javascript-array-functions-are-mutating) en el array fuente:
    
    
    ```js
    this.todos.push(newTodo)
    ```
    
2. Reemplazar el array por uno nuevo:
        
    ```js
    this.todos = this.todos.filter(/* ... */)
    ```
    

Aquí tenemos una simple lista de tareas. Trata de implementar la lógica de los métodos `addTodo()` y `removeTodo()` para que funcione!

Más detalles sobre `v-for`: [Guía - Renderizado de Listas](https://vue3-spanish-docs.netlify.app/guide/essentials/list.html)

```vue
<script>
// dar a cada tarea (todo) un id único
let id = 0

export default {
  data() {
    return {
      newTodo: '',
      todos: [
        { id: id++, text: 'Aprender HTML' },
        { id: id++, text: 'Aprender JavaScript' },
        { id: id++, text: 'Aprender Vue' }
      ]
    }
  },
  methods: {
    addTodo() {
      this.todos.push({ id: id++, text: this.newTodo })
      this.newTodo = ''
    },
    removeTodo(todo) {
      this.todos = this.todos.filter((t) => t !== todo)
    }
  }
}
</script>

<template>
  <form @submit.prevent="addTodo">
    <input v-model="newTodo">
    <button>Añadir Tarea</button>
  </form>
  <ul>
    <li v-for="todo in todos" :key="todo.id">
      {{ todo.text }}
      <button @click="removeTodo(todo)">X</button>
    </li>
  </ul>
</template>
```

# Propiedad Computada

Sigamos desarrollando la lista de tareas del último paso. Para ello, ya hemos añadido una funcionalidad de conmutación a cada una de las tareas. Ello se consigue añadiendo una propiedad `done` a cada objeto todo, y utilizando `v-model` para vincularlo con una casilla de verificación:



```vue
<li v-for="todo in todos">
  <input type="checkbox" v-model="todo.done">
  ...
</li>
```

La siguiente mejora que podemos añadir es ser capaz de ocultar las tareas completadas. Ya tenemos un botón que conmuta el estado `hideCompleted`. Pero, ¿cómo renderizamos diferentes elementos de la lista en función de ese estado?

Te presentamos a la [propiedad computada](https://vue3-spanish-docs.netlify.app/guide/essentials/computed.html). Podemos declarar una propiedad que se computa reactivamente a partir de otras propiedades utilizando la opción `computed`:


```js
export default {
  // ...
  computed: {
    filteredTodos() {
      // se devuelven las tareas filtradas basándose en `this.hideCompleted`.
    }
  }
}
```


```diff
- <li v-for="todo in todos">
+ <li v-for="todo in filteredTodos">
```

Una propiedad computada rastrea otros estados reactivos utilizados en su cómputo como dependencias. Almacena el resultado en caché y lo actualiza automáticamente cuando sus dependencias cambian.

Ahora, ¡intenta añadir la propiedad computada `filteredTodos` e implementa su lógica de cálculo! En caso de que se implemente correctamente, si se verifica una tarea, se ocultará instantáneamente cuando se oculten los ítems completados.


```vue
<script>
let id = 0

export default {
  data() {
    return {
      newTodo: '',
      hideCompleted: false,
      todos: [
        { id: id++, text: 'Aprender HTML', done: true },
        { id: id++, text: 'Aprender JavaScript', done: true },
        { id: id++, text: 'Aprender Vue', done: false }
      ]
    }
  },
  computed: {
    filteredTodos() {
      return this.hideCompleted
        ? this.todos.filter((t) => !t.done)
        : this.todos
    }
  },
  methods: {
    addTodo() {
      this.todos.push({ id: id++, text: this.newTodo, done: false })
      this.newTodo = ''
    },
    removeTodo(todo) {
      this.todos = this.todos.filter((t) => t !== todo)
    }
  }
}
</script>

<template>
  <form @submit.prevent="addTodo">
    <input v-model="newTodo" />
    <button>Añadir Tarea</button>
  </form>
  <ul>
    <li v-for="todo in filteredTodos" :key="todo.id">
      <input type="checkbox" v-model="todo.done" />
      <span :class="{ done: todo.done }">{{ todo.text }}</span>
      <button @click="removeTodo(todo)">X</button>
    </li>
  </ul>
  <button @click="hideCompleted = !hideCompleted">
    {{ hideCompleted ? 'Mostrar todo' : 'Ocultar completada' }}
  </button>
</template>

<style>
.done {
  text-decoration: line-through;
}
</style>
```

# Ciclo de Vida y Refs de la Plantilla

Hasta ahora, Vue ha manejado todas las actualizaciones del DOM por nosotros, gracias a la reactividad y al renderizado declarativo. Pero, inevitablemente, habrá casos en los que necesitemos trabajar manualmente con el DOM.

Para ello podemos solicitar una **template ref**; es decir, una referencia a un elemento de la plantilla, utilizando el [atributo especial `ref`](https://vue3-spanish-docs.netlify.app/api/built-in-special-attributes.html#ref):



```vue
<p ref="pElementRef">hola</p>
```

El elemento será expuesto en `this.$refs` como `this.$refs.pElementRef`. Sin embargo, sólo se puede acceder a él después de que el componente esté **montado**.

Para ejecutar el código después del montaje, podemos utilizar la opción `mounted`:


```js
export default {
  mounted() {
    // el componente está montado.
  }
}
```

Esto se denomina "**hook del ciclo de vida**"; este nos permite registrar un callback para ser llamado en ciertos momentos del ciclo de vida del componente. Hay otros hooks como `created` y `updated`. Consulta el [Diagrama del Ciclo de Vida](https://vue3-spanish-docs.netlify.app/guide/essentials/lifecycle.html#diagrama-del-ciclo-de-vida) para obtener más detalles.

A continuación, intenta añadir un hook `mounted`, accede a `<p>` a través de `this.$refs.pElementRef`, y realiza algunas operaciones directas en el DOM (por ejemplo, cambiar su `textContent`).

```vue
<script>
export default {
  mounted() {
    this.$refs.pElementRef.textContent = '¡montado!'
  }
}
</script>

<template>
  <p ref="pElementRef">hola</p>
</template>
```


# Watchers

Hay ocasiones en las que podemos necesitar la ejecución de "efectos secundarios" de forma reactiva; por ejemplo, registrar un número en la consola cada vez que esta cambie. Esto lo podemos conseguir con los watchers:


```js
export default {
  data() {
    return {
      count: 0
    }
  },
  watch: {
    count(newCount) {
      // sí, console.log() es un efecto secundario
      console.log(`La nueva cuenta es: ${newCount}`)
    }
  }
}
```

En este caso, estamos utilizando la opción `watch` para observar los cambios en la propiedad `count`. El callback watch es llamado cuando `count` cambia, y recibe el nuevo valor como argumento. Para más detalles, consulta la [Guía - Watchers](https://vue3-spanish-docs.netlify.app/guide/essentials/watchers.html).

Un ejemplo más práctico que registrar en la consola sería recuperar nuevos datos cuando un ID cambia. El código que tenemos está recuperando datos de tareas desde una API de prueba cuando se monta el componente. Hay también un botón que incrementa el ID de la tarea que debe ser recuperada. Intenta implementar un watcher que recupere una nueva tarea cuando se pulse el botón.

```vue
<script>
export default {
  data() {
    return {
      todoId: 1,
      todoData: null
    }
  },
  methods: {
    async fetchData() {
      this.todoData = null
      const res = await fetch(
        `https://jsonplaceholder.typicode.com/todos/${this.todoId}`
      )
      this.todoData = await res.json()
    }
  },
  mounted() {
    this.fetchData()
  },
  watch: {
    todoId() {
      this.fetchData()
    }
  }
}
</script>

<template>
  <p>Id de la tarea: {{ todoId }}</p>
  <button @click="todoId++ :disabled="!todoData">Recuperar la próxima tarea</button>
  <p v-if="!todoData">Cargando...</p>
  <pre v-else>{{ todoData }}</pre>
</template>
```

# Componentes

Hasta ahora, sólo hemos trabajado con un único componente. Las verdaderas aplicaciones Vue se crean usualmente con componentes anidados.

Un componente padre puede renderizar otro componente en su plantilla como un componente hijo. Para usar un componente hijo, primero tenemos que importarlo:


```js
import ChildComp from './ChildComp.vue'

export default {
  components: {
    ChildComp
  }
}
```

También necesitamos registrar el componente usando la opción `components`. En este caso estamos utilizando la abreviatura de la propiedad del objeto para registrar el componente `ChildComp` bajo la key `ChildComp`.

Entonces, podemos utilizar el componente en la plantilla como:


```vue
<ChildComp />
```

Ahora pruébalo tú mismo: importa el componente hijo y renderízalo en la plantilla.

```vue
<!-- App.vue -->
<script>
import ChildComp from './ChildComp.vue'

export default {
  components: {
    ChildComp
  }
}
</script>

<template>
  <ChildComp />
</template>
```

```vue
<!-- ChildComp.vue -->
<template>
  <h2>¡Un Componente Hijo!</h2>
</template>
```


# Props

Un componente hijo puede aceptar entradas del padre a través de **props**. En primer lugar, es necesario declarar las props que recibe:

```js
// en el componente hijo
export default {
  props: {
    msg: String
  }
}
```

Una vez declarada, la prop `msg` queda expuesta en el `this` y puede ser utilizada en la plantilla del componente hijo.

El padre puede pasar la prop al hijo de la misma forma que los atributos. Para pasar un valor dinámico, también se puede utilizar la sintaxis `v-bind`:

```vue
<ChildComp :msg="greeting" />
```

Ahora pruébalo tú mismo en el editor.

```vue
<!-- App.vue -->
<script>
import ChildComp from './ChildComp.vue'

export default {
  components: {
    ChildComp
  },
  data() {
    return {
      greeting: 'Hola desde el padre'
    }
  }
}
</script>

<template>
  <ChildComp :msg="greeting" />
</template>
```


```js
// ChildComp.vue
<script>
export default {
  props: {
    msg: String
  }
}
</script>

<template>
  <h2>{{ msg || 'Todavía no se ha pasado ninguna prop' }}</h2>
</template>
```


# Emits

Además de recibir props, un componente hijo puede también emitir eventos hacia el padre:



```js
export default {
  // declara los eventos a emitir
  emits: ['response'],
  created() {
    // emit con argumento
    this.$emit('response', 'hola desde el hijo')
  }
}
```

El primer argumento para `this.$emit()` es el nombre del evento. Cualquier argumento adicional se pasa al escuchador del evento.

El padre puede escuchar los eventos emitidos por el hijo usando `v-on`; en el siguiente caso el manejador recibe el argumento extra de la llamada de emisión del hijo y lo asigna al estado local:

```vue
<ChildComp @response="(msg) => childMsg = msg" />
```

Ahora pruébalo tú mismo en el editor.

```vue
<!-- App.vue-->
<script>
import ChildComp from './ChildComp.vue'

export default {
  components: {
    ChildComp
  },
  data() {
    return {
      childMsg: 'Ningún mensaje del hijo aún'
    }
  }
}
</script>

<template>
  <ChildComp @response="(msg) => childMsg = msg" />
  <p>{{ childMsg }}</p>
</template>
```

```vue
<!-- ChildComp.vue-->
<script>
export default {
  emits: ['response'],
  created() {
    this.$emit('response', 'hola desde el hijo')
  }
}
</script>

<template>
  <h2>Componente hijo</h2>
</template>
```

# Slots

Además de pasar datos a través de props, el componente padre también puede pasar fragmentos de plantilla al hijo a través de **slots**:


```vue
<ChildComp>
  ¡Este es algún contenido del slot!
</ChildComp>
```

En el componente hijo, se puede renderizar el contenido del slot del padre utilizando el elemento `<slot>` como punto de salida:


```vue
<!-- en la plantilla hija -->
<slot />
```

El contenido dentro del punto de salida `<slot>` será tratado como contenido "fallback" (_de reserva_): se mostrará si el padre no pasó ningún contenido al slot:


```vue
<slot>Contenido fallback</slot>
```

En este momento no estamos pasando ningún contenido del slot a `<ChildComp>`, por lo que deberías ver el contenido fallback. Proporcionemos algún contenido del slot al hijo mientras hacemos uso del estado `msg` del padre.

```vue
<!-- App.vue-->
<script>
import ChildComp from './ChildComp.vue'

export default {
  components: {
    ChildComp
  },
  data() {
    return {
      msg: 'desde el padre'
    }
  }
}
</script>

<template>
  <ChildComp>Mensaje: {{ msg }}</ChildComp>
</template>
```

```vue
<!-- ChildComp.vue-->
<template>
  <slot>Contenido fallback</slot>
</template>
```


# ¡Lo lograste!

¡Has terminado el tutorial!

En este punto, deberías tener una buena idea de cómo es trabajar con Vue. Sin embargo, hemos cubierto muchas cosas muy rápido y hemos pasado por alto los detalles, así que ¡sigue aprendiendo! Como siguiente paso, puedes:

- Configurar un proyecto real de Vue en tu máquina siguiendo el [Inicio Rápido](https://vue3-spanish-docs.netlify.app/guide/quick-start.html).
    
- Revisar la [Guía principal](https://vue3-spanish-docs.netlify.app/guide/essentials/application.html), que cubre todos los temas que hemos aprendido hasta ahora con mayor detalle, y mucho más.
    
- Consulta algunos [Ejemplos](https://vue3-spanish-docs.netlify.app/examples/) más prácticos.
    

¡Estamos deseando ver qué es lo próximo que construyes!

```vue
<script>
import JSConfetti from 'js-confetti'

const confetti = new JSConfetti()

export default {
  mounted() {
    this.showConfetti()
  },
  methods: {
    showConfetti() {
      confetti.addConfetti()
    }
  }
}
</script>

<template>
  <h1 @click="showConfetti">🎉 ¡Felicitaciones!</h1>
</template>

<style>
h1 {
  text-align: center;
  cursor: pointer;
  margin-top: 3em;
}
</style>
```

