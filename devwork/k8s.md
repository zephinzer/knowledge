# Kubernetes

- - -

## Atomics

### Deployments

#### Using `kubectl`
##### Creating
> This assumes we want to start an `nginx` service using the `nginx` image, exposing port 80.

```bash
$ kubectl run nginx --image=nginx --port=80
```

##### Exposing
> This exposes an existing deployment named `nginx` which has port 80 exposed.

```bash
$ kubectl expose deployment nginx --target-port=80 --type=NodePort
```

##### Scaling
> This scales a deployment named `nginx` to use 2 replicas.

```bash
$ kubectl scale deployment nginx --replicas=2
```

##### Updating
> This updates a deployment using a `specfile`

```bash
$ kubectl apply -f ./path/to/specfile.yaml
```

#### Using Specfile
##### Template
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

##### Variables

`__DEPLOYMENT_NAME__` : The name of your deployment

`__DEPLOYMENT_TEMPLATE_NAME__` : The name of your template

`__DEPLOYMENT_CONTAINER_NAME__` : The name of your container

`__DEPLOYMENT_CONTAINER_PORT__` : The port which your container listens on

`__PROJECT_NAME__` : Your project name in Google Cloud Platform (GCP)

`__IMAGE_NAME__` : Your image's name in Google Container Registry (GCR)


- - -

### Ingresses
 
#### Using Specfile

##### Template for `nginx-ingress-controller`

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

- - -

### Secrets

#### Using `kubectl`

##### Creating a TLS Secret resource

```bash
$ kubectl create secret tls __SECRET_NAME__ --key ./path/to/key --cert ./path/to/crt
```

#### Using Specfile

##### Template for TLS Secret

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

- - -

## Useful Stuff

### Automatic Let's Encrypt SSL Cert Provisioning
The tool to use is `kube-lego`. Check out:

- [The GitHub Repository](https://github.com/jetstack/kube-lego)
- [This Useful Guide](https://www.matt-j.co.uk/2017/03/03/automatic-dns-and-ssl-on-kubernetes-with-letsencrypt-part-2/)

Basic instructions follows:

#### Set up annotations on your Ingress
Set up your target Ingresses so that they contain the following annotation in the `metadata.annotations[]` field:

`kubernetes.io/tls-acme: "true"`

#### Add the TLS specifications
Next, add the `tls` field to your `spec` field as follows:

```yaml
...
spec:
  ...
  tls:
  - secretName: secret1-tls
    hosts:
    - example.com
    - api.example.com
  - secretName: secret2-tls
    hosts:
    - example2.com
```

`kube-lego` will detect the annotation above, and read from the `tls` section. For the above example, two certificates will be registered, one for `example.com` and `api.example.com`, and the second certificate will be for `example2.com`. The two certificates will be automatically created as Kubernetes Secrets with the names `secret1-tls` and `secret2-tls` in the namespace which your Ingress exists in.

> It doesn't matter that `secret1-tls` and `secret2-tls` do not exist, they will be auto-created by `kube-lego`. Good stuff.

#### Deploy `kube-lego`

Create a specfile with the following contents:

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: kube-lego-cm
  namespace: kube-system
data:
  lego.email: "email@example.com"
  lego.url: "https://acme-v01.api.letsencrypt.org/directory"
  # staging: "https://acme-staging.api.letsencrypt.org/directory"
  # production: "https://acme-v01.api.letsencrypt.org/directory"

---

kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: kube-lego
  namespace: kube-system
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: kube-lego
    spec:
      containers:
      - name: kube-lego
        image: jetstack/kube-lego:0.1.5
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
        env:
        - name: LEGO_EMAIL
          valueFrom:
            configMapKeyRef:
              name: kube-lego-cm
              key: lego.email
        - name: LEGO_URL
          valueFrom:
            configMapKeyRef:
              name: kube-lego-cm
              key: lego.url
        - name: LEGO_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: LEGO_POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        readinessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 10
          timeoutSeconds: 3
```

#### Verifying Success/Debugging
To see what is happening, identify the Pod running the specified Deployment:

```bash
$ kubectl get pods -n=kube-system
```

Search for a pod named `kube-lego-XXXXXXXXXX-yyyyy` and check the logs:

```bash
$ kubectl log -n=kube-system -f kube-lego-XXXXXXXXXX-yyyyy
```

On success, you should see something resembling the following where:

- `example.com` is a specified domain in an entry in the `tls` field
- `secret-name-tls` is the `metadata.name` of the Secret as specified in the `secretName` field in an entry of the `tls` field.
- `your-ingress` is the `metadata.name` of your Ingress resource

```
time="2017-10-24T17:24:08Z" level=info msg="Attempting to create new secret" context=secret name=secret-name-tls namespace=default
time="2017-10-24T17:24:08Z" level=info msg="no cert associated with ingress" context="ingress_tls" name=your-ingress namespace=default
time="2017-10-24T17:24:08Z" level=info msg="requesting certificate for example.com" context="ingress_tls" name=your-ingress namespace=default
time="2017-10-24T17:24:08Z" level=info msg="Attempting to create new secret" context=secret name=kube-lego-account namespace=kube-system
time="2017-10-24T17:24:09Z" level=info msg="if you don't accept the TOS (https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf) please exit the program now" context=acme
time="2017-10-24T17:24:09Z" level=info msg="created an ACME account (registration url: https://acme-v01.api.letsencrypt.org/acme/reg/23208775)" context=acme
time="2017-10-24T17:24:09Z" level=info msg="Attempting to create new secret" context=secret name=kube-lego-account namespace=kube-system
time="2017-10-24T17:24:09Z" level=info msg="Secret successfully stored" context=secret name=kube-lego-account namespace=kube-system
time="2017-10-24T17:24:46Z" level=info msg="authorization successful" context=acme domain=example.com
time="2017-10-24T17:24:46Z" level=info msg="successfully got certificate: domains=[example.com] url=https://acme-v01.api.letsencrypt.org/acme/cert/034ee9178e8435897ea6a9069e2e8b9d5347" context=acme
time="2017-10-24T17:24:46Z" level=info msg="Attempting to create new secret" context=secret name=secret-name-tls namespace=default
time="2017-10-24T17:24:46Z" level=info msg="Secret successfully stored" context=secret name=secret-name-tls namespace=default
```

#### Troubleshooting/Common Errors

##### 403 Unauthorized

`authorization failed after 1m0s: getting authorization failed: 403 urn:acme:error:unauthorized: No registration exists matching provided key`

This error happens when you first run `kube-lego-nginx` with the staging Let's Encrypt URL and then again with the production URL. To resolve this, delete the `kube-lego-account` secret. Search for it:

```bash
$ kubectl get secret -n=kube-system | grep kube-lego
```

Down the `kube-lego` deployment:

```bash
$ kubectl delete deploy -n=kube-system kube-lego
$ kubectl delete cm -n=kube-system kube-lego-cm
```

Then delete the `kube-lego-account`:

```bash
$ kubectl delelte secret -n=kube-system kube-lego-account
```

Confirm you are using the **production** Let's Encrypt URL instead of the staging, and deploy the above specfile again. 

- - -

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

- - -

## Useful Links

- [Advanced Ingress Configuration](https://docs.giantswarm.io/guides/advanced-ingress-configuration/)
- [Troubleshooting ingress and service traffic flows](http://blog.wercker.com/troubleshooting-ingress-kubernetes)
- [Tuning NGINX behind Google Cloud Platform HTTP(S) Load Balancer](https://blog.percy.io/tuning-nginx-behind-google-cloud-platform-http-s-load-balancer-305982ddb340)
- [Kubernetes made easy: Develop and deploy on a cloud cluster (Part 3: Ingress Controller)](https://beroux.com/english/articles/kubernetes/?part=3)