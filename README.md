# DataLake as a Service with RUCIO and JupyterHub on Kubernetes

This is a complete installation guide for data access from a DataLake with RUCIO using JupyterNotebooks and OIDC token-based authentication.

  * [Structure of the deployment](#structure-of-the-deployment)
  * [Installation steps](#installation-steps)
    + [Deployment of a Kubernetes Cluster](#deployment-of-a-kubernetes-cluster)
    + [Deployment of a Rucio Storage Element with StoRM and WebDav](#deployment-of-a-rucio-storage-element-with-storm-and-webdav)
    + [Deployment of the Datalake as a Service with JupyterHub](#deployment-of-the-datalake-as-a-service-with-jupyterhub)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>



*Objectives*:

- Provide access to big data located in a Datalake through an interactive analysis service such as Jupyter Notebook.
- Configure access to data and service using Identity Access Management platform.

In the figure below you can see the general scheme of the platform where JupyterHub is deployed, and the RUCIO RSE.

## Structure of the deployment

![imagen](https://user-images.githubusercontent.com/7033451/191051003-4543d728-1456-43d2-bd22-f45a400aa42d.png)


  * [Structure of the deployment](#structure-of-the-deployment)
  * [Installation steps](#installation-steps)
    + [Deployment of a Kubernetes Cluster](#deployment-of-a-kubernetes-cluster)
    + [Deployment of a Rucio Storage Element with StoRM and WebDav](#deployment-of-a-rucio-storage-element-with-storm-and-webdav)
    + [Deployment of the Datalake as a Service with JupyterHub](#deployment-of-the-datalake-as-a-service-with-jupyterhub)


## Installation steps

### Deployment of a Kubernetes Cluster

If you have this step ready, please go to the next step (*Deployment of a Rucio Storage Element*).

If not, please follow the next instructions on how to set-up a kubernetes cluster with at least 2 nodes: https://kubernetes.io/docs/setup/

### Deployment of a Rucio Storage Element with StoRM and WebDav

If you have this step ready, please go to the next step (*Deployment of the Datalake as a Service*).

If not, please follow the next instructions: https://github.com/manuparra/rucio-rse-StoRM-webdav

### Deployment of the Datalake as a Service with JupyterHub 

:one: Configure Storage on Workers nodes: follow the next steps https://github.com/manuparra/dlaas/tree/main/docs/storage

This will enable access to the RSE from worker nodes and containers.

:two: Create first configuration:

a) Go to the header node of kubernetes.

b) Clone this repository within kubernetes cluster master node.

c) Add and update `helm`

```
helm repo add jupyterhub https://jupyterhub.github.io/helm-chart/
helm repo update
```


d) Install our first configuration:

```
helm upgrade --cleanup-on-fail --install jhub-dl jupyterhub/jupyterhub --namespace jhub-dl --create-namespace --values dl.deploy.hub.yaml
```

Check the status of this deployment:

```
watch kubectl get all -n jhub-dl
```

:three:  Apply `configMap`:

```
kubectl apply -f dl.configmap.yaml
```

Check the status of this deployment:

```
watch kubectl get all -n jhub-dl
```

:four:  Deploy single user configuration: 

```
helm upgrade --cleanup-on-fail jupyterhub/jupyterhub --namespace jhub-dl  --values dl.deploy.singleuser.yaml
```

Check the status of this deployment:

```
watch kubectl get all -n jhub-dl
```

:five: :warning:  Uninstalling this deployment:

```
helm uninstall jupyterhub -n jhub-dl
```


