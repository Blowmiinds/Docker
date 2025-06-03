FROM php:8.1-apache

# Instala dependencias necesarias
RUN apt-get update && apt-get install -y \
    unzip curl libpng-dev libjpeg-dev libfreetype6-dev libzip-dev libxml2-dev libldap2-dev pkg-config \
    && pecl install apcu && docker-php-ext-enable apcu \
    && docker-php-ext-install mysqli zip gd soap ldap \
    && mkdir -p /var/lib/php/sessions && chmod 733 /var/lib/php/sessions \
    && echo 'session.save_path="/var/lib/php/sessions"' > /usr/local/etc/php/conf.d/session.ini

# Cambiar todos los puertos 80 a 8080 en configuración Apache
RUN find /etc/apache2 -type f -exec sed -i 's/:80/:8080/g' {} + \
    && sed -i '/^Listen /c\Listen 8080' /etc/apache2/ports.conf \
    && echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Expone puerto 8080
EXPOSE 8080

# Descargar y copiar iTop
WORKDIR /var/www/html
RUN curl -L -o itop.zip https://github.com/Combodo/iTop/releases/download/3.2.1-1/iTop-3.2.1-1-16749.zip \
    && unzip itop.zip -d itop \
    && cp -r itop/web/* . \
    && rm -rf itop.zip itop

# Otorgar permisos a www-data para configuración
RUN chown -R www-data:www-data /var/www/html/conf

# Ejecutar Apache en foreground
CMD ["apache2-foreground"]

