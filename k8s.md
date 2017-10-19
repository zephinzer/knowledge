# Kubernetes

## Deployments
## Creating

```bash
$ kubectl run nginx --image=nginx --port=80
```

## Exposing

```bash
$ kubectl expose deployment nginx --target-port=80 --type=NodePort
```

## Creating Basic Ingress

Save the following as `ingress.yaml`:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: basic-ingress
spec:
  backend:
    serviceName: nginx
    servicePort: 80
```

Run following command to create the ingress resource

```bash
$ kubectl apply -f ./path/to/ingress.yaml
```