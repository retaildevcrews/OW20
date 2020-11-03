# Autoscaling the application

## Goals

- Expand current cluster by adding new nodes
- Use a horizontal pod autoscaler (HPA) resource to scale pods in same cluster
- *Bonus: Look into scaling Cosmos DB when the load is high as well*

### HPA with Prometheus Adapter

The default Istio installation includes the Prometheus metrics server, where various network metrics are stored. By porting these Prometheus metrics as a custom Kubernetes metric `custom.metrics.k8s.io/v1beta1` using a [Prometheus Adapter](https://github.com/DirectXMan12/k8s-prometheus-adapter), we can leverage the native Kubernetes Horizontal Pod Autoscaler (HPA) object to scale the pods.

For this hack session, we used the following Prometheus query to calculate the incoming requests per second for the Helium application.

```promql

avg(rate(istio_requests_total{pod_name=~"helium.*"}[1m]))

```

#### Prometheus Adapter Setup

> NOTE: All the YAML template files used in this setup are available in the manifests directory. Move and/or modify them as needed to fit your need.

Install the Prometheus Adapter and create the HPA object.

```bash

# Add the custom helm repo and install the adapter using the config file
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus-adapter prometheus-community/prometheus-adapter -f prom-adapter-config.yaml

# Create the HPA object
kubectl apply -f hpa.yaml

```

#### Visualize Scaling with Grafana

Use the Grafana monitoring tool included with the default Istio installation to observe the application pod scaling behavior in action.

```bash

# Access Grafana (live tail metrics dashboard)
istioctl dashboard grafana

# Create a custom dashboard by importing the grafana-dashboard.json file into Grafana
grafana-dashboard.json

# Save the dashboard so you can access it on future sessions in Grafana

```

You will end up with a dashboard that looks like this.

![alt text](./images/grafana-dashboard.png "Sample Grafana Dashboard")

#### Visualize Scaling with Azure Dashboards

Replace the values in the `azure-dashboard.json` file surrounded by `%%` with the proper environment variables.

```bash

sed -i "s/%%SUBSCRIPTION_GUID%%/$(eval He_Sub)/g" azure-dashboard.json && \
sed -i "s/%%AKS_RESOURCE_GROUP%%/${He_App_RG}/g" azure-dashboard.json && \
sed -i "s/%%COSMOS_RESOURCE_GROUP%%/${Imdb_RG}/g" azure-dashboard.json && \
sed -i "s/%%HE_NAME%%/${He_Name}/g" azure-dashboard.json

```
