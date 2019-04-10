FROM nginx:1.15

ENV DEBIAN_FRONTEND noninteractive
ENV NODEJS_VERSION 10.15.3
ENV PIA_VERSION 2.0.0
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

RUN wget --no-verbose https://nodejs.org/dist/v${NODEJS_VERSION}/node-v${NODEJS_VERSION}-linux-x64.tar.xz -O /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz \
 && tar -xf /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz -C /opt \
 && ln -s /opt/node-v${NODEJS_VERSION}-linux-x64/bin/node /usr/bin/node \
 && ln -s /opt/node-v${NODEJS_VERSION}-linux-x64/bin/npm /usr/bin/npm \
 && rm /tmp/node-v${NODEJS_VERSION}-linux-x64.tar.xz

RUN  /usr/bin/npm install npm@latest -g \
 && /usr/bin/npm install --unsafe-perm -g node-sass \
 && /usr/bin/npm install -g @angular/cli

# Add Yarn repository
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
# Update
RUN apt-get update -y \
 && apt-get install yarn -y

COPY ./conf/cnil_pia.conf /etc/nginx/conf.d/default.conf

RUN mkdir -p /var/www && chown www-data. /var/www/

USER www-data

RUN git clone --branch $PIA_VERSION https://github.com/LINCnil/pia.git --depth 1 /var/www/pia

WORKDIR /var/www/pia

# Fix for release 1.6.0 : https://github.com/LINCnil/pia/issues/127#issuecomment-369620227
RUN yarn install \
 && cp src/environments/environment.prod.ts.example src/environments/environment.prod.ts \
 && sed -i -e "s/version: ''/version: '$PIA_VERSION'/g" src/environments/environment.prod.ts \
 && /var/www/pia/node_modules/@angular/cli/bin/ng build --prod --build-optimizer --sourcemaps

USER root
#Cleaning Packages
RUN apt-get remove git ca-certificates wget xz-utils sudo curl gnupg apt-transport-https  yarn -y \
 && apt-get autoremove -y \
 && rm -Rf /etc/apt/sources.list.d/yarn.list
