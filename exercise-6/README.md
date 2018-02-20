# Exercise 6 - Creating a service mesh with Istio Proxy

### What is a service mesh?

Cloud-native apps require a new approach to manage the communication between each service. This problem is best solved by creating a dedicated infrastructure layer that handles service-to-service communication. With Istio, this infrastructure layer is created by deploying a lightweight proxy alongside each app service. This is done in a way that the application does not need to be aware of the proxy.

Moving the service communication to a separate layer provides a separation of concerns. The monitoring, management and security of communication can be handled outside of the application logic.

### What is a Kubernetes sidecar?

Pods represent small deployable units in a Kubernetes cluster and are used to group the containers that must be treated as a single unit. In most cases, each container is deployed in its own pod. However, an app might require a container and other helper containers to be deployed into one pod so that those containers can be addressed by using the same private IP address. This helper container is called a sidecar and is used to extend and enhance the main container in the pod. 

### The Istio Proxy sidecar

To create a service mesh with Istio, you update the deployment of the pods to add the Istio Proxy (based on the Lyft Envoy Proxy) as a side car to each pod. The proxy is then run as a separate container that manages all communication with that pod. 

#### Adding the Istio proxy side car to your deployment yaml

You can modify your deployment yaml and add the Istio proxy side car by running the `istioctl cube-inject` command. This process is called manual injection. Every instance of your app is now packaged with the proxy side car that runs as a separate container in the same pod. The proxy side car manages all incoming and outgoing requests for your app. 

1. Navigate in to the `istio-workshop` directory. 

2. Add the Istio proxy side car. 
   ```sh
   istioctl kube-inject -f guestbook/helloworld-deployment.yaml
   ```
3. Verify that the Istio proxy side car configuration was added as an additional container for your pod in your deployment yaml. 

   Your output looks similar to the following: 
   ```
   image: docker.io/istio/proxy_debug:[version]
   imagePullPolicy: IfNotPresent
   name: istio-proxy    
   ```
   
### Deploying Guestbook services

To demonstrate Istio, we will be using [this guestbook example](https://github.com/retroryan/spring-boot-docker). This example is built with Spring Boot, a frontend that uses Spring MVC and Thymeleaf, and two microservices. The 3 microservices that we are going to deploy are:

* Hello World service - A simple service that returns a greeting back to the user.

* Guestbook service - A service that keeps a registry of guests and the message they left.

* Guestbook UI - The front end to the app that calls the other microservices to get the list of guests, register a new guest, and get the greeting for the user when they register.

The guestbook example requires MySQL to store guestbook entries and Redis to store session information.

**Note:** Do not change the order how the services are started. Some services depend on other services to be available first before they can be started.

1. Deploy MySQL, Redis, the Hello World microservices, and the associated Kubernetes Services from the `istio-workshop` directory.

    ```sh
    cd ../istio-workshop
    kubectl apply -f <(istioctl kube-inject -f guestbook/mysql-deployment.yaml) -f guestbook/mysql-service.yaml
    kubectl apply -f <(istioctl kube-inject -f guestbook/redis-deployment.yaml) -f guestbook/redis-service.yaml
    kubectl apply -f <(istioctl kube-inject -f guestbook/helloworld-deployment.yaml) -f guestbook/helloworld-service.yaml
    kubectl apply -f <(istioctl kube-inject -f guestbook/helloworld-deployment-v2.yaml)
    ```

2. Verify that these microservices are available before continuing. **Do not procede until they are up and running.** 

    ```
    kubectl get -w deployment
    ```
    
3. Deploy the guestbook microservice.

    ```sh
    kubectl apply -f <(istioctl kube-inject -f guestbook/guestbook-deployment.yaml) -f guestbook/guestbook-service.yaml
    ```

4. Verify that guestbook is available before continuing. **Do not procede until the microservice is up and running.** 

    ```
    kubectl get -w deployment
    ```

5. Deploy the guestbook UI:

    ```sh
    kubectl apply -f <(istioctl kube-inject -f guestbook/guestbook-ui-deployment.yaml --debug) -f guestbook/guestbook-ui-service.yaml
    ```

#### [Continue to Exercise 7 - Istio Ingress controller](../exercise-7/README.md)
