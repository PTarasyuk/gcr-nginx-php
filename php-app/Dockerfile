FROM php:8-fpm-alpine

# Configure PHP for Cloud Run.
# Precompile PHP code with opcache.
RUN docker-php-ext-install -j "$(nproc)" opcache
RUN set -ex; \
    { \
        echo "; Cloud Run enforces memory & timeouts"; \
        echo "memory_limit = -1"; \
        echo "; Configure Opcache for Containers"; \
        echo "opcache.enable = 1"; \
        echo "opcache.validate_timestamps = 0"; \
        echo "opcache.memory_consumption = 192"; \
        echo "opcache.max_accelerated_files = 10000"; \
        echo "opcache.max_wasted_percentage = 10"; \
        echo "opcache.interned_strings_buffer = 16"; \
    } > "$PHP_INI_DIR/conf.d/cloud-run.ini"

COPY . /var/www/html/

# Use the default production configuration
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
