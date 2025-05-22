
# Lista de Casos de Prueba para el CRUD de Jobs

## Método `index()`
1. **Solicitud sin parámetros**: Verificar que devuelve todos los trabajos existentes.
   - Método: `GET`
   - URL: `/api/jobs`
   - Esperado: Respuesta `200` con una lista de todos los trabajos.

2. **Solicitud cuando no hay trabajos en la base de datos**: Comprobar que devuelve una lista vacía.
   - Método: `GET`
   - URL: `/api/jobs`
   - Esperado: Respuesta `200` con una lista vacía (`[]`).

3. **Solicitud con una gran cantidad de registros**: Verificar el comportamiento con una base de datos grande.
   - Método: `GET`
   - URL: `/api/jobs`
   - Esperado: Respuesta `200` con todos los trabajos listados correctamente.

4. **Verificar el formato de respuesta**: Asegurarse de que los datos están en formato JSON y contienen los campos correctos (`title`, `salary`, `employer_id`).
   - Método: `GET`
   - URL: `/api/jobs`
   - Esperado: Respuesta `200` con el formato adecuado.

## Método `store()`
1. **Crear un trabajo con todos los campos válidos**: Verificar que el trabajo se crea correctamente.
   - Método: `POST`
   - URL: `/api/jobs`
   - Body:
     ```json
     {
       "title": "Software Developer",
       "salary": "5000 USD",
       "employer_id": 1
     }
     ```
   - Esperado: Respuesta `201` con los detalles del trabajo creado.

2. **Falta de un campo requerido (`title`)**: Verificar la respuesta de error.
   - Método: `POST`
   - URL: `/api/jobs`
   - Body:
     ```json
     {
       "salary": "5000 USD",
       "employer_id": 1
     }
     ```
   - Esperado: Respuesta `422` indicando que `title` es requerido.

3. **`employer_id` no existente**: Intentar crear un trabajo con un `employer_id` inválido.
   - Método: `POST`
   - URL: `/api/jobs`
   - Body:
     ```json
     {
       "title": "Project Manager",
       "salary": "6000 USD",
       "employer_id": 9999
     }
     ```
   - Esperado: Respuesta `422` indicando que el `employer_id` no es válido.

4. **Campo `salary` con valor incorrecto**: Enviar `salary` como un número en lugar de una cadena.
   - Método: `POST`
   - URL: `/api/jobs`
   - Body:
     ```json
     {
       "title": "Data Analyst",
       "salary": 4500,
       "employer_id": 1
     }
     ```
   - Esperado: Respuesta `422` indicando que `salary` debe ser una cadena.

## Método `show()`
1. **Mostrar un trabajo existente**: Verificar que devuelve el trabajo solicitado.
   - Método: `GET`
   - URL: `/api/jobs/1`
   - Esperado: Respuesta `200` con los detalles del trabajo.

2. **Trabajo no existente**: Intentar acceder a un trabajo con un ID que no existe.
   - Método: `GET`
   - URL: `/api/jobs/9999`
   - Esperado: Respuesta `404` con el mensaje "Job not found".

3. **ID no válido**: Probar con un ID que no es un número.
   - Método: `GET`
   - URL: `/api/jobs/abc`
   - Esperado: Respuesta `404` o `400` indicando que el ID no es válido.

4. **Verificar el formato de respuesta**: Comprobar que la respuesta tiene los campos correctos (`title`, `salary`, `employer_id`).
   - Método: `GET`
   - URL: `/api/jobs/1`
   - Esperado: Respuesta `200` con el formato adecuado.

## Método `update()`
1. **Actualizar todos los campos de un trabajo existente**: Verificar que los cambios se realizan correctamente.
   - Método: `PUT`
   - URL: `/api/jobs/1`
   - Body:
     ```json
     {
       "title": "Senior Developer",
       "salary": "7000 USD",
       "employer_id": 1
     }
     ```
   - Esperado: Respuesta `200` con los detalles actualizados.

2. **Actualizar solo algunos campos (`title` y `salary`)**: Verificar que los campos especificados se actualicen y los demás permanezcan igual.
   - Método: `PATCH`
   - URL: `/api/jobs/1`
   - Body:
     ```json
     {
       "title": "Junior Developer",
       "salary": "4000 USD"
     }
     ```
   - Esperado: Respuesta `200` con los detalles actualizados.

3. **Actualizar con un `employer_id` no válido**: Probar un ID de empleador que no existe.
   - Método: `PATCH`
   - URL: `/api/jobs/1`
   - Body:
     ```json
     {
       "employer_id": 9999
     }
     ```
   - Esperado: Respuesta `422` indicando que el `employer_id` no es válido.

4. **Intentar actualizar un trabajo no existente**: Verificar que devuelva un error `404`.
   - Método: `PUT`
   - URL: `/api/jobs/9999`
   - Body:
     ```json
     {
       "title": "Team Lead"
     }
     ```
   - Esperado: Respuesta `404` con el mensaje "Job not found".

## Método `destroy()`
1. **Eliminar un trabajo existente**: Verificar que el trabajo se elimina correctamente.
   - Método: `DELETE`
   - URL: `/api/jobs/1`
   - Esperado: Respuesta `200` con el mensaje "Job deleted successfully".

2. **Intentar eliminar un trabajo que no existe**: Verificar que devuelva un error `404`.
   - Método: `DELETE`
   - URL: `/api/jobs/9999`
   - Esperado: Respuesta `404` con el mensaje "Job not found".

3. **Eliminar un trabajo dos veces**: Verificar que la segunda vez devuelve un `404`.
   - Método: `DELETE`
   - URL: `/api/jobs/1`
   - Esperado: La primera solicitud devuelve `200`, la segunda devuelve `404`.

4. **Verificar la respuesta cuando se elimina un trabajo asociado a otros datos**: Asegurarse de que no haya errores de integridad referencial.
   - Método: `DELETE`
   - URL: `/api/jobs/2`
   - Esperado: Respuesta `200` con el mensaje "Job deleted successfully". No debe haber errores relacionados con datos dependientes.

