FROM php:7.2-fpm

# Set working directory
WORKDIR /var/www

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    mariadb-client \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    libzip-dev \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl \
    && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-install zip

RUN apt-get install -y cron && \
	apt-get install -y supervisor

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl
RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
RUN docker-php-ext-install gd

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy existing application directory contents
COPY . /var/www

RUN (crontab -l ; echo "* * * * * /usr/bin/php /var/www/artisan schedule:run >>/dev/null 2>&1") | crontab

# Copy existing application directory permissions
COPY --chown=www:www . /var/www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
#CMD ["php-fpm"]
CMD  service supervisor start; php-fpm