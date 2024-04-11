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
create a new swarm, and make the node the first manager of the swarm.

 
# Setup Worker Nodes
