# Exercise 8 - Telemetry

## Configuring Istio to automatically gather telemetry data 

1. Create a rule to collect telemetry data for your guestbook service. 
    ```sh
    istioctl create -f guestbook/guestbook-telemetry.yaml
    ```
2. Run a script to generate a small work load for the app.
    ```sh
    while sleep 0.5; do curl http://$INGRESS_IP/hello/world; done
    ```
3. Wait until you see a few curl commands in your CLI. 
4. Cancel the script by pressing `Control` + `c`. 

## Viewing the guestbook telemetry data

### Grafana
1. Establish port forwarding from local port 3000 to the Grafana instance. 
   ```sh
   kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana \
   -o jsonpath='{.items[0].metadata.name}') 3000:3000
   ```

2. Browse to http://localhost:3000.
3. Navigate to the Istio dashboard.

### Zipkin
1. Establish port forwarding from local port 9411 to the Zipkin instance.
   ```sh
   kubectl port-forward -n istio-system \
   $(kubectl get pod -n istio-system -l app=zipkin -o jsonpath='{.items[0].metadata.name}') \
   9411:9411
   ```
2. Browse to http://localhost:9411.

### Prometheus
1. In a new terminal window, establish port forwarding from local port 9090 to the Prometheus instance.
   ```sh
   kubectl -n istio-system port-forward \
   $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') \
   9090:9090
   ```
2. Browse to http://localhost:9090/graph. 
3. In the `Expression` input box, enter: `request_count`. 
4. Click **Execute**.


### Service Graph
1. In a new terminal window, establish port forwarding from local port 8088 to the Service Graph instance.
   ```sh
   kubectl -n istio-system port-forward \
   $(kubectl -n istio-system get pod -l app=servicegraph -o jsonpath='{.items[0].metadata.name}') \
   8088:8088
   ```
2. Browse to http://localhost:8088/dotviz.

#### Mixer Log Stream

```sh
kubectl -n istio-system logs $(kubectl -n istio-system get pods -l istio=mixer -o jsonpath='{.items[0].metadata.name}') mixer | grep \"instance\":\"newlog.logentry.istio-system\"
```

#### [Continue to Exercise 9 - Request Routing and Canary Deployments](../exercise-9/README.md)
