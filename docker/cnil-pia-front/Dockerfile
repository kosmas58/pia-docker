FROM nginx:1.19.5

ENV DEBIAN_FRONTEND noninteractive
ENV NODEJS_VERSION 14.15.1
ENV PIA_VERSION v2.4.0-beta04
ENV BRANCH pia-2.4-5-docker
ENV SUDO_FORCE_REMOVE yes

RUN apt-get update \
 && apt-get install --no-install-recommends -y \
    git \
    ca-certificates \
    wget \
    xz-utils \
    sudo \
    curl \
    gnupg \
    apt-transport-https \
 && apt-get clean

# Add Yarn repository
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
# Update
RUN apt-get update -y \
 && apt-get install yarn -y \
 && apt-get remove nodejs -y

RUN wget --no-verbose https://nodejs.org/dist/v${NODEJS_VERSION}/node-v${NODEJS_VERSION}-linux-x64.tar.xz -O /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz \
 && tar -xf /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz -C /opt \
 && ln -s /opt/node-v${NODEJS_VERSION}-linux-x64/bin/node /usr/bin/node \
 && ln -s /opt/node-v${NODEJS_VERSION}-linux-x64/bin/npm /usr/bin/npm \
 && rm /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz

RUN  /usr/bin/npm install npm@latest -g \
 && /usr/bin/npm install --unsafe-perm -g node-sass \
 && /usr/bin/npm install -g @angular/cli@9

COPY ./conf/cnil_pia.conf /etc/nginx/conf.d/default.conf

RUN mkdir -p /var/www && chown www-data. /var/www/

USER www-data

#RUN git clone --branch community https://github.com/kosmas58/pia.git --depth 1 /var/www/pia
RUN git clone --branch $BRANCH https://github.com/kosmas58/pia.git --depth 1 /var/www/pia

WORKDIR /var/www/pia

RUN yarn install \
 && cp src/environments/environment.prod.ts.example src/environments/environment.prod.ts \
 && sed -i -e "s/version: '2.0.0'/version: '$PIA_VERSION'/g" src/environments/environment.prod.ts \
 && /var/www/pia/node_modules/@angular/cli/bin/ng build --prod --build-optimizer

USER root
#Cleaning Packages
RUN apt-get remove git ca-certificates wget xz-utils sudo curl gnupg apt-transport-https  yarn -y \
 && apt-get autoremove -y \
 && rm -Rf /etc/apt/sources.list.d/yarn.list
