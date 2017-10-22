# Kubernetes

- - -

## Deployments

### Using `kubectl`
#### Creating
> This assumes we want to start an `nginx` service using the `nginx` image, exposing port 80.

```bash
$ kubectl run nginx --image=nginx --port=80
```

#### Exposing
> This exposes an existing deployment named `nginx` which has port 80 exposed.

```bash
$ kubectl expose deployment nginx --target-port=80 --type=NodePort
```

#### Scaling
> This scales a deployment named `nginx` to use 2 replicas.

```bash
$ kubectl scale deployment nginx --replicas=2
```

#### Updating
> This updates a deployment using a `specfile`

```bash
$ kubectl apply -f ./path/to/specfile.yaml
```

### Using Specfile
#### Template
Save the following as a `.yaml` file:

```yaml
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: __DEPLOYMENT_NAME__
  labels:
    app: __DEPLOYMENT_NAME__
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    maxUnavailable: 1
    maxSurge: 4
  selector:
    matchLabels:
      app: __DEPLOYMENT_NAME__
  template:
    metadata:
      labels:
        app: __DEPLOYMENT_TEMPLATE_NAME__
    spec:
      containers:
      - name: __DEPLOYMENT_CONTAINER_NAME__
        image: asia.gcr.io/__PROJECT_NAME__/__IMAGE_NAME__:1.0.0
        imagePullPolicy: Always
        ports:
        - containerPort: __DEPLOYMENT_CONTAINER_PORT__
```

#### Variables

`__DEPLOYMENT_NAME__` : The name of your deployment

`__DEPLOYMENT_TEMPLATE_NAME__` : The name of your template

`__DEPLOYMENT_CONTAINER_NAME__` : The name of your container

`__DEPLOYMENT_CONTAINER_PORT__` : The port which your container listens on

`__PROJECT_NAME__` : Your project name in Google Cloud Platform (GCP)

`__IMAGE_NAME__` : Your image's name in Google Container Registry (GCR)


- - -

## Ingresses
 
### Using Specfile

#### Template for `nginx-ingress-controller`

> Creates a basic Ingress resource using `nginx-ingress-controller`

```yaml
kind: Ingress
apiVersion: extensions/v1beta1
metadata:
  name: __INGRESS_NAME__
  labels:
    app: __INGRESS_NAME__
    url: "__DOMAIN_NAME__"
  annotations:
    kubernetes.io/ingress.class: "nginx"
    kubernetes.io/ingress.global-static-ip-name: __GLOBAL_STATIC_IP_ADDRESS__
spec:
  backend:
    serviceName: __BACKEND_DEFAULT_SERVICE_NAME__
    servicePort: __BACKEND_DEFAULT_SERVICE_PORT__
  rules:
    - host: __DOMAIN_NAME__
      http:
        paths:
        - path: /
          backend:
            serviceName: __BACKEND_LIVE_SERVICE_NAME__
            servicePort: __BACKEND_LIVE_SERVICE_PORT__
```

`__BACKEND_DEFAULT_SERVICE_NAME__` : A default application which should return 404 on `/` endpoint and 200 on `/healthz` endpoint

`__BACKEND_DEFAULT_SERVICE_PORT__` : Port which the default application listens on at the Service level

`__BACKEND_LIVE_SERVICE_NAME__` : Your application's Service's name

`__BACKEND_LIVE_SERVICE_NAME__` : Port which your application listens on at the Service level

`__DOMAIN_NAME__` : Domain name for accessing your application

`__GLOBAL_STATIC_IP_ADDRESS__` : Name for a created reserved global static IP address

`__INGRESS_NAME__` : Name for your Ingress resource

## Secrets

### Using `kubectl`

#### Creating a TLS Secret resource

```bash
$ kubectl create secret tls __SECRET_NAME__ --key ./path/to/key --cert ./path/to/crt
```

### Using Specfile

#### Template for TLS Secret

```yaml
kind: Secret
apiVersion: v1
metadata:
  name: __SECRET_NAME__
type: kubernetes.io/tls
data:
  tls.key: __BASE64_ENCODED_KEY__
  tls.crt: __BASE64_ENCODED_CERT__
```

`__SECRET_NAME__` : Referenceable ID of this Secret resource

`__BASE64_ENCODED_KEY__` : Run `cat privateKey.key | base64` to get the base64 encoded private key

`__BASE64_ENCODED_CERT__` : Run `cat certificate.crt | base64` to get the base64 encoded certificate file

## Useful Aliases
> Include this inside your `~/.profile` or specific `.[?]shrc` file

```sh
alias kc='kubectl';
alias kca='kubectl apply -f';
alias kcd='kubectl describe';
alias kcdp='kubectl describe pod';
alias kcdn='kubectl describe node';
alias kcdd='kubectl describe deployment';
alias kcds='kubectl describe service';
alias kcdsec='kubectl describe sercret';
alias kcdi='kubectl describe ing';
alias kcdcm='kubectl describe cm';
alias kcdel='kubectl delete';
alias kcg='kubectl get';
alias kcgp='kubectl get pods -o wide';
alias kcgn='kubectl get nodes -o wide';
alias kcgep='kubectl get ep -o wide';
alias kcgd='kubectl get deploy -o wide';
alias kcgs='kubectl get svc -o wide';
alias kcgsec='kubectl get secrets -o wide';
alias kcgi='kubectl get ing -o wide';
alias kcgcm='kubectl get cm -o wide';
alias kcl='kubectl logs';
alias kclf='kubectl logs -f';
alias kcexec='kubectl exec';
```

## Useful Links

- [Quickstart for Google Container Engine](https://cloud.google.com/container-engine/docs/quickstart)
- [Deploying a containerized web application](https://cloud.google.com/container-engine/docs/tutorials/hello-app)
- [Setting up HTTP Load Balancing with Ingress](https://cloud.google.com/container-engine/docs/tutorials/http-balancer)
- [Advanced Ingress Configuration](https://docs.giantswarm.io/guides/advanced-ingress-configuration/)
- [Troubleshooting ingress and service traffic flows](http://blog.wercker.com/troubleshooting-ingress-kubernetes)