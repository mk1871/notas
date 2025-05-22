

- Asegurarse de tener instalado y activado Tailwind
- Asegurarse de tener instalado y activado Prettier

## Instalar tailwind
Install `@tailwindcss/vite` and its peer dependencies via npm.

```
npm install tailwindcss @tailwindcss/vite
```

## Configurar Vite
Add the `@tailwindcss/vite` plugin to your Vite configuration.

```typescript
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
	plugins: [    
		tailwindcss(),    
		// …  
	],
})
```

## Importar Tailwind CSS

Add an `@import` to `./resources/css/app.css` that imports Tailwind CSS. Additionally, tell Tailwind CSS to scan your `resources/views` directory for utilities.

```css
@import "tailwindcss";
@source "../views";
```

## Tailwind prettier

To get started, install `prettier-plugin-tailwindcss` as a dev-dependency:

```shell
npm install -D prettier prettier-plugin-tailwindcss
```

Then add the plugin to your [Prettier configuration](https://prettier.io/docs/en/configuration.html):

```js
// .prettierrc
{
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

