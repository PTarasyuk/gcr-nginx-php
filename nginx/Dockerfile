FROM nginx:1-alpine

ARG FASTCGI_PASS=127.0.0.1:9000
ENV FASTCGI_PASS=${FASTCGI_PASS}

COPY nginx.conf /etc/nginx/conf.d/nginx.conf

COPY index.php /var/www/html/

RUN envsubst '${FASTCGI_PASS}' < /etc/nginx/conf.d/nginx.conf > /etc/nginx/conf.d/nginx.temp \
    && mv /etc/nginx/conf.d/nginx.temp /etc/nginx/conf.d/nginx.conf
