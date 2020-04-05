FROM ruby:2.7

ENV DEBIAN_FRONTEND noninteractive
ENV RAILS_ENV production
ENV RAILS_SERVE_STATIC_FILES=true
ENV BRANCH pia-2.3

RUN apt-get update \
 && apt-get install --no-install-recommends -y \
    git \
    postgresql-client \
 && apt-get clean

RUN git clone --branch $BRANCH https://github.com/kosmas58/pia-back.git --depth 1 /var/www/

COPY database.yml /var/www/config/database.yml
COPY application.yml /var/www/config/application.yml

WORKDIR /var/www/

RUN gem install bundler
RUN bundle install

COPY entrypoint /entrypoint
RUN chmod +x /entrypoint
ENTRYPOINT ["/entrypoint"]

CMD ["bin/rails", "server"]
