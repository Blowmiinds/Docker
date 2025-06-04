FROM php:8.1-apache

ARG release=3.2.1
ARG patch=1

# Instalar extensiones necesarias
COPY --from=mlocati/php-extension-installer:latest /usr/bin/install-php-extensions /usr/local/bin/

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        graphviz \
        mariadb-client \
        unzip \
    && install-php-extensions \
        apcu \
        gd \
        ldap \
        mcrypt \
        mysqli \
        soap \
        zip \
    && rm -rf /var/lib/apt/lists/*

# Cambiar Apache al puerto 8080
RUN sed -i 's/Listen 80/Listen 8080/' /etc/apache2/ports.conf \
    && sed -i 's/<VirtualHost \*:80>/<VirtualHost *:8080>/' /etc/apache2/sites-available/000-default.conf

EXPOSE 8080

# Descargar iTop
RUN curl -L -o /tmp/iTop.zip https://sourceforge.net/projects/itop/files/itop/3.2.1-1/iTop-3.2.1-1-16749.zip/download \
    && unzip /tmp/iTop.zip -d /tmp/ web/* \
    && mv -v /tmp/web/* /var/www/html/ \
    && mkdir -p /var/www/html/conf/production /var/www/html/data /var/www/html/log /var/www/html/env-production \
    && chown -R www-data:www-data /var/www/html \
    && rm -rf /tmp/iTop.zip /tmp/web

# Configurar PHP directamente
RUN echo "memory_limit = 256M" > /usr/local/etc/php/conf.d/itop.ini \
    && echo "zend.exception_ignore_args = 1" >> /usr/local/etc/php/conf.d/itop.ini \
    && echo "session.save_path = '/tmp'" >> /usr/local/etc/php/conf.d/itop.ini \
    && echo "session.cookie_httponly = 1" >> /usr/local/etc/php/conf.d/itop.ini \
    && echo "session.cookie_samesite = 'Lax'" >> /usr/local/etc/php/conf.d/itop.ini

# Variables de entorno esperadas por iTop
ENV DB_HOSTNAME=db \
    DB_ENV_MYSQL_DATABASE=itop \
    DB_ENV_MYSQL_USER=itopuser \
    DB_ENV_MYSQL_PASSWORD=secret \
    DB_PREFIX=""

# Configuración dinámica (recomendada hacerla vía instalación web)
# También podrías crear el config-itop.php dinámicamente aquí si lo necesitas

VOLUME ["/var/www/html/conf", "/var/www/html/data", "/var/www/html/log", "/var/www/html/env-production"]

CMD ["apache2-foreground"]
