FROM node:14 as builder

USER root

ARG PIA_VERSION=2.2.0
ARG NODE_SASS_VERSION=4.14.1
ARG ANGULAR_CLI_VERSION=latest

WORKDIR /build

RUN npm install -g @angular/cli@$ANGULAR_CLI_VERSION

RUN wget https://github.com/LINCnil/pia/archive/refs/tags/$PIA_VERSION.zip

RUN unzip $PIA_VERSION.zip \
 && rm $PIA_VERSION.zip \
 && cd pia-$PIA_VERSION \
 && cp src/environments/environment.prod.ts.example src/environments/environment.prod.ts \
 # Fix for release 1.6.0 : https://github.com/LINCnil/pia/issues/127#issuecomment-369620227
 && sed -i -e "s/version: '2.0.0'/version: '$PIA_VERSION'/g" src/environments/environment.prod.ts \
 # Note, it should be npm ci but the upstream do not necessarily sync the package-lock
 && npm install \
 && npm install node-sass@$NODE_SASS_VERSION \
 && ng build --prod --build-optimizer \
 && mv /build/pia-$PIA_VERSION /build/pia


FROM nginx:1.16

RUN mkdir -p /var/www && chown www-data. /var/www/

COPY ./conf/cnil_pia.conf /etc/nginx/conf.d/default.conf

COPY --from=builder /build/pia/dist/pia /var/www/