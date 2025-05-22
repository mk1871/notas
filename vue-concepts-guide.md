# Guía de Conceptos Fundamentales de Vue.js
## Con ejemplos del Job Board Project

### 1. Componentes

Los componentes son bloques de construcción reutilizables en Vue.js. Cada componente encapsula su propia estructura (template), lógica (script) y estilos. Piensa en ellos como piezas de LEGO que puedes combinar para construir tu aplicación.

#### Ejemplos de tu proyecto:

1. **Componente Base Simple (FormLabel.vue)**
```vue
export default {
  name: 'FormLabel',
  props: {
    className: {
      type: String,
      default: ''
    },
    required: {
      type: Boolean,
      default: false
    }
  }
}

<template>
  <label :class="['block text-sm font-medium leading-6 text-gray-900', className]">
    <slot></slot>
    <span v-if="required" class="text-red-500 ml-1">*</span>
  </label>
</template>
```
Este componente es simple pero versátil: crea etiquetas de formulario con la opción de marcarlas como requeridas.

2. **Componente con Estado (AlertMessage.vue)**
```vue
export default {
  name: 'AlertMessage',
  props: {
    type: {
      type: String,
      default: 'success',
      validator: value => ['success', 'error', 'warning', 'info'].includes(value)
    },
    message: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      isVisible: true
    }
  }
}
```
Este componente maneja su propio estado (isVisible) y acepta props para personalizar el tipo de alerta.

3. **Componente Complejo (BaseLayout.vue)**
```vue
export default {
  name: 'BaseLayout',
  data() {
    return {
      isOpen: false,
      user: null,
      isAuthenticated: false
    }
  },
  created() {
    window.addEventListener('auth:login', this.checkAuthStatus)
    window.addEventListener('auth:logout', this.checkAuthStatus)
  }
}
```
Este componente actúa como estructura base de la aplicación, manejando navegación y estado de autenticación.

### 2. Directivas

Las directivas son instrucciones especiales que le dicen a Vue cómo manipular el DOM. Se reconocen por el prefijo `v-`.

#### Ejemplos de tu proyecto:

1. **v-if/v-else (JobsView.vue)**
```vue
<loading-spinner
  v-if="isLoading"
  label="Cargando trabajos..."
  class="py-8"
/>

<div
  v-else-if="!jobs || jobs.length === 0"
  class="text-center py-12"
>
  <h3>No hay trabajos</h3>
</div>

<div v-else>
  <!-- Lista de trabajos -->
</div>
```
Este código muestra diferentes elementos según el estado de carga y disponibilidad de datos.

2. **v-model (JobCreateView.vue)**
```vue
<form-input
  v-model="form.title"
  id="title"
  name="title"
  type="text"
  ref="titleInput"
  placeholder="Ej: Desarrollador Senior"
  required
  :disabled="isSubmitting"
/>
```
v-model crea un enlace bidireccional entre el input y la propiedad del formulario.

3. **v-for (BasePagination.vue)**
```vue
<template v-for="page in pageNumbers" :key="page">
  <button
    v-if="page !== '...'"
    @click="changePage(page)"
    :class="[isCurrentPage(page) ? 'active-page' : 'inactive-page']"
  >
    {{ page }}
  </button>
</template>
```
v-for itera sobre una colección para renderizar elementos múltiples veces.

### 3. Eventos

Los eventos permiten la comunicación entre componentes y la respuesta a interacciones del usuario. Se pueden emitir eventos personalizados y escuchar eventos nativos.

#### Ejemplos de tu proyecto:

1. **Eventos del Sistema (BaseLayout.vue)**
```javascript
created() {
  window.addEventListener('auth:login', this.checkAuthStatus)
  window.addEventListener('auth:logout', this.checkAuthStatus)
},
beforeUnmount() {
  window.removeEventListener('auth:login', this.checkAuthStatus)
  window.removeEventListener('auth:logout', this.checkAuthStatus)
}
```
Este código escucha eventos personalizados del sistema de autenticación.

2. **Eventos de Usuario (JobEditView.vue)**
```javascript
async handleDelete() {
  if (!confirm('¿Estás seguro de eliminar este trabajo?')) {
    return
  }
  
  this.isDeleting = true
  try {
    const { data } = await axios.delete(`/jobs/${this.$route.params.id}`)
    window.dispatchEvent(new CustomEvent('show-alert', {
      detail: {
        type: 'success',
        message: data.message
      }
    }))
  } catch (error) {
    // Manejo de error
  }
}
```
Este código maneja el evento de click en el botón de eliminar y emite un evento personalizado.

3. **Eventos de Formulario (JobCreateView.vue)**
```vue
<form @submit.prevent="handleSubmit" novalidate>
  <!-- Contenido del formulario -->
</form>
```
Previene el comportamiento por defecto del formulario y maneja la submission con un método personalizado.

### 4. Comunicación entre Componentes

La comunicación entre componentes en Vue se realiza principalmente a través de props (padre a hijo) y eventos (hijo a padre).

#### Ejemplos de tu proyecto:

1. **Props Básicas (FormInput.vue)**
```javascript
props: {
  modelValue: {
    type: [String, Number],
    default: '',
  },
  wrapperClass: {
    type: String,
    default: '',
  },
  autofocus: {
    type: Boolean,
    default: false
  }
}
```
Este componente acepta props para personalizar su comportamiento y apariencia.

2. **Evento Personalizado (AlertMessage.vue)**
```javascript
methods: {
  dismiss() {
    this.isVisible = false
    this.$emit('dismiss')
  }
}
```
El componente emite un evento 'dismiss' que el padre puede escuchar.

3. **Slots (BaseLayout.vue)**
```vue
<template>
  <header class="bg-white shadow">
    <div class="mx-auto max-w-7xl px-4 py-6">
      <slot name="heading"></slot>
    </div>
  </header>
  <main>
    <slot></slot>
  </main>
</template>
```
Los slots permiten que los componentes padres inyecten contenido en lugares específicos.

### Puntos Clave a Destacar

1. **Arquitectura Componente-Based**: Tu aplicación está bien estructurada con componentes reutilizables que siguen el principio de responsabilidad única.

2. **Manejo de Estado**: Cada componente maneja su propio estado local cuando es necesario, y usas eventos personalizados para comunicación entre componentes no relacionados directamente.

3. **Comunicación Robusta**: Has implementado un sistema de comunicación entre componentes que combina props, eventos y eventos globales de manera efectiva.

4. **UX Considerada**: Tus componentes manejan estados de carga, errores y éxito de manera consistente en toda la aplicación.

Este proyecto demuestra una buena comprensión de los conceptos fundamentales de Vue.js y su aplicación práctica en una aplicación del mundo real.
