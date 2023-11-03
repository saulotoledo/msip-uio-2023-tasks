# Introduction to Microservices - Tasks

## Docker

### Before starting

If you do not have Docker installed yet, follow the instructions at https://docs.docker.com/get-docker/ to install it.

### Task D1: Docker Hello world!

The following command will download the hello-world:latest image and run it:

```sh
docker run hello-world
```

Now, check the status of the container:

```sh
docker container ls -a

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
763d61bd1f66  hello-world  "/hello"   15 seconds ago  Exited (0) 14 seconds ago       angry_mestorf
```

Note that the status "Exited": the container is not running anymore.

### Task D2: Running Ubuntu

The following command will download the ubuntu:latest image and run it:

```sh
docker run ubuntu
```

> **Question:** Why is there no output as before?


Now, check the status of the container:

```sh
docker container ls -a

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
252e90596cd3  ubuntu    "/bin/bash"  10 seconds ago  Exited (0) 11 seconds ago       confident_hopper
```

Note that the status "Exited": the container is not running anymore.

Try the `ls` without `-a`:

```sh
docker container ls

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
```

> **Question:** Why is the output empty?

> **Hint:** `docker ps` and `docker ps -a` were the old way of checking the containers.
> They still work and can be found in many tutorials everywhere.

### Task D3: Running an interactive shell on Ubuntu

```sh
docker run -it ubuntu bash

root@1b308e8aa1a3:/# 
```

If you executed the previous task, the `ubuntu:latest` image is already on your computer and will not be downloaded again. You can interact with the shell.

Keep this shell running and open a new terminal window. Then type the following command:

```sh
docker container ls -a

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
1b308e8aa1a3  ubuntu    "bash"    3 minutes ago  Up 3 minutes             clever_bartik
252e90596cd3  ubuntu    "/bin/bash"  13 minutes ago  Exited (0) 13 minutes ago       confident_hopper
```

Compare the two Ubuntu containers: one is still running.

Get back to the previous terminal and type `exit`:

```sh
root@1b308e8aa1a3:/# exit
```

Then, recheck the containers:

```sh
docker container ls -a   

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
1b308e8aa1a3  ubuntu    "bash"    6 minutes ago  Exited (0) 11 seconds ago       clever_bartik
252e90596cd3  ubuntu    "/bin/bash"  17 minutes ago  Exited (0) 17 minutes ago       confident_hopper
```

They both exited.


### Task D4: Where are the images?

Try the command below:

```sh
docker image ls

REPOSITORY  TAG    IMAGE ID    CREATED    SIZE
ubuntu    latest  e4c58958181a  3 weeks ago  77.8MB
hello-world  latest  9c7a54a9a43c  6 months ago  13.3kB
```

### Task D5: Our first Dockerfile

Create a folder named `msip-docker` on your computer.
In this folder, create a file named `Dockerfile` (the first letter must be a capital `D`) with the following content:

```
FROM ubuntu:20.04

CMD ["echo", "Hello", "World!"]
```

**Did you note that we used a specific version this time instead of `latest`?**

Open a terminal in this folder and build the image:

```sh
docker build . -t msip-hello
docker build . -t msip-hello:1.0
```

Next, check the images:

```sh
docker image ls

REPOSITORY  TAG    IMAGE ID    CREATED    SIZE
...
msip-hello  1.0    670fcf3ec158  4 weeks ago  72.8MB
msip-hello  latest  670fcf3ec158  4 weeks ago  72.8MB
```

We used a `tag` to specify the version. If a tag is not informed, `latest` is assumed.

Finally, run a container based on your new image:

```sh
docker run msip-hello
```


### Task D6: Containerizing an application

Clone the Docker "getting started" app and go to the `app` folder:

```sh
git clone https://github.com/docker/getting-started.git
cd getting-started/app
```

Create a `Dockerfile` in this folder with the following content:

```sh
FROM node:16-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
```

Build and run the app:

```sh
docker build -t node-app .
docker run -p 3000:3000 node-app
```

Open your browser at [http://localhost:3000/](http://localhost:3000/).

> **Question:** Did you notice the application keeps the container running?

Stop the application with `Ctrl + C`. Then try the following command:

```sh
docker run -p 4000:3000 node-app
```

Open your browser at [http://localhost:4000/](http://localhost:4000/).

> **Question:** What happened here?

One more time, stop the application with `Ctrl + C` and try the following command:

```sh
docker run -d -p 4000:3000 node-app
```

Open your browser at [http://localhost:4000/](http://localhost:4000/). The container is now running as a background process. You can see, stop, and remove it with the following commands:

```sh
docker container ls
docker stop <container-id>
docker rm <container-id>
```

### Task D7: Creating a PostgreSQL container for development

Let's do this one together. Follows the command we are going to run:

```sh
docker pull postgres
docker pull dpage/pgadmin4
docker image ls
docker network create msip-network
docker network ls
docker run --name msip-postgres --network=msip-network -e "POSTGRES_PASSWORD=postgres" -d postgres:latest
docker run --name msip-pgadmin --network=msip-network -p 8100:80 -e "PGADMIN_DEFAULT_EMAIL=admin@admin.com" -e "PGADMIN_DEFAULT_PASSWORD=postgres" -d dpage/pgadmin4:latest
docker container ls
```

> **Hint:** Note that the new containers do not have random names anymore.

Open [http://localhost:8100/](http://localhost:8100/) on your preferred browser.


### Task D8: Docker compose

Let's do some clean up:

```sh
docker stop msip-pgadmin msip-postgres
docker rm msip-pgadmin msip-postgres
docker network rm msip-network
```

In your `msip-docker` folder, create a file named `docker-compose.yml` (no uppercase `D` this time) with the following content:

```version: '3'
services:
  msip-postgres:
    image: postgres:latest
    container_name: msip-postgres
    environment:
      POSTGRES_PASSWORD: postgres
    networks:
      - msip-network
  msip-pgadmin:
    image: dpage/pgadmin4:latest
    container_name: msip-pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: postgres
    ports:
      - 8100:80
    networks:
      - msip-network
networks:
  msip-network:
    driver: bridge
```

Run the following command:

```sh
docker-compose up
```

The containers will start as described. It's more straightforward than running Docker commands manually, isn't it?

Stop the process with `Ctrl + C` and run it in the "detach" mode:

```sh
docker-compose up -d
```

You can stop all those containers with:

```sh
docker-compose down
```

## Kubernetes

### Before starting

We need the following software installed:
Kind: [https://kind.sigs.k8s.io/docs/user/quick-start/#installation](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
Kubectl (CLI for the cluster): [https://kubernetes.io/docs/tasks/tools/](https://kubernetes.io/docs/tasks/tools/)

### Task K1: Playing with a local Kubernetes cluster using Kind:

Let's do it together:

```sh
kind create cluster --name tempcluster
kind get clusters
kubectl cluster-info
kind delete cluster -n tempcluster
kind create cluster --name ourcatalog
kubectl get nodes
kubectl cluster-info
docker container ls
```

### Task K2: Our first pod

Create a folder named `k8s-tutorial` and change to it:

```sh
mkdir k8s-tutorial
cd k8s-tutorial
```

Create a file named `pod.yml` with the following content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: msip-products
  labels:
    type: products
spec:
   containers:
     - name: msip-products-container
       image: saulotoledo/ourcatalog-products:v1
       env:
         - name: PORT
           value: "3000"
         - name: HOST
           value: "0.0.0.0"
         - name: NODE_ENV
           value: "production"
         - name: APP_KEY
           value: "adonisjs-appkey-change-me"
         - name: PAGINATION_LIMIT
           value: "10"
         - name: DB_CONNECTION
           value: "sqlite3"
```

Apply the manifest and list your pods:

```sh
kubectl apply -f pod.yml
kubectl get pods
kubectl get po
```

You need to expose the Pod to access it locally using the `port-forward` command:

```sh
kubectl port-forward pod/msip-products 8080:3000
```

Finnaly, open [http://localhost:8080/api/products](http://localhost:8080/api/products) in your browser.


### Task K3: Our first ReplicaSet

Stop the `port-forwarding` from the previous step with `Ctrl + C` and remove the pod we created before:

```sh
kubectl delete pod msip-products
```

Create a file named `replicaset.yml` with the following content:

```yamlapiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: msip-products
spec:
  replicas: 3
  selector:
    matchLabels:
      app: products
  template:
    metadata:
      labels:
        app: products
    spec:
      containers:
        - name: msip-products-container
          image: saulotoledo/ourcatalog-products:v1
          env:
            - name: PORT
              value: "3000"
            - name: HOST
              value: "0.0.0.0"
            - name: NODE_ENV
              value: "production"
            - name: APP_KEY
              value: "adonisjs-appkey-change-me"
            - name: PAGINATION_LIMIT
              value: "10"
            - name: DB_CONNECTION
              value: "sqlite3"
```

Apply the manifest and check ReplicaSets and Pods:

```sh
kubectl apply -f replicaset.yml
kubectl get replicasets
kubectl get po
```

Remove one Pod created by the ReplicaSet, then recheck the Pods:

```sh
kubectl delete po (PodName)
kubectl get po
```

Note that a new Pod was created to restore the previous state.

Finally, increase replicas and reapply the manifest. Note that replicas will change to match the value you changed.


### Task K4: Using a new version of the container

Version 2 of our "products" service has a new endpoint `/info`. Let's release it in our cluster!

Start by checking our pods:

```sh
kubectl get po

NAME                  READY   STATUS    RESTARTS   AGE
msip-products-8fn6n   1/1     Running   0          9m6s
msip-products-9phgb   1/1     Running   0          8m18s
msip-products-pjlhd   1/1     Running   0          9m6s
```

Next, open our `replicaset.yml` file and change `:v1` with `:v2` in the container tag. Reapply the manifest and check our pods again:

```sh
kubectl apply -f replicaset.yml
kubectl get po

NAME                  READY   STATUS    RESTARTS   AGE
msip-products-8fn6n   1/1     Running   0          11m
msip-products-9phgb   1/1     Running   0          10m
msip-products-pjlhd   1/1     Running   0          11m
```

Something seems wrong; the pods did not change even if the ReplicaSet was applied successfully.

Describe one of the pods:

```sh
kubectl describe pod (PodName)
```

And... wait! The Pod still uses the version `v1`! Let's delete this Pod.

```sh
kubectl delete pod (PodName)
```

Recheck the Pods: the ReplicaSet will restore the missing one. Describe this new pod and see that it uses `v2` now:

```sh
kubectl describe pod (NewPodName)
```

But the other Pods still use `v1` until you remove them. How can we fix this?


### Task K5: Deployments

Remove the previous ReplicaSet:

```sh
kubectl delete replicaset msip-products
```

Create a copy of the `replicaset.yml` as `deployment.yml`.

Restore the version of the image to `v1` and replace the value of `kind` with `Deployment`. Next, apply the manifest:

```sh
kubectl apply -f deployment.yml
```

Change the version of the image to `v2` and reapply the manifest. Then, check the pods a few times. Note that the Deployment will terminate the old pods and create new ones in place:

```sh
kubectl apply -f deployment.yml
kubectl get po
```

Finally, let's scale the Deployment:

```sh
kubectl scale deployment msip-products --replicas=10
```

### Task K6: Creating a Service

A Service helps us access the application and may act as a load balancer. Create a file named `service.yml` with the following content:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: msip-products
spec:
  # Filter pods to use (see matchLabels in Deployment)
  selector:
    app: products
  type: ClusterIP
  ports:
    - name: msip-products-port
      port: 80 # Service port
      targetPort: 3000 # Container port
      protocol: TCP
```

Apply the manifest and check the services. Note that it has an internal IP:

```sh
kubectl apply -f service.yml
kubectl get services
```

Finally, use the `port-forward` command to expose the service to local access:

```sh
kubectl port-forward svc/msip-products 8080:80
```
