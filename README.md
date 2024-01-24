# Introduction

SeAT k8s is a sample of kubernetes yaml files used to deploy [Eve Online SeAT tool](https://eveseat.github.io/docs/)

It depends on the following external tools already installed in a cluster :

- A mariadb database
- A redis instance (used for caching)
- Traefik for reverse proxying using [IngressRoute](https://doc.traefik.io/traefik/routing/providers/kubernetes-crd/)
- [Skaffold](https://skaffold.dev/) available on your computer
- kubectl also available on your computer and configured to point to the target cluster

I am also using glusterfs as a storage (pv/pvc claims) manager.

# Install

Before installing there are several steps you will have to perform.

## Configure

1) Create a namespace or reuse an existing namespace. You can use the file k8s/namespace.yaml to create the eve-corp
   namespace
2) Search all yaml files for namespace references and rename it to the namespace you use.
3) Create a service account (see k8s/rbac.yaml) in that namespace and then replace the service account name in every
   yaml files for the key serviceAccountName
4) Edit the file in k8s/seat/routing/ingress-route.example.yaml and set your hostname and your cert resolver name and
   rename the file to ingress-route.yaml
5) Copy the .env.example file and rename it .env and change the values configured in it
6) Create a kubernetes configmap using the command

```
kubectl create configmap eve-seat-env-config -n your-namespace --from-env-file=.env
```

It is recommanded to not put sensible keys like secrets (db password for example) in the configmap but to create an
alternative .env.secrets file and create a secret using

```
kubectl create secret generic eve-seat-secrets -n your-namespace --from-env-file=.env.secrets
```

## Deploy

1) Deploy the file k8s/seat/pvc.yaml in order to create the volume for SeAT (used to persist the storage directory)
2) Deploy the app using skaffold at the root of this project:

```
skaffold run
```

