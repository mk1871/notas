Para configurar la última versión de PHP y Laravel en Fedora 42, sigue estos pasos:

### 1. Actualizar el sistema (ya realizado)

```bash
sudo dnf update
```

### 2. Agregar repositorios de PHP (Remi)

Fedora puede no tener la última versión de PHP en sus repos oficiales. Usa el repositorio Remi:

```bash
sudo dnf install https://rpms.remirepo.net/fedora/remi-release-42.rpm
dnf config-manager setopt remi.enabled=1 # Algunas dependencias comunes necesitamos habilitarlas
sudo dnf module reset php
sudo dnf module enable php:remi-8.4  # Reemplaza "8.4" con la última versión estable
```

### 3. Instalar PHP y extensiones requeridas

```bash
sudo dnf install php php-cli php-fpm php-common php-ctype php-mbstring php-xml php-mysqlnd php-openssl php-pdo php-tokenizer php-opcache php-curl php-zip php-json php-bcmath
```


### 4. Verificar la versión de PHP

```bash
php -v
```

