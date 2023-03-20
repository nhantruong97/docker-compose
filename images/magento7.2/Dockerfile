FROM public.ecr.aws/h9g4e3y5/magento-base-dev:03

ENV COMPOSER_VERSION 2.3.10

RUN groupadd -g 1001 app; useradd -m -s /bin/bash -u 1001 -g 1001 app
RUN usermod -aG www-data app; usermod -aG php app

RUN apt install ca-certificates && apt-get update -y
RUN apt-get install --no-install-recommends --no-install-suggests -q -y  \
    libnss3-tools \
    curl \
    vim \
    openssl \
    cron \
    default-mysql-client \
    git \
    && rm -rf /etc/nginx/conf.d/default.conf \
    # Install Composer
    && curl -o /tmp/composer-setup.php https://getcomposer.org/installer \
    && curl -o /tmp/composer-setup.sig https://composer.github.io/installer.sig \
    && php -r "if (hash('SHA384', file_get_contents('/tmp/composer-setup.php')) !== trim(file_get_contents('/tmp/composer-setup.sig'))) { unlink('/tmp/composer-setup.php'); echo 'Invalid installer' . PHP_EOL; exit(1); }" \
    && php /tmp/composer-setup.php --no-ansi --install-dir=/usr/local/bin --filename=composer --version=${COMPOSER_VERSION} \
    && rm -rf /tmp/composer-setup.php

RUN mkdir /etc/nginx/certs/ \
    && echo -e "\n\n\n\n\n\n\n" | openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/certs/nginx.key -out /etc/nginx/certs/nginx.crt

ARG TARGETARCH

# OPTIONAL: for local ssl
RUN cd /usr/local/bin/ \
  && curl -L https://github.com/FiloSottile/mkcert/releases/download/v1.4.3/mkcert-v1.4.3-linux-$TARGETARCH -o mkcert \
  && chmod +x mkcert

###blackfire
RUN wget -q -O - https://packages.blackfire.io/gpg.key |  dd of=/usr/share/keyrings/blackfire-archive-keyring.asc \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/blackfire-archive-keyring.asc] http://packages.blackfire.io/debian any main" |  tee /etc/apt/sources.list.d/blackfire.list \
  &&  apt update -y \
  &&  apt install blackfire -y



RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && architecture=$(uname -m) \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/$architecture/$version \
    && mkdir -p /tmp/blackfire \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp/blackfire \
    && mv /tmp/blackfire/blackfire-*.so $(php -r "echo ini_get ('extension_dir');")/blackfire.so \
    && rm -rf /tmp/blackfire /tmp/blackfire-probe.tar.gz

RUN mkdir -p /etc/nginx/html /var/www/html \
  && chown -R app:app /etc/nginx /etc/nginx/certs /var/www /var/cache/nginx /var/run

# Nginx main config copy
COPY config/nginx/nginx.conf /etc/nginx/nginx.conf
COPY config/nginx/default.conf /etc/nginx/conf.d/default.conf
COPY config/nginx/nginx.conf.sample /etc/nginx/
COPY config/entrypoint72.sh /etc/entrypoint72.sh



## add nginx permissions
RUN chown -R app:app /var/cache/nginx && \
    chown -R app:app /var/log/nginx && \
    chown -R app:app /etc/nginx/conf.d

RUN touch /var/run/nginx.pid && \
        chown -R app:app /var/run/nginx.pid
RUN touch /var/run/php-fpm.pid && \
        chown -R app:app /var/run/php-fpm.pid /var/run/php

#php copy configuration
COPY config/php/php-fpm.conf /etc/php/7.2/fpm/
COPY config/php/www.conf /etc/php/7.2/fpm/pool.d/
COPY config/php/php.ini /etc/php/7.2/fpm/

#blackfire if any
COPY config/blackfire/blackfire.ini /etc/php/7.2/fpm/conf.d/blackfire.ini

WORKDIR /var/www/html

RUN chmod +x /etc/entrypoint72.sh

ENTRYPOINT ["/bin/sh", "-c", "/etc/entrypoint72.sh"]

USER app:app

