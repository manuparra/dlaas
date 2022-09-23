# DataLake as a Service with RUCIO and JupyterHub on Kubernetes

This is a complete installation guide for data access from a DataLake with RUCIO using JupyterNotebooks and OIDC token-based authentication.


*Objectives*:

- Provide access to big data located in a Datalake through an interactive analysis service such as Jupyter Notebook.
- Configure access to data and service using Identity Access Management platform.

In the figure below you can see the general scheme of the platform where JupyterHub is deployed, and the RUCIO RSE.

## Structure of the deployment

![imagen](https://user-images.githubusercontent.com/7033451/191051003-4543d728-1456-43d2-bd22-f45a400aa42d.png)

## Installation steps

### Deployment of a Kubernetes Cluster

If you have this step ready, please go to the next step (*Deployment of a Rucio Storage Element*).

If not, please follow the next instructions on how to set-up a kubernetes cluster with at least 2 nodes: https://kubernetes.io/docs/setup/

### Deployment of a Rucio Storage Element with StoRM and WebDav

If you have this step ready, please go to the next step (*Deployment of the Datalake as a Service*).

If not, please follow the next instructions: https://github.com/manuparra/rucio-rse-StoRM-webdav

### Deployment of the Datalake as a Service with JupyterHub 

Create first configuration:

```
helm upgrade --cleanup-on-fail --install jhub-dl jupyterhub/jupyterhub --namespace jhub-dl --create-namespace --values dl.deploy.hub.yaml
```

Apply `configMap`:

```
kubectl apply  dl.cofigmap.yaml
```

Deploy single user configuration: 

```
helm upgrade --cleanup-on-fail jupyterhub/jupyterhub --namespace jhub-dl  --values dl.deploy.singleuser.yaml
```



