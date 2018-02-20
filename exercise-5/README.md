# Exercise 5 - Installing Istio

### Downloading the Istio client

1. You can download the Istio client from https://github.com/istio/istio/releases directly or get the latest version by using the curl command. 
   ```
   curl -L https://git.io/getLatestIstio | sh -
   ```

2. Extract the installation files.
   
3. Add the `istioctl` client to your PATH environment variable. 
 
   **Example for MacOS or Linux: **
   ```
   export PATH=$PWD/istio-[version]/bin:$PATH
   ```
   Replace `[version]` with the Istio version number that you downloaded. 

### Installing Istio on your Kubernetes cluster

1. Navigate in to the Istio installation directory.

   ```
   cd [path_to_istio-version]
   ```
   
2. Add the `istio-system` namespace to the Prometheus add-on configuration file. **Note:** This is a bug in the Istio version 0.5.1 that must be fixed.
   ```sh
   echo "  namespace: istio-system" >> install/kubernetes/addons/prometheus.yaml 
   ```
   
3. Install Istio on your Kubernetes cluster.
   ```
   kubectl apply -f install/kubernetes/istio-auth.yaml
   ```

4. Install the add-ons for Grafana, Prometheus, and Zipkin
   ```sh
   kubectl apply -f install/kubernetes/addons/zipkin.yaml
   kubectl apply -f install/kubernetes/addons/grafana.yaml
   kubectl apply -f install/kubernetes/addons/prometheus.yaml
   kubectl apply -f install/kubernetes/addons/servicegraph.yaml
   ```

5. Watch the deployment of Istio and related add-ons by using the watch flag (`-w`) when listing Kubernetes resources. Istio and all related add-ons are deployed as services and pods in to the Kubernetes namespace `istio-system`. Run these commands in two separate terminal windows. 
   ```sh
   kubectl get pods -w --all-namespaces
   ```
   ```sh
   kubectl get services -w --all-namespaces
   ```

#### [Continue to Exercise 6 - Creating a service mesh with Istio Proxy](../exercise-6/README.md)
