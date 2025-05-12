# Base Image
FROM php:8.3-fpm

# Install system dependencies
RUN apt-get update && apt-get install -y \
    nginx \
    git \
    unzip \
    curl \
    libzip-dev \
    libpng-dev \
    libjpeg-dev \
    libonig-dev \
    libxml2-dev \
    libcurl4-openssl-dev \
    libicu-dev \
    libmagickwand-dev \
    libpq-dev \
    libfreetype6-dev \
    nodejs \
    npm \
    python3 \
    ca-certificates \
    wget \
    gnupg \
    software-properties-common \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install -j$(nproc) \
    pdo \
    pdo_mysql \
    curl \
    json \
    zip \
    mbstring \
    intl \
    exif \
    gd \
    fileinfo \
    xml \
    bcmath \
    sodium

# Enable Imagick
RUN pecl install imagick && docker-php-ext-enable imagick

# Install Composer globally
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install Node.js (required for Sage)
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && apt-get install -y nodejs

# Install Playwright
RUN npm i -g playwright && playwright install

# Install Acorn CLI (for Sage theme)
RUN composer global require roots/acorn

# Add global Composer bin to PATH
ENV PATH="${PATH}:/root/.composer/vendor/bin"

# Create and set working directory
WORKDIR /var/www/html

# Install Bedrock
RUN composer create-project roots/bedrock .

# Install WordPress plugins via Composer (use wpackagist and ACF Pro with auth)
COPY composer.json composer.json
RUN composer install

# Permissions
RUN chown -R www-data:www-data /var/www/html

# Copy Nginx config (replace with your actual config files if needed)
COPY nginx.conf /etc/nginx/nginx.conf
COPY bedrock.conf /etc/nginx/conf.d/default.conf

# Expose ports
EXPOSE 80

# Start PHP and Nginx
CMD service nginx start && php-fpm



	