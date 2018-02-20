## Exercise 7 - Istio Ingress controller

The components deployed on the service mesh by default are not exposed outside the cluster. External access to individual services so far has been provided by creating an external load balancer on each service.

A Kubernetes Ingress rule can be created that routes external requests through the Istio Ingress controller to the backing services.

#### Configuring Guestbook Ingress routes with the Istio Ingress controller

1. Configure the guestbook UI default route with the Istio Ingress controller.

    ```sh
    kubectl apply -f guestbook/guestbook-ingress.yaml
    ```

2. Review the YAML file for your Ingress controller. In this file, the Ingress class is specified as `kubernetes.io/ingress.class: istio`, which routes every request to the Istio Ingress controller. When you look at the Ingress rules that are defined in this YAML, you see that requests are routed to either the `helloworld-service` or `guestbook-ui` service, depending on the URL path that is used in the request. 

    ```sh
    kubectl describe ingress

    Name:             simple-ingress
    Namespace:        default
    Address:          
    Default backend:  default-http-backend:80 (<none>)
    Rules:
      Host  Path  Backends
      ----  ----  --------
      *     
            /hello/.*   helloworld-service:8080 (<none>)
            /.*         guestbook-ui:80 (<none>)
    Annotations:
    Events:  <none>
    ```

3. Get the `External-IP` of the Istio Ingress controller.

    ```sh
    kubectl get service istio-ingress -n istio-system

    NAME                   CLUSTER-IP      EXTERNAL-IP      PORT(S)                       AGE
    istio-ingress          10.31.244.185   169.47.103.138   80:31920/TCP,443:32165/TCP    1h
    ```

4. Store the external IP address from the previous command as a session variable.
   
    ```sh
    export INGRESS_IP=[external_IP]
    ```

5. Use the external IP address to access the guestbook UI in your web browser.
   ```
   http://[external_IP]
   ```
   
6. Access the Hello World service and see the response JSON in your web browser.
   ```
   http://[external_IP]/hello/world
   ```

7. Curl the guestbook service.
   ```
   curl http://$INGRESS_IP/echo/universe
   ```

6. Curl the helloworld service:
   ```
   curl http://$INGRESS_IP/hello/world
   ```

7. Curl the guestbook service multiple times. Every request that you send to your guestbook service is load balanced between the two hello world service instances (`v1` and `v2`) on a round robin basis. You can see the different hello world service versions in the `hostname` and `greeting` field of your CLI output. 

    ```sh
    curl http://$INGRESS_IP/echo/universe

    {"greeting":{"hostname":"helloworld-service-v1-286408581-9204h","greeting":"Hello universe from helloworld-service-v1-286408581-9204h with 1.0","version":"1.0"},
    ```

    ```sh
    curl http://$INGRESS_IP/echo/universe

    {"greeting":{"hostname":"helloworld-service-v2-1009285752-n2tpb","greeting":"Hello universe from helloworld-service-v2-1009285752-n2tpb with 2.0","version":"2.0"}

    ```

#### [Continue to Exercise 8 - Telemetry](../exercise-8/README.md)
