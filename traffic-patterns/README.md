# Traffic Patterns

## Goals

- Blue/Green Deployment Pattern​
- Canary Deployment Pattern​
- Circuit Breaker (Disaster Recovery/HA modes active-active or active standby)​
- Use Kiali to show and test traffic flow for all patterns​

### Overview

#### Blue/Green/Canary Deployment Patterns

- Blue/Green and Canary Deployments seek to minimize downtime when upgrading environments while simultaneously providing a backup mechanism.
- Blue / Green deployments are when there are two production environments called blue and green. Since there is redundency between environments it can be ensure the upgraded environment is throughly tested before switching traffic to it.
- Canary deployments are when you incrementally test a new environment be routing small amounts of traffic before entirely migrating all users to the new environment.

#### Why Istio

- Istio is a service mesh that makes it easier to manage microservice clusters. By being able to manage/view dependencies, configurations, connectivity, and traffic, it reduces the cognitive load to the developer and provides a more maintainable approach to devops.

#### Configuring Blue/Green/Canary Deployments

##### Deployment of Duplicate Environments

Create two different pods for the same application

```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: helium-aks-green
spec:
  template:
    metadata:
      labels:
        randlabel: green
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: helium-aks-blue
spec:
  template:
    metadata:
      labels:
        randlabel: blue

```

##### Destination Rule

This is assign two subsets based on the labels created above:

```yaml

apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: helium-aks
spec:
  host: helium-aks
  subsets:
  - name: blue
    labels:
      randlabel: blue
  - name: green
    labels:
      randlabel: green

```

##### Virtual Service

You can then use the two subsets to direct traffic. In this case, route all traffic to blue.

```yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: {{ include "helium.fullname" . }}
spec:
  hosts: 
  {{- range .Values.ingress.hosts }}
    - {{ . | quote }}
  {{- end }}
  gateways:
  - {{ include "helium.fullname" . }}-gateway
  http:
  - match: 
    {{- range .Values.ingress.paths }}
      - uri:
          prefix: {{ . }}
    {{- end }}
    route:
    - destination:
        port:
          number: {{ .Values.service.port }}
        host: helium-aks
        subset: blue

```

##### Deploying default cluster

`helm upgrade helium-aks helium -f ./helium/helm-config.yaml`

##### Applying Routing Changes During Deployment

Create the following yaml file and place in aks/cluster/charts/helium. The filled out yaml file can be found by going through Kiali as described below.

```yaml

kind: VirtualService
apiVersion: networking.istio.io/v1alpha3
metadata:
  name: helium-aks
  namespace: default
  selfLink: >-
    /apis/networking.istio.io/v1alpha3/namespaces/default/virtualservices/helium-aks
  uid: 4d36d75f-9f04-46fa-a78c-308282ac848e
  resourceVersion: '11777'
  generation: 8
  creationTimestamp: '2020-11-03T18:42:39Z'
  labels:
    app.kubernetes.io/managed-by: Helm
  annotations:
    meta.helm.sh/release-name: helium-aks
    meta.helm.sh/release-namespace: default
  managedFields:
    - manager: Go-http-client
      operation: Update
      apiVersion: networking.istio.io/v1alpha3
      time: '2020-11-03T19:12:15Z'
spec:
  hosts:
    - 52.158.212.148.nip.io
  gateways:
    - helium-aks-gateway
  http:
    - match:
        - uri:
            prefix: /
      route:
        - destination:
            port:
              number: 4120
            host: helium-aks
            subset: green


```

Switch To Green:

- `cd aks/cluster/charts`

- `kubectl delete -f helium/switch-to-green.yaml`

- `kubectl apply -f helium/switch-to-green.yaml`

### Kiali Is Great For Managing Istio Configs

- Can be used to gather post processed yaml configs
  - Click on Configs
  - Navigate to Config You'd Like To See
  - Click view Yaml
  - Useful for creating yaml files that can be applied outside helm
    - Doing it this way is easier than changing VirtualService file and upgrading with helm
- Can be used to delete configs that weren't deleted using `helm delete`
