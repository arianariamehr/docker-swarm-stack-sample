# Build a secure Swarm cluster
We’ll build a secure swarm cluster with three manager nodes and three worker nodes.

![image](https://github.com/arianariamehr/docker-swarm-stack-sample/assets/130653489/8392ebf6-40be-47bb-8127-ab188fc0ed53)

On the networking front, you need the following ports open on routers and firewalls between nodes:  
    - 2377/tcp: for secure client-to-swarm communication  
    - 7946/tcp and udp: for control plane gossip  
    - 4789/udp: for VXLAN-based overlay networks  

The process of building a swarm is called `initializing a swarm`, and the high-level process is this: Initialize the
*first manager node* > *Join additional manager nodes* > *Join worker nodes* > *Done*.


# Initializing a new swarm
Docker nodes that are not part of a swarm are said to be in **single-engine mode**. Once they’re added to a swarm
they’re automatically switted into **swarm mode**.  
Running `docker swarm init` on a Docker host in single-engine mode will switch that node into swarm mode,
create a new swarm, and make the node the first manager of the swarm. Additional nodes can then be joined to the swarm as workers and managers.  

**Note**: If the datetime of your host did not set, you must set correct timezone before starting following steps (Guide: https://orcacore.com/set-up-time-synchronization-debian-12-bookworm/)  


1. Log on to mgr1 and initialize a new swarm.  
```
swarm-manager-01:~# docker swarm init \
    --advertise-addr 172.16.188.11:2377 \
    --listen-addr 172.16.188.11:2377
Swarm initialized: current node (d0psdx1wo3jt5vk7lgrmrlksv) is now a manager.

```

The command can be broken down as follows:  

• `docker swarm init`: This tells Docker to initialize a new swarm and make this node the first manager. It also enables swarm mode on the node.  

• `--advertise-addr`: As the name suggests, this is the swarm API endpoint that will be advertised to other nodes in the swarm. It will usually be one of the node’s IP addresses, but can be an external load-balancer address. It’s an optional flag unless you want to specify a load-balancer or specific IP address on a node with multiple interfaces.  

• `--listen-addr`: This is the IP address that the node will accept swarm traffic on. If not explicitly set, it defaults to the same value as --advertise-addr. If --advertise-addr is a load balancer, you must use --listen-addr to specify a local IP or interface for swarm traffic.  


2. List the nodes in the swarm.  
```
swarm-manager-01:~# docker node ls
ID              HOSTNAME           STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
dps...rlksv *   swarm-manager-01   Ready     Active         Leader           26.0.0

```

3. From mgr1 run the docker swarm join-token command to extract the commands and tokens required to add new workers and managers to the swarm.  
```
swarm-manager-01:~# docker swarm join-token worker 
To add a worker to this swarm, run the following command:

    docker swarm join --token  SWMTKN-1-0uahebax...c87tu8dx2c 172.16.188.11:2377

```
```
swarm-manager-01:~# docker swarm join-token manager 
To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0uahebax...ue4hv6ps3p 172.16.188.11:2377

```  

**Note**: You should ensure that your join tokens are kept secure, as they’re the
only thing required to join a node to a swarm!  


4. Log on to wrk1 and join it to the swarm using the docker swarm join command with the worker join token.  
```
swarm-worker-01:~# docker swarm join \
    --token SWMTKN-1-0uahebax...c87tu8dx2c \ \
    172.16.188.11:2377 \
    --advertise-addr 172.16.188.21:2377 \
    --listen-addr 172.16.188.21:2377

```  

This node joined a swarm as a worker. The --advertise-addr, and --listen-addr flags optional. I’ve added them as I consider it best practice to be as specific as possible when it comes to network configuration.  

5. Repeat the previous step on wrk2 and wrk3 so that they join the swarm as workers. If you’re specifying the --advertise-addr and --listen-addr flags, make sure you use wrk2 and wrk3’s respective IP addresses.  

6. Log on to mgr2 and join it to the swarm as a manager using the docker swarm join command with the manager join token.  
```
swarm-manager-02:~# docker swarm join \
    --token SWMTKN-1-0uahebax...ue4hv6ps3p \
    172.16.188.11:2377 \
    --advertise-addr 172.16.188.12:2377 \
    --listen-addr 172.16.188.12:2377

```

7. Repeat the previous step on mgr3, remembering to use mgr3’s IP address for the advertise-addr and --listen-addr flags.

8. List the nodes in the swarm by running `docker node ls` from any of the manager nodes in the swarm.
