### **¿Qué es `git merge`?**

`git merge` es un comando en Git que combina los cambios de dos ramas. A diferencia de `git rebase`, que reestructura el historial de commits, `git merge` mantiene ambos historiales de las ramas involucradas y crea un commit adicional que une las dos ramas. Es útil para integrar el trabajo de una rama en otra sin alterar el historial original de ninguna de las ramas.

- **Cuándo usarlo:** Utilizas `git merge` cuando quieres combinar el trabajo de diferentes ramas y estás de acuerdo con que el historial refleje el proceso de fusión, lo que puede incluir commits de merge adicionales.

### **Ejemplo de `git merge`:**

Imagina que tienes dos ramas, `main` y `feature`. Estás trabajando en la rama `feature` con algunos cambios y ahora quieres integrar esos cambios en la rama `main`.

### **Pasos para hacer un merge:**

1. **Ve a la rama donde quieres hacer la fusión (por lo general, `main`):**

   ```bash
   git checkout main
   ```

2. **Fusiona la rama `feature` en `main`:**

   ```bash
   git merge feature
   ```

   Este comando combinará los commits de la rama `feature` en `main`. Si no hay conflictos, Git simplemente agregará los cambios de `feature` a `main` y creará un nuevo commit de merge que unirá ambas ramas.

### **Ejemplo de cómo cambia el historial:**

- **Antes del merge:**

   ```plaintext
   main:    A -- B -- C
                \
   feature:      D -- E -- F
   ```

   Aquí, la rama `main` tiene los commits `A`, `B`, y `C`, mientras que la rama `feature` tiene sus propios commits `D`, `E`, y `F`.

- **Después del merge:**

   ```plaintext
   main:    A -- B -- C ----------- M
                \                 /
   feature:      D -- E -- F -----
   ```

   El commit `M` es el **commit de merge**, que une los historiales de `main` y `feature`. El historial de `main` ahora contiene los cambios de `feature`, y ambos historiales quedan preservados.

### **Posibilidad de conflictos:**

Si durante el merge hay conflictos (por ejemplo, si has cambiado algo en `main` que también se ha cambiado en `feature`), Git detendrá el proceso de merge y te pedirá que resuelvas los conflictos manualmente. Similar al proceso de rebase, deberás:

1. **Abrir los archivos en conflicto y resolver los cambios manualmente.**
2. **Añadir los archivos después de resolver los conflictos:**

   ```bash
   git add archivo-en-conflicto.txt
   ```

3. **Finalizar el merge:**

   ```bash
   git commit
   ```

   Este commit resolverá el merge y unirá ambas ramas.

### **Tipos de merge:**

1. **Fast-forward merge:** Como mencionamos anteriormente, este tipo de merge ocurre cuando la rama de destino no tiene commits nuevos desde que la rama fuente se bifurcó. En este caso, Git simplemente avanza el puntero de la rama de destino, sin crear un commit de merge. Ocurre automáticamente si no hay cambios divergentes.
   
2. **Merge con commit:** Si ambas ramas han tenido commits desde la bifurcación, Git creará un commit de merge para combinar los cambios.

### **Ejemplo completo de `git merge`:**

1. Estás trabajando en `feature` y has hecho algunos commits:

   ```bash
   git checkout feature
   git commit -m "Cambios en feature"
   ```

2. Cambias a `main` para fusionar `feature` en ella:

   ```bash
   git checkout main
   ```

3. Haces el merge:

   ```bash
   git merge feature
   ```

4. Si no hay conflictos, Git combinará los cambios y creará un nuevo commit de merge. Si hay conflictos, tendrás que resolverlos manualmente, hacer `git add` de los archivos en conflicto, y luego realizar un `git commit` para completar la fusión.

---

### **Resumen del flujo de `git merge`:**

1. **Asegúrate de estar en la rama de destino:**
   ```bash
   git checkout main
   ```

2. **Fusiona la rama fuente en la rama de destino:**
   ```bash
   git merge feature
   ```

3. **Resuelve conflictos si es necesario, añade los archivos corregidos y termina el merge:**
   ```bash
   git add archivo-en-conflicto.txt
   git commit
   ```

Con este ejemplo, ahora puedes entender claramente cómo funciona `git merge`, cómo mantiene ambos historiales de ramas y cómo resuelve cualquier conflicto que surja durante la fusión.