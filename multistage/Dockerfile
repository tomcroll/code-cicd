# Build stage: Install Composer dependencies
FROM php:8.3-cli AS dammy

# Install necessary packages
RUN apt-get update && apt-get install -y git unzip

# Set working directory
WORKDIR /app

# Copy composer.json and composer.lock if it exists
COPY ../app/composer.json ./

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install dependencies with no interaction
RUN composer install --no-interaction --no-dev --prefer-dist --optimize-autoloader --no-suggest

# Production stage: Only copy what is needed for running the application
FROM php:8.3-apache

# Set working directory
WORKDIR /var/www/html

# Copy the installed dependencies from the builder stage
COPY --from=dammy /app/vendor /var/www/html/vendor

# Copy the rest of the application files
COPY . .

# Install necessary PHP extensions
RUN docker-php-ext-install mysqli pdo pdo_mysql

# Set the correct permissions
RUN chown -R www-data:www-data /var/www/html

# Expose port 80
EXPOSE 80

# Start Apache in the foreground
CMD ["apache2-foreground"]