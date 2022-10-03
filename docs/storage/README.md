# NFS Storage on RSE and Workers Nodes (Kubernetes

Overall steps:

- NFS Server on StoRM VM - this is to provision the physical storage that backs the StoRM-WebDAV RSE to the workers that our JupyterHub instance runs workloads on.
- NFS Client installed on each worker that the JHub service runs on - required to interact with the NFS server provisioning the storage.
- Mount the NFS-managed StoRM path to a common location on each worker - this will allow the StoRM storage to be visible to each worker node.
- Create PVC or HostPath to make storage K8s accessible - this allows the storage to be mounted in K8s pods.
- Mount this PVC in the JHub user pods - this provides read-only access to the StoRM storage from within the Jupyter user environments.
- Add a test environment which has the rucio-jupyterlab extension - this includes the Rucio client and Rucio UI elements which allow the Jupyter user to query/issue commands to Rucio.
- Configure Rucio client - additional configuration to enable interaction with the SKAO Rucio prototype via CLI
- Modify notebook envs and JHub service - enable interaction with the SKAO Rucio prototype via UI

## Installing NFS server on StoRM-WebDav Server (the RSE, Rucio Storage Element)

Check if you can access to your master node and workers nodes from Kubernetes:

- Worker node #1

```
ssh -o StrictHostKeyChecking=no -o ForwardAgent=yes -o ProxyJump=core@161.111.167.184 core@192.168.100.100
```

- Worker node #2

```
ssh -o StrictHostKeyChecking=no -o ForwardAgent=yes -o ProxyJump=core@161.111.167.184 core@192.168.100.101
```

If yes, we go to the next step, Add NFS service to the RSE.

Go to your RSE server and install NFS service:

```
sudo yum install nfs-utils nfs-utils-lib
```

Update service to set it on boot time:

```
systemctl start nfs-server.service
systemctl enable nfs-server.service
systemctl status nfs-server.service
```



## Installing NFS client on Kubernetes Worker's nodes
