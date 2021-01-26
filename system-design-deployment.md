# System design

## Create packages

// TODO: create debian packages


## LXD containers

- install `apt install lxd`


```
$ lxd init
$ sudo lxc image list
$ sudo lxc launch images:alpine/3.5 web
$ sudo lxc list
$ sudo exec web -- /bin/sh
```
## HA Clustering/Replication


- Install `pcs, pacemaker, fence-agents-all` in 2 hosts

-  Add both nodes to `/etc/hosts`

```
sudo pcs cluster auth lfce-server lfce-client -h hacluster -p hacluster
sudo pcs cluster setup --name lfce-cluster --force lfce-server lfce-client
sudo pcs cluster start --all
sudo pcs cluster status
Cluster Status:
 Stack: unknown
 Current DC: NONE
 Last updated: Tue Jan 26 21:56:04 2021
 Last change: Tue Jan 26 21:55:53 2021 by hacluster via crmd on lfce-client
 2 nodes configured
 0 resources configured

PCSD Status:
  lfce-server: Online
  lfce-client: Online
  sudo pcs property set stonith-enabled=false
  sudo pcs stonith list
  sudo pcs resources list
  # create a VIP
  sudo pcs resource create pinehead IPaddr2 ip=10.0.0.1 cidr_netmask=24 nic=ens3
  sudo pcs cluster stop lfce-client
  sudo pcs resource show
  sudo pcs cluster start lfce-client
  sudo pcs resource move pinehead
  sudo pcs cluster status
  sudo pcs constraint list --full
```

- In a production environment you should use fencing agents STONITH (shoot the other node in the head)

