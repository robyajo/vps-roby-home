FROM php:8.2-fpm

# -- ARGs for dynamic user creation from docker-compose.yml
ARG user=www
ARG uid=1000

# -- Set working directory
WORKDIR /var/www

# -- Install system dependencies
RUN apt-get update -y && apt-get install -y \
  libicu-dev \
  libmariadb-dev \
  unzip zip \
  zlib1g-dev \
  libpng-dev \
  libjpeg-dev \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  git \
  curl

# -- Install Composer (copy from official Composer image)
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# -- Install PHP extensions
RUN docker-php-ext-install gettext intl pdo_mysql

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
  && docker-php-ext-install -j$(nproc) gd

# -- Add user and group based on ARG
RUN groupadd -g ${uid} ${user} && \
  useradd -u ${uid} -ms /bin/bash -g ${user} ${user} && \
  usermod -aG www-data ${user} && \
  mkdir -p /home/${user}/.composer && \
  chown -R ${user}:${user} /home/${user}

# -- Copy source code with correct permissions
COPY --chown=${user}:${user} . /var/www

# -- Switch to non-root user
USER ${user}

# -- Expose port and start php-fpm
EXPOSE 9000
CMD ["php-fpm"]
