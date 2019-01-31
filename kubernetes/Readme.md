Kubernetes deployment

Knowledge and experience with kubernetes are required.
This will not covers how to get SSL certificates (Letsencrypt) neither than the deployment of an Ingress Controller (Haproxy/Traefik/Nginx).
First of all, you have to build your own docker images than push it to your own docker registry or use the docker hub.

Than you have to adapt the cnil-pia-deploy.yml to fit your docker registry url and your secrets parameters (POSTGRES_PASSWORD, SECRET_KEY_BASE...)
Deploy your pod with postgres, pia-front, pia-back on your kubernetes cluster on a production namespaces.
Depending of your kubernetes cluster you might want to change the location of where the postgres data will be stored (actually HostPath storage model)
kubectl create -f cnil-pia-deploy.yml -n production

Now you want to create the secret where your certificates will be stored in order to be used by the ingress controller.
kubectl create secret tls dpia.example.com --key ./dpia.example.com.key --cert ./dpia.example.com.fullchain -n production
kubectl create secret tls dpia-backend.example.com --key ./dpia-backend.example.com.key --cert ./dpia-backend.example.com.fullchain -n production

As the PIA is not using authentication or user backend it's good to add a bit of authentication using your ingress controller (BASIC-Auth).
In order to do that you have to create another secret with the user and the hash of the password using mkpasswd or makepasswd
kubectl create secret generic dpia-auth --from-file auth -n production

At least we use this file ```ingress-rules``` to declare our hosts with his url and SSL certificates using Basic-auth.
kubectl create -f ingress-rules.yml
This will create two ingress records, one with basic authentication for the application frontend and anther without authentication (because we can't make a setting on the front to connect to the backend using credentials yet)
