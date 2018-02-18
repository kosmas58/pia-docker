Getting started
===============

Run the application in production mode
--------------------------------------

1. Fill the `production` section in the `database.yml`file.
2. Create the database: `RAILS_ENV=production bin/rake db:create`
3. Create the tables: `RAILS_ENV=production bin/rake db:migrate`
4. Run the server: `RAILS_ENV=production bin/rails s`

Run the full app through Docker-Compose for production
------------------------------------------------------

1. Fill the environment variables in `docker-compose.yml`
2. Run the containers `docker-compose up`
3. Access the website with `localhost:8080` or `yourdomain.net:8080`

Run the test
------------

`bin/rake`
