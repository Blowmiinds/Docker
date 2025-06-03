FROM php:8.1-apache

# Instala dependencias necesarias

RUN apt-get update && apt-get install -y \
    unzip curl libpng-dev libjpeg-dev libfreetype6-dev libzip-dev libxml2-dev libldap2-dev pkg-config \
    && pecl install apcu && docker-php-ext-enable apcu \
    && docker-php-ext-install mysqli zip gd soap ldap \
    && mkdir -p /var/lib/php/sessions && chmod 733 /var/lib/php/sessions && \
    echo 'session.save_path="/var/lib/php/sessions"' > /usr/local/etc/php/conf.d/session.ini



# Descarga iTop
WORKDIR /var/www/html
RUN curl -L -o itop.zip https://github.com/Combodo/iTop/releases/download/3.2.1-1/iTop-3.2.1-1-16749.zip && \
    ls -lh itop.zip && file itop.zip && \
    unzip itop.zip -d itop && \
    cp -r itop/web/* . && \
    rm -rf itop.zip itop


# Otorga permisos
RUN chown -R www-data:www-data /var/www/html/conf

# Expone puerto
CMD ["apache2-foreground"]

# CONFIGURAR APACHE PARA ESCUCHAR EN 8080
#RUN sed -i 's/80/8080/g' /etc/apache2/ports.conf /etc/apache2/sites-available/000-default.conf
