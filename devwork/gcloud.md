# Google Cloud Platform (GCP)

## Installing GCloud SDK

### MacOS/Linux

```bash
$ curl https://sdk.cloud.google.com | bash
```

### Windows

Download it from https://dl.google.com/dl/cloudsdk/channels/rapid/GoogleCloudSDKInstaller.exe.

## Setting GCloud Configuration

### Set account

```bash
$ gcloud config set account __GOOGLE_ACCOUNT__
```

### Set project

```bash
$ gcloud config set project __PROJECT_ID__
```

### Set Zone

```bash
$ gcloud config set compute/zone __COMPUTE_ZONE__
```

For Singapore, use: `asia-southeast1-a`.

### Set Region

```bash
$ gcloud config set compute/region asia-southeast1
```

For Singapore, use: `asia-southeast1`

## Compute Engine (GCE)

### Get all Compute Engine instances

```
$ gcloud compute instances list
```

### Create global static IP address

```bash
$ gcloud compute addresses create __IP_ADDRESS_RESOURCE_NAME__ --global
```

`__IP_ADDRESS_RESOURCE_NAME__` : This will be the referenceable ID of your static IP address.

## Container Engine (GKE)

### Installing required components

> Requires `gcloud` SDK

Install the Kubernetes add-on locally:

```bash
$ gcloud components install kubectl
```

For pipeline installation (automated installation):

```bash
$ gcloud components install kubectl -q
```

### Getting credentials 

Retrieve credentials for specified cluster and sets `kubectl` to use it:

```bash
$ gcloud container clusters get-credentials __GKE_CLUSTER_ID__
```

### Create deployment

```bash
$ kubectl run __DEPLOYMENT_NAME__ --image=__IMAGE_NAME__ --port __PORT__
```

`__DEPLOYMENT_NAME__` is an arbitrary name you'd like to prefix all your pods with.

`__IMAGE_NAME__` is your image name in GCR. See [Container Registry (GCR)](#container-registry-gcr) for more

`__PORT__` is the port which your application listens to.

### Expose deployment

```bash
$ kubectl expose __DEPLOYMENT_NAME__ --port 80 --target-port __PORT__
```

`__DEPLOYMENT_NAME__` is an arbitrary name you've defined in `kubectl run` from above Create deployment step

`__PORT__` is the port which your deployment is listening to as defined in Create deployment

### Scale deployment

```bash
$ kubectl scale __DEPLOYMENT_NAME__ --replicas=__N_REPLICAS__
```

`__DEPLOYMENT_NAME__` is an arbitrary name you've defined in `kubectl run` from above Create deployment step

`__N_REPLICAS__` is the number of replicas you wish to have.

## Container Registry (GCR)

### Pushing an image to the registry
Build your image locally and tag it according to GCR conventions:

```
$ docker build 
  --file ./path/to/Dockerfile
  --tag __GCR_HOSTNAME__/__PROJECT_ID__/__IMAGE_ID__:__VERSION__ 
  .;
```

`__GCR_HOSTNAME__` is one of `asia.gcr.io`, `us.gcr.io`.

`__PROJECT_ID__` is the ID of your project as shown in Google Cloud Platform.

`__IMAGE_ID__` is the name of the image as you want it to show up.

`__VERSION__` is an optional parameter specifying the verison of your image.

Push it to GCR using:

```bash
$ gcloud docker -- push __GCR_HOSTNAME__/__PROJECT_ID__/__IMAGE_ID__:__VERSION__
```

### Pulling an image from the registry
Pull your image using:

```bash
$ gcloud docker -- pull __GCR_HOSTNAME__/__PROJECT_ID__/__IMAGE_ID__:__VERSION__
```

## Firewall Rules

Health check pings source IP address range:

- 130.211.0.0/22
- 35.191.0.0/16

> In GCP, this is in the menu: **VPC Network > Firewall rules**

## Kubernetes Mappings

This section covers where to find which Kubernetes resources where in GCP

### Cluster

**Container Engine** > **Container clusters**

### ConfigMap

**Container Engine** > **Configuration**

### Containers

**Storage** > **Browser** > *BROWSE `/containers`*

### Deployments

**Container Engine** > **Workloads**

### Health Checks/Liveness/Readiness

**Compute Engine** > **Health checks**

### Ingresses

**Network Services** > **Load balancing**

### Services

**Container Engine** > **Workloads**

## Troubleshooting

### `error: unable to recognize "./api.yaml": no matches for /, Kind=Deployment`

The `apiVersion` root property has not been set.

### Accessing global HTTP(S) Load Balancer logs

Go to your GCP dashboard and access the left side menu item under **Logging** > **Logs**.

Select **Cloud HTTP Load Balancer**.

### Intermittent 502 Bad Gateway from global HTTP(S) Load Balancer

#### Logs Output
```json
jsonPayload: {
  @type:  "type.googleapis.com/google.cloud.loadbalancing.type.LoadBalancerLogEntry",
  statusDetails:  "failed_to_connect_to_backend"   
}
```

```json
resource: {
  labels: {
   backend_service_name:  ""
   forwarding_rule_name:  "k8s-fw-default-knearby-api-nginx-ing--c5f2619895d55113"
   project_id:  "knearby-org"
   target_proxy_name:  "k8s-tp-default-knearby-api-nginx-ing--c5f2619895d55113"
   url_map_name:  "k8s-um-default-knearby-api-nginx-ing--c5f2619895d55113"
   zone:  "global"
  }
  type:  "http_load_balancer"   
}
```
#### Related Links
- [URL Maps](https://cloud.google.com/compute/docs/load-balancing/http/url-map)


## Useful Aliases
> Include this inside your `~/.profile` or specific `.[?]shrc` file

```sh
alias gc='gcloud';
alias gce='gcloud compute';
alias gke='gcloud container';
alias gcdpush='gcloud docker -- push';
alias gcdpull='gcloud docker -- pull';
```

## Useful Links

- [Quickstart for Google Container Engine](https://cloud.google.com/container-engine/docs/quickstart)
- [Deploying a containerized web application](https://cloud.google.com/container-engine/docs/tutorials/hello-app)
- [Setting up HTTP Load Balancing with Ingress](https://cloud.google.com/container-engine/docs/tutorials/http-balancer)
- [Tuning NGINX behind Google Cloud Platform HTTP(S) Load Balancer](https://blog.percy.io/tuning-nginx-behind-google-cloud-platform-http-s-load-balancer-305982ddb340)
- [Kubernetes made easy: Develop and deploy on a cloud cluster (Part 3: Ingress Controller)](https://beroux.com/english/articles/kubernetes/?part=3)