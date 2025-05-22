

#### **1. Instalar MariaDB (recomendado en Fedora):**

```bash
sudo dnf install mariadb-server
```

#### **2. Iniciar el servicio de MariaDB/MySQL:**

```bash
sudo systemctl start mariadb
```

Asegurarse de que MariaDB/MySQL se inicie automáticamente al arrancar:
```bash
sudo systemctl enable mariadb
```

#### **3. Configurar MariaDB/MySQL:**
Configurar la instalación inicial de MariaDB con:
```bash
sudo mysql_secure_installation
```
Pedirá contraseña de root. Seguir recomendaciones que dé.

---

### **Paso 2: Crear la base de datos y usuarios**

Ahora que tenemos MariaDB/MySQL instalado, vamos a crear la base de datos y un usuario para el proyecto.

#### **1. Iniciar sesión en MariaDB/MySQL:**
Como el usuario `root`:
```bash
sudo mysql -u root -p
```

Pedirá la contraseña que ya configuramos

#### **2. Crear la base de datos:**
Crear una base de datos llamada `guia_colombia`:
```sql
CREATE DATABASE guia_colombia;
```

#### **3. Crear un usuario y otorgar permisos:**
Por ejemplo, `usuario_guia`) con una contraseña y darle todos los permisos sobre la base de datos:
```sql
CREATE USER 'usuario_guia'@'localhost' IDENTIFIED BY 'tu_contraseña_segura';
GRANT ALL PRIVILEGES ON guia_colombia.* TO 'usuario_guia'@'localhost';
FLUSH PRIVILEGES;
```

#### **4. Usar la base de datos recién creada:**
```sql
USE guia_colombia;
```

---

### **Paso 3: Crear la tabla para las regiones**

Crear la tabla **regions** que almacenará la información de las diferentes regiones de Colombia.

#### **Esquema de la tabla `regions`:**
```sql
CREATE TABLE regions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    main_image VARCHAR(255) NOT NULL,
    gallery_image_1 VARCHAR(255) NOT NULL,
    gallery_image_2 VARCHAR(255) NOT NULL,
    gallery_image_3 VARCHAR(255) NOT NULL,
    map_image VARCHAR(255) NOT NULL
);
```

---

### **Paso 4: Alimentar la base de datos con los datos de las regiones**

Insertar los datos del arreglo que tienes en la tabla `regions`:

#### **Insertar datos de ejemplo:**
```sql
INSERT INTO regions (name, description, main_image, gallery_image_1, gallery_image_2, gallery_image_3, map_image) VALUES('Gran Caribe', 'Esta tierra de naturaleza exuberante, selvas y nieves perpetuas, dunas del desierto y mares de siete colores, es mucho más que arena dorada y arrecifes de coral. Es el hogar de culturas fascinantes, indígenas y raizales, de carnavales y música, de sitios arqueológicos sagrados, del imaginario garciamarquiano y
de la ciudad colonial más hermosa del mundo, Cartagena de Indias.', 'caribe.jpg', 'caribe-1.jpg', 'caribe-2.jpg', 'caribe-3.jpg', 'mapa_caribe.png'),('Pacífico', 'El secreto mejor guardado de Colombia, es un ecosistema megadiverso donde salvajes selvas colisionan con el océano, ballenas jorobadas hacen épicas travesías para dar a luz en sus aguas y tortugas marinas anidan en sus enormes playas vacías. De herencia afrodescendiente y ancestral, su oferta cultural es de talla mundial, gracias a Cali y su Salsa, su deliciosa gastronomía y sus festivales.', 'pacifica.jpg', 'pacifica-1.jpg', 'pacifica-2.jpg', 'pacifica-3.jpg', 'mapa_pacifica.png'),('Amazonía - Orinoquía', 'El corazón verde de Colombia es una inmensa región megadiversa de bosques milenarios, colosales cielos y atronadores raudales. El norte alberga un paisaje salvaje de llanuras vírgenes, hatos y esteros biodiversos, ricos en cultura de campo. Las inexploradas selvas del sur esconden un universo de maravillas naturales, petroglifos, saberes indígenas ancestrales y culturas vivas, que debemos seguir preservando.', 'amazo-orinoco.jpg', 'amazo-orinoco-1.jpg', 'amazo-orinoco-2.jpg', 'amazo-orinoco-3.jpg', 'mapa_amazo-orinoco.png'),('Macizo', 'Estos misteriosos páramos, montañas y volcanes son la cuna de la cultura andina colombiana y de los grandes ríos del país. La impronta de antiguas civilizaciones se siente fuertemente en sus parques arqueológicos y en sus  comunidades indígenas, que mantienen vivos sus saberes ancestrales. Un destino que invita a explorar la experiencia de lo eterno y que alberga la Ciudad Blanca de Popayán.', 'macizo.jpg', 'macizo-1.jpg', 'macizo-2.jpg', 'macizo-3.jpg', 'mapa_macizo.png'),('Andes Orientales', 'Andes históricos, donde empezó la colonización y la ruta libertadora. Tierra de cumbres, páramos y valles que alberga impresionantes Parques Naturales. Desde la capital Bogotá, con sus rascacielos, museos, gastronomía y su riquísima oferta cultural; la región se extiende hacia el norte transportando al visitante a maravillosos pueblos coloniales y a una época de leyendas, como la de El Dorado.', 'andina-oriental.jpg', 'andina-oriental-1.jpg', 'andina-oriental-2.jpg', 'andina-oriental-3.jpg', 'mapa_andina-oriental.png'),('Andes Occidentales', 'Región que transforma sociedades y mira al futuro desde la tradición. Escenario de majestuosas montañas con aroma a café, pueblos extraordinariamente coloridos, campesinos conversadores, aves exóticas y valles colmados de flores. Es la tierra del Paisaje Cultural Cafetero y de la moderna Medellín: ciudad de Botero, la cultura silletera, la gastronomía y la moda colombiana.', 'andina-occidental.jpg', 'andina-occidental-1.jpg', 'andina-occidental-2.jpg', 'andina-occidental-3.jpg', 'andina-occidental.png');
```

Se llenará la tabla `regions` con los datos de las regiones.

---

### **Paso 5: Verificar la base de datos desde PHPStorm**

**PHPStorm** puede conectarse a bases de datos si tienes configurada la funcionalidad del **Database Tool Window**. Si no se tiene, se puede gestionar la base de datos desde la terminal usando **MySQL Workbench** o cualquier otro cliente de bases de datos que prefieras.

Desde PHPStorm:

1. Ir a **View > Tool Windows > Database**.
2. Haz clic en el botón **+** para añadir una nueva conexión.
3. Seleccionar **MySQL** o **MariaDB** como el tipo de base de datos.
4. Introducir los detalles de conexión:
   - **Host**: localhost
   - **Database**: guia_colombia
   - **User**: usuario_guia
   - **Password**: (la contraseña que creamos)
   
5. Conectarse y revisar si todo está configurado correctamente.

---
### Crear tablas de `users` y `comments` para el sitio.

```SQL
-- Crear la tabla de usuarios
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL UNIQUE
);

-- Crear la tabla de comentarios
CREATE TABLE comments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    region VARCHAR(255) NOT NULL,
    comment TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```
