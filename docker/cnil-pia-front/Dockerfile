FROM nginx:1.13
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update \
 && apt-get install --no-install-recommends -y \
    git \
    ca-certificates \
    wget \
    xz-utils \
    sudo \
 && apt-get clean

ENV NODEJS_VERSION 8.9.4
RUN wget --no-verbose https://nodejs.org/dist/v${NODEJS_VERSION}/node-v${NODEJS_VERSION}-linux-x64.tar.xz -O /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz \
 && tar -xf /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz -C /opt \
 && ln -s /opt/node-v${NODEJS_VERSION}-linux-x64/bin/node /usr/bin/node \
 && ln -s /opt/node-v${NODEJS_VERSION}-linux-x64/bin/npm /usr/bin/npm \
 && rm /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz

COPY ./conf/cnil_pia.conf /etc/nginx/conf.d/default.conf

RUN mkdir -p /var/www && chown www-data. /var/www/

USER www-data

# We should checkout a tag once there is a new release after 1.5.0
#RUN git clone https://github.com/LINCnil/pia.git /var/www/pia
RUN git clone -b master https://github.com/LINCnil/pia.git /var/www/pia

WORKDIR /var/www/pia

RUN npm install \
  && npm run build -prod

USER root
