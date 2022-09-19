# DataLake as a Service with RUCIO and JupyterHub on Kubernetes

This is a complete installation guide for data access from a DataLake with RUCIO using JupyterNotebooks and OIDC token-based authentication.


*Objectives*:

- Provide access to big data located in a Datalake through an interactive analysis service such as Jupyter Notebook.
- Configure access to data and service using Identity Access Management platform.

In the figure below you can see the general scheme of the platform where JupyterHub is deployed, and the RUCIO RSE.

## Structure of the deployment

![imagen](https://user-images.githubusercontent.com/7033451/191051003-4543d728-1456-43d2-bd22-f45a400aa42d.png)

