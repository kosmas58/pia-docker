FROM ruby:2.5.1-stretch
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update \
 && apt-get install --no-install-recommends -y \
    git \
    postgresql-client \
 && apt-get clean

RUN git clone --branch master https://github.com/LINCnil/pia-back.git --depth 1 /var/www/

COPY database.yml /var/www/config/database.yml
COPY application.yml /var/www/config/application.yml

WORKDIR /var/www/

RUN bundle install

COPY entrypoint /entrypoint
RUN chmod +x /entrypoint
ENTRYPOINT ["/entrypoint"]

ENV RAILS_ENV production

CMD ["bin/rails", "server"]
