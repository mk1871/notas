
# Guía Completa: Configurar Base de Datos MariaDB en Fedora y Usarla con DBeaver

Esta guía detalla el proceso completo para instalar y configurar un servidor MariaDB en Fedora, crear una base de datos y un usuario dedicado, y conectar DBeaver a esa base de datos.

---

## Entendiendo Usuarios y Contraseñas

Es crucial distinguir entre los diferentes tipos de usuarios y contraseñas involucrados:

* **Contraseña de Root del Sistema:** La contraseña de tu usuario administrador en Fedora (usada con `sudo`). La necesitas para instalar software a nivel de sistema e iniciar/detener servicios. **Nunca la uses dentro de DBeaver ni para acceder a la base de datos.**
* **Contraseña de Root de MariaDB:** La contraseña del superusuario *dentro del servidor de base de datos MariaDB*. La usas para tareas administrativas del servidor (crear bases de datos, usuarios, etc.). **No se recomienda usar esta cuenta para el trabajo diario con tus aplicaciones o conexiones regulares de DBeaver.**
* **Contraseña de Usuario Propio (Usuario de Base de Datos dedicado):** Una cuenta que tú creas *dentro de MariaDB* con permisos limitados, generalmente solo sobre una o varias bases de datos específicas. Esta es la cuenta **recomendada para usar en DBeaver** y en tus aplicaciones para el trabajo diario.

**Recomendaciones Clave:**

1.  Ejecuta siempre `mariadb-secure-installation`.
2.  Establece contraseñas fuertes y únicas para el root de MariaDB y para cada usuario dedicado.
3.  Usa *siempre* usuarios dedicados con permisos limitados para tus conexiones diarias en DBeaver y aplicaciones.

---

## Proceso de Configuración Paso a Paso

### Paso 1: Instalar el servidor MariaDB

* **Objetivo:** Instalar el software del servidor de base de datos MariaDB y el cliente de línea de comandos en tu sistema Fedora.
* **Terminal:**
    ```bash
    sudo dnf install mariadb-server mariadb
    ```
    * Se te pedirá la **contraseña de root de tu sistema Fedora** debido al uso de `sudo`.
* **DBeaver:** No es posible instalar el servidor MariaDB desde DBeaver. Es una tarea a nivel de sistema operativo.

### Paso 2: Iniciar y Habilitar el servicio MariaDB

* **Objetivo:** Asegurarse de que el servidor MariaDB se inicie ahora y automáticamente cada vez que arranque tu sistema.
* **Terminal:**
    ```bash
    sudo systemctl start mariadb
    sudo systemctl enable mariadb
    ```
    * Necesitas `sudo` para gestionar servicios del sistema. Es posible que se te pida la **contraseña de root de tu sistema**.
* **DBeaver:** No es posible gestionar servicios del sistema desde DBeaver.

### Paso 3: Asegurar la instalación de MariaDB (¡CRUCIAL!)

* **Objetivo:** Configurar una contraseña segura para el usuario `root` de MariaDB, eliminar configuraciones por defecto inseguras y restringir el acceso.
* **Terminal:**
    ```bash
    sudo mariadb-secure-installation
    ```
    * Necesitas `sudo` para ejecutar este script de configuración de seguridad del servidor.
    * El script te guiará:
        * Te pedirá la contraseña actual de root de MariaDB (generalmente vacía al principio; simplemente presiona Enter).
        * Te preguntará si quieres configurar una contraseña para root de MariaDB. Responde `Y` e ingresa una **contraseña fuerte y única para el usuario `root` de MariaDB**. **Guarda esta contraseña en un lugar seguro.**
        * Te hará varias preguntas sobre eliminar usuarios anónimos, deshabilitar el inicio de sesión remoto para root, eliminar la base de datos de prueba y recargar las tablas de privilegios. Responde `Y` a estas preguntas para mejorar la seguridad.
    * **Recuerda:** La contraseña que estableces aquí es para el usuario `root` del *servidor de base de datos*, NO para el root de tu sistema Fedora.
* **DBeaver:** No es posible ejecutar este script de configuración inicial desde DBeaver.

### Paso 4: Conectarse al servidor MariaDB (Inicialmente como root de MariaDB para administración)

Ahora te conectarás al servidor MariaDB con el usuario `root` de MariaDB para poder realizar tareas administrativas como crear bases de datos y usuarios.

* **Terminal (usando el cliente de línea de comandos de MariaDB):**
    ```bash
    mariadb -u root -p
    ```
    * Presiona Enter y se te pedirá la **contraseña del usuario `root` de MariaDB** que configuraste en el Paso 3. Si la ingresas correctamente, verás el prompt de MariaDB (`MariaDB [(none)]>`).
* **DBeaver (Interfaz Gráfica):**
    1.  Abre DBeaver.
    2.  Haz clic en el icono **"New Database Connection"** (generalmente un enchufe o un signo `+`) en la barra de herramientas. También puedes ir al menú `Database -> New Database Connection`.
    3.  En la ventana emergente "Connect to a database", busca y selecciona **"MariaDB"**.
    4.  Haz clic en **"Next >"**.
    5.  En la pestaña **"Main"**:
        * **Server Host:** Ingresa `localhost`.
        * **Port:** Ingresa `3306`.
        * **Database:** Deja este campo **vacío**.
        * **User name:** Ingresa `root`.
        * **Password:** Ingresa la **contraseña del usuario `root` de MariaDB**.
    6.  (Opcional) En la pestaña **"General"**, puedes cambiar el "Connection name" a algo como `MariaDB Local (Admin Root)`.
    7.  Haz clic en **"Test Connection..."**. Debería confirmar la conexión exitosa.
    8.  Haz clic en **"Finish"**.
    9.  Verás la nueva conexión en el **"Database Navigator"**.

### Paso 5: Crear una nueva base de datos (esquema)

Crearás un contenedor vacío para tus tablas y datos dentro del servidor MariaDB.

* **Terminal (conectado al prompt de MariaDB como root):**
    ```sql
    CREATE DATABASE nombre_de_tu_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
    ```
    * Reemplaza `nombre_de_tu_db` por el nombre deseado. Especificar el conjunto de caracteres (`CHARACTER SET`) y la intercalación (`COLLATE`) es una buena práctica.
* **DBeaver (conectado al servidor como root):**
    1.  En el **"Database Navigator"**, expande la conexión `MariaDB Local (Admin Root)`.
    2.  Haz clic derecho sobre el **nombre de la conexión** o sobre el nodo "Databases" o "Schemas".
    3.  En el menú contextual, selecciona **`Create -> Database`**.
    4.  En la ventana "Create Database":
        * En **"Database name"**, ingresa `nombre_de_tu_db`.
        * En la pestaña **"Properties"**, selecciona "Default Charset" (`utf8mb4`) y "Default Collation" (`utf8mb4_unicode_ci`).
    5.  Haz clic en **"OK"**.
    6.  Haz clic derecho en la conexión `MariaDB Local (Admin Root)` y selecciona **`Refresh`** para ver la nueva base de datos.

### Paso 6: Crear un usuario dedicado para tu base de datos

Crearás una nueva cuenta de usuario *dentro de MariaDB* con la que trabajarás normalmente.

* **Terminal (conectado al prompt de MariaDB como root):**
    ```sql
    CREATE USER 'nuevo_usuario'@'localhost' IDENTIFIED BY 'tu_contraseña_segura_unica';
    ```
    * Reemplaza `nuevo_usuario` y `tu_contraseña_segura_unica`. `'localhost'` restringe la conexión a la misma máquina (recomendado).
* **DBeaver (conectado al servidor como root):**
    1.  En el **"Database Navigator"**, expande la conexión `MariaDB Local (Admin Root)`.
    2.  Expande el nodo **"Security"**.
    3.  Haz clic derecho sobre el nodo **"Users"**.
    4.  Selecciona **`Create New User`**.
    5.  En la ventana "Create User":
        * **User Name:** Ingresa `nuevo_usuario`.
        * **Host:** Ingresa `localhost`.
        * **Password:** Ingresa **`tu_contraseña_segura_unica`**.
        * **Confirm password:** Vuelve a ingresarla.
    6.  Haz clic en **"OK"**.
    7.  Haz clic derecho en el nodo "Users" y selecciona **`Refresh`** para ver el nuevo usuario.

### Paso 7: Otorgar permisos al usuario dedicado en la base de datos

Asignarás los derechos de acceso al nuevo usuario, limitándolos a tu base de datos específica.

* **Terminal (conectado al prompt de MariaDB como root):**
    ```sql
    GRANT ALL PRIVILEGES ON nombre_de_tu_db.* TO 'nuevo_usuario'@'localhost';
    FLUSH PRIVILEGES; -- Buena práctica para asegurar que los cambios surtan efecto
    ```
    * `GRANT ALL PRIVILEGES ON nombre_de_tu_db.*` le da al `nuevo_usuario` control total (`ALL PRIVILEGES`) sobre todos los objetos (`.*`) dentro de la base de datos `nombre_de_tu_db`.
* **DBeaver (conectado al servidor como root):**
    1.  En el **"Database Navigator"**, expande la conexión `MariaDB Local (Admin Root)` -> **"Security" -> "Users"**.
    2.  Haz **doble clic** en `nuevo_usuario` para abrir su editor.
    3.  En el editor del usuario, ve a la pestaña **"Grants"**.
    4.  En la sección de permisos sobre objetos, haz clic en **"Add Grant"**.
    5.  En la ventana emergente, selecciona **"Database"** y busca/selecciona **`nombre_de_tu_db`**. Haz clic en **"OK"**.
    6.  Ahora, en la parte inferior del editor del usuario, busca la lista de permisos. Marca la casilla **"ALL PRIVILEGES"** (o selecciona los permisos específicos que necesites: SELECT, INSERT, UPDATE, etc.).
    7.  Busca y haz clic en el botón **"Save"** o **"Apply"** en la barra de herramientas del editor del usuario.
    8.  Si DBeaver pregunta si quieres ejecutar `FLUSH PRIVILEGES`, acepta.

### Paso 8: Conectarse a la base de datos usando el usuario dedicado (Conexión para trabajo diario)

Configurarás la conexión en DBeaver que usarás para tu trabajo habitual, conectando directamente a tu base de datos con el usuario de menor privilegio.

* **Terminal (usando el cliente de línea de comandos de MariaDB):**
    ```bash
    mariadb -u nuevo_usuario -p -D nombre_de_tu_db
    ```
    * Se te pedirá la **contraseña del `nuevo_usuario`**. `-D nombre_de_tu_db` te conecta directamente a esa base de datos al iniciar la sesión.
* **DBeaver (Interfaz Gráfica):**
    1.  Haz clic de nuevo en el icono **"New Database Connection"**.
    2.  Selecciona **"MariaDB"**. Haz clic en **"Next >"**.
    3.  En la pestaña **"Main"**:
        * **Server Host:** `localhost`.
        * **Port:** `3306`.
        * **Database:** **¡Aquí ingresa el nombre de tu base de datos!** `mi_proyecto_db`.
        * **User name:** Ingresa `nuevo_usuario`.
        * **Password:** Ingresa la **contraseña del `nuevo_usuario`**.
    4.  (Recomendado) En la pestaña **"General"**, cambia el **"Connection name"** a algo descriptivo, como `Mi Proyecto DB (usuario_proyecto)`.
    5.  Haz clic en **"Test Connection..."**. Debería conectar exitosamente a la base de datos específica.
    6.  Haz clic en **"Finish"**.

---

¡Felicidades! Ahora tienes configurado tu servidor MariaDB y una conexión segura en DBeaver usando un usuario dedicado para tu base de datos. Utiliza la última conexión configurada (`Mi Proyecto DB (usuario_proyecto)`) para tus tareas diarias.