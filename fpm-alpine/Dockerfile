FROM nextcloud:fpm-alpine:latest

# Configure global container 
ARG TIMEZONE=${TIMEZONE:-Europe/Paris}
ENV TZ $TIMEZONE
EXPOSE 80

# Add nginx with config
RUN apk add nginx
COPY nextcloud.conf /etc/nginx/http.d/default.conf

CMD nginx && /entrypoint.sh php-fpm
