
# Crear el proyecto

## Instalar lo básico

```shell
npm create vue@latest
```

Decir sí a la pregunta de si desea crear create-vue@...

Seguir las instrucciones que da la terminal y seleccionar las opciones, por ejemplo, usar Vue Route.

Para el caso actual: 

```shell
  cd job-management
  npm install
  npm run format
  npm run dev
```


## Configurar Tailwind CSS

1. Instalar las dependencias necesarias:

```shell
npm install -D tailwindcss postcss autoprefixer
```

2. Generar el archivo de configuración de Tailwind:

```shell
npx tailwindcss init -p
```

3. En el archivo `tailwind.config.js` que se creó, configurar los archivos que Tailwind debe analizar:

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{vue,js,ts,jsx,tsx}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

4. En el CSS principal (normalmente `src/style.css` o `src/assets/main.css`), agregar las directivas de Tailwind:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

5. Verificar que se está importando el archivo CSS en el `main.js`:

```js
import './assets/main.css'
```

Ya estamos listos para iniciar a configurar el proyecto, sus rutas, vistas, componentes.

