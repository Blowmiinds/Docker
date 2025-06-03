FROM php:8.1-apache

# Instala dependencias necesarias
RUN apt-get update && apt-get install -y \
    unzip curl libpng-dev libjpeg-dev libfreetype6-dev libzip-dev libxml2-dev libldap2-dev pkg-config \
    && pecl install apcu && docker-php-ext-enable apcu \
    && docker-php-ext-install mysqli zip gd soap ldap \
    && mkdir -p /var/lib/php/sessions && chmod 733 /var/lib/php/sessions \
    && echo 'session.save_path="/var/lib/php/sessions"' > /usr/local/etc/php/conf.d/session.ini

# Configurar Apache para puerto 8080 y ServerName
RUN find /etc/apache2 -type f -exec sed -i 's/:80/:8080/g' {} + \
    && sed -i '/^Listen /c\Listen 8080' /etc/apache2/ports.conf \
    && echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Expone puerto 8080
EXPOSE 8080

# Descargar, descomprimir y copiar iTop
WORKDIR /var/www/html
RUN curl -L -o itop.zip https://sourceforge.net/projects/itop/files/itop/3.2.1-1/iTop-3.2.1-1-16749.zip/download \
    && unzip -q -o itop.zip -d itop \
    && ls -la itop/web \
    && cp -r itop/web/* . \
    && rm -rf itop.zip itop

# Crear carpetas necesarias y asignar permisos
RUN mkdir -p /var/www/html/conf /var/www/html/data /var/www/html/log /var/www/html/env-production \
    && chown -R www-data:www-data /var/www/html

# Iniciar Apache en primer plano
CMD ["apache2-foreground"]

