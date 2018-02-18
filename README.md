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
