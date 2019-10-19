Getting started
=
Here is **Docker-Compose** configuration for production purpose. Everything is automated from creating containers to setting up the database. A simple `docker-compose up` does everything and gives a running website : front-end, back-end and database.
However, you still have to add the back-end URL on the front-end interface as we don't see a way to automate it.
We include the Dockerfiles so you can build the images yourself. The `docker-compose.yml` is here for demonstration purpose. You may want to use Docker with Kubernetes for example for production.

Prerequisites
-
1. Get and install **Docker** https://www.docker.com/get-docker on your machine
2. Download` pia-docker.zip` and unzip it.

Run the full app through Docker-Compose for production
-

1. Fill the environment variables in `docker-compose.yml`
2. Open a shell and switch to the `pia-docker` directory
3. Run the containers by typing `docker-compose up` into the shell
4. Access the website with `localhost:8080` or `yourdomain.net:8080`

Addtional information
-
The installation under Windows 10 is described in the Wiki:
https://github.com/kosmas58/pia-docker/wiki/Installation-under-Windows-10

Connect the frontend (or client app) to the backend
-

- Press "Start" in the PIA frontend or client
- Then in frontend or client set this URL to enable the server mode.
- Fill the field in "Tools" > "Settings"

![PIA Settings](docker/pia-settings.png)

SSL Setup
-

To setup PIA with SSL/TLS encryption (i.e. access it with https:// later), you first need to obtain a valid certificate and key from your certificate authority of choice. As an example, we will assume a LetsEncrypt certificate obtained by certbot is available at the host machine (the machine docker runs on).

1. Mount the directory containing the certificate and private key in cnil-pia-back **and** cnil-pia-front

For the letsencrypt example this should be a modification in the docker-compose file like:

```
  volumes:
  - "/etc/letsencrypt:/etc/letsencrypt"
```

(Yes, the whole /etc/letsencrypt directory is necessary, as certbot links the most recent certificate using soft links. The real certificate and private key reside under `archive` with changing filenames.)

2. Change the target port of cnil-pia-front to 443 in the docker-compose.yml

In the backend, we need to force ssl for ruby-on-rails (i.e. the puma application server).

3. Uncomment `config.force_ssl = true` in the `environment/production.rb` config file.
4. Change the command executed by docker to start the container pointing to SSL certificate and key

You could do this by adding a simple sed command and by chaning the CMD line in the Dockerfile of the backend:
```
...
# Force SSL in production
RUN sed -i 's/# config.force_ssl = true/config.force_ssl = true/' config/environments/production.rb

COPY entrypoint /entrypoint
RUN chmod +x /entrypoint
ENTRYPOINT ["/entrypoint"]

CMD ["bundle", "exec", "puma", "-b", "ssl://0.0.0.0:3000?key=/etc/letsencrypt/live/www.example.com/privkey.pem&cert=/etc/letsencrypt/live/www.example.com/fullchain.pem"]
```

5. Modify the nginx configuration of cnil-pia-front to listen to ssl

Instead of listening to port 80, the standard SSL port 443 has to be setup. You can change this in `docker/cnil-pia-front/conf/cnil_pia.conf`:

```
server {
    listen 443 ssl;
    server_name _;
    root /var/ww/pia/dist/;

    ssl_certificate /etc/letsencrypt/live/www.example.com/fullchian.pem;
    ssl_certificate_key /etc/letsencrypt/live/www.example.com/privkey.pem;

    location / {
    }
}
```

This should do the trick, use `docker-compose up -d` as usual to start HTTPS-enabled pia!
