FROM ruby:2.5-stretch
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update \
 && apt-get install --no-install-recommends -y \
    git \
    postgresql-client \
 && apt-get clean

# We should checkout a tag once there is a new release after 1.1
# RUN git clone https://github.com/LINCnil/pia-back.git /var/www/
RUN git clone -b master https://github.com/LNICnil/pia-back.git --depth 1 /var/www/

COPY database.yml /var/www/config/database.yml
COPY application.yml /var/www/config/application.yml

WORKDIR /var/www/

RUN bundle install

COPY entrypoint /entrypoint
RUN chmod +x /entrypoint
ENTRYPOINT ["/entrypoint"]

ENV RAILS_ENV production

CMD ["bin/rails", "server"]
