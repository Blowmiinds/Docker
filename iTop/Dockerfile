FROM php:8.1-apache

# Instala dependencias necesarias
RUN apt-get update && apt-get install -y \
    unzip curl libpng-dev libjpeg-dev libfreetype6-dev libzip-dev libxml2-dev libldap2-dev pkg-config \
    && pecl install apcu && docker-php-ext-enable apcu \
    && docker-php-ext-install mysqli zip gd soap ldap \
    && mkdir -p /var/lib/php/sessions && chmod 733 /var/lib/php/sessions \
    && echo 'session.save_path="/var/lib/php/sessions"' > /usr/local/etc/php/conf.d/session.ini

# Cambiar puertos Apache a 8080 y configurar ServerName
RUN find /etc/apache2 -type f -exec sed -i 's/:80/:8080/g' {} + \
    && sed -i '/^Listen /c\Listen 8080' /etc/apache2/ports.conf \
    && echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Expone puerto 8080
EXPOSE 8080

# Descargar, descomprimir y copiar iTop
WORKDIR /var/www/html
RUN curl -L -o itop.zip https://sourceforge.net/projects/itop/files/itop/3.2.1-1/iTop-3.2.1-1-16749.zip/download \
    && unzip -q -o itop.zip -d itop \
    && cp -r itop/web/* . \
    && rm -rf itop.zip itop

# Crear directorios para caché, logs y otros archivos fuera de /var/www/html
RUN mkdir -p /tmp/itop-cache /tmp/itop-log /tmp/itop-data /var/www/html/conf /var/www/html/env-production \
    && chown -R www-data:www-data /tmp/itop-cache /tmp/itop-log /tmp/itop-data /var/www/html

# Modificar la configuración de iTop para usar directorios en /tmp
RUN echo "\$MySettings['cache_dir'] = '/tmp/itop-cache';" >> /var/www/html/conf/production/config-itop.php \
    && echo "\$MySettings['log_dir'] = '/tmp/itop-log';" >> /var/www/html/conf/production/config-itop.php \
    && echo "\$MySettings['data_dir'] = '/tmp/itop-data';" >> /var/www/html/conf/production/config-itop.php

# Comando para iniciar Apache en primer plano
CMD ["apache2-foreground"]
