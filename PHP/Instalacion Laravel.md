
### 5. Instalar Composer (gestor de paquetes PHP)

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php --install-dir=/usr/local/bin --filename=composer
php -r "unlink('composer-setup.php');"
```

### 6. Instalar Laravel CLI globalmente

```bash
composer global require laravel/installer
```

### 7. Agregar Composer y Laravel al PATH

Edita tu `.bashrc` o `.zshrc`:

```bash
echo 'export PATH="$PATH:$HOME/.config/composer/vendor/bin"' >> ~/.bashrc
source ~/.bashrc
```

### 8. Crear un proyecto de Laravel

```bash
laravel new mi-proyecto
```
