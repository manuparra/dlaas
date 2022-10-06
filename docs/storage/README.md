# NFS Storage on RSE and Workers Nodes (on Kubernetes nodes)

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
sudo yum install nfs-utils
```

Update service to set it on boot time:


```
sudo chkconfig --levels 235 nfs on
```

or

```
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

Edit `/etc/exports` :


```
/storage/dteam/disk/dev/deterministic/ 192.168.100.100(ro,sync,no_root_squash,no_subtree_check) 192.168.100.101(ro,sync,no_root_squash,no_subtree_check)
```

*Note*: We are assuming that our RSE folder is living: ``/storage/dteam/disk/dev/deterministic`` and 192.168.100.100 is the IP or the Worker Node #1 and the next for the another node.

```
sudo exportfs -a
sudo systemctl restart nfs-server.service
```

Then check the status:

````
sudo systemctl status nfs-server.service
````

Now verify the list of exported FS's:

```
sudo exportfs -arv
```

Then we go to the workers nodes in order to import the exported folder from the RSE:

```
ssh -o StrictHostKeyChecking=no -o ForwardAgent=yes -o ProxyJump=core@161.111.167.184 core@192.168.100.100
```

Create the same folder structure:

```
sudo mkdir -p /storage/dteam/disk/dev/deterministic/
```

If you are on a CoreOS you will probably to use:

```
sudo mkdir -p /var/storage/dteam/disk/dev/deterministic/
```

And then create a sym link to the ``/storage/...``

```
sudo ln -s /var/storage/ storage
```

Then try to mount the folder:

```
sudo mount 192.168.100.204:/storage/dteam/disk/dev/deterministic/ /var/storage/dteam/disk/dev/deterministic/
```


## Installing NFS client on Kubernetes Worker's nodes
