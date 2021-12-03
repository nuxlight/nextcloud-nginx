FROM nextcloud:22.2.3-fpm

# Installe l'UTF8
ENV LANG=fr_FR.UTF-8
RUN apt-get update && \
    export DEBIAN_FRONTEND=noninteractive; \
    apt-get -y install vim rsyslog && \
    echo "LANG=\"$LANG\"\nLANGUAGE=\"$LANG\"\nLC_NUMERIC=\"$LANG\"\nLC_TIME=\"$LANG\"\nLC_MONETARY=\"$LANG\"\nLC_PAPER=\"$LANG\"\nLC_IDENTIFICATION=\"$LANG\"\nLC_NAME=\"$LANG\"\nLC_ADDRESS=\"$LANG\"\nLC_TELEPHONE=\"$LANG\"\nLC_MEASUREMENT=\"$LANG\"" >>/etc/default/locale;

# Installe logrotate: nécessite un CMD service cron start
RUN apt-get update && \
    apt-get -y install logrotate cron && \
    echo '#!/bin/sh\nexit 0' >/usr/sbin/policy-rc.d;

# Mise à l'heure
#   TIMEZONE=<fuseau>
ARG TIMEZONE=${TIMEZONE:-Europe/Paris}
ENV TZ $TIMEZONE
RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && \
    apt-get -y install tzdata && \
    echo "$TIMEZONE" >/etc/timezone; \
    ln -sf /usr/share/zoneinfo/$TIMEZONE /etc/localtime; \
    dpkg-reconfigure -f noninteractive tzdata;

RUN apt-get update && \
    apt-get -y install nginx redis

RUN apt-get update && \
    apt-get -y install sudo telnet htop

ARG DOMAIN=localhost
RUN mkdir -p /etc/nginx/ssl && \
    openssl req -x509 -nodes -newkey rsa:2048 -days 1 -keyout /etc/nginx/ssl/privkey.pem -out /etc/nginx/ssl/fullchain.pem -subj "/CN=$DOMAIN"

ARG CRON=/var/spool/cron/crontabs/www-data
RUN echo "*/5 * * * * PHP_MEMORY_LIMIT=512M /usr/local/bin/php -f /var/www/html/cron.php >>/var/log/nextcloud_cron.log" >$CRON; \
    chmod 600 $CRON; \
    chown www-data:crontab $CRON; \
    sed -i 's/^memory_limit=.*$/memory_limit=512m/' /usr/local/etc/php/conf.d/nextcloud.ini;

RUN echo "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" | base64 -d | xz -dc >/etc/nginx/sites-enabled/nextcloud.conf;

ENTRYPOINT 

CMD chown -R www-data: /var/www/html; \
    chown :www-data /var/log; \
    chmod g+w /var/log; \
    service rsyslog start && \
    service nginx start && \
    service redis-server start && \
    service cron start && \
    sudo -u www-data PHP_MEMORY_LIMIT=512M php occ db:add-missing-columns; \
    sudo -u www-data PHP_MEMORY_LIMIT=512M php occ db:add-missing-indices; \
    /entrypoint.sh php-fpm