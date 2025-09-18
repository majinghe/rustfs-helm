# rustfs-helm

You can use this helm chart to deploy rustfs on k8s cluster.

## Parameters Overview

| parameter | description | default value |
| -- | -- | -- |
| replicaCount   | Number of cluster nodes.   |   4 or 16(Only support currently). |
|  image.repository  | docker image repository.   |  rustfs/rustfs.  |
| image.tag | the tag for rustfs docker image | "latest" |
| secret.rustfs.access_key | RustFS Acccess Key ID | `rustfsadmin` |
| secret.rustfs.secret_key | RustFS Secret Key ID | `rustfsadmin` |
| storageclass.name | The name for StroageClass. | `local-path` |


**NOTE**: [`local-path`](https://github.com/rancher/local-path-provisioner) is used by k3s. If you want to use `local-path`, running the command,

```
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/v0.0.32/deploy/local-path-storage.yaml
```

## Requirement

* Helm V3
* RustFS >= 1.0.0-alpha.56

## Installation

Running the command:

```
helm install rustfs -n rustfs --create-namespace ./
```

Check the pod status

```
kubectl -n rustfs get pods -w
NAME       READY   STATUS    RESTARTS        AGE
rustfs-0   1/1     Running   0               2m27s
rustfs-1   1/1     Running   0               2m27s
rustfs-2   1/1     Running   0               2m27s
rustfs-3   1/1     Running   0               2m27s
```

## Uninstall

Uninstalling the rustfs installation with command,

```
helm uninstall rustfs -n rustfs
```

## For Traefik user

If you use traefik to expose service, you should configure `leastconn` and `session sticky`. 

```
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: rustfs-route
  namespace: rustfs
spec:
  entryPoints:
    - web
  routes:
    - match: Host(`your.rustfs.com`)
      kind: Rule
      services:
        - name: rustfs-svc
          port: 80
          strategy: leastconn
          sticky:
            cookie:
              name: rustfs-cookie
```
