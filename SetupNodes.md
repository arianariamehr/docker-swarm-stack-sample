# Build a secure Swarm cluster
We’ll build a secure swarm cluster with three manager nodes and three worker nodes.

![image](https://github.com/arianariamehr/docker-swarm-stack-sample/assets/130653489/8392ebf6-40be-47bb-8127-ab188fc0ed53)

On the networking front, you need the following ports open on routers and firewalls between nodes:
• 2377/tcp: for secure client-to-swarm communication
• 7946/tcp and udp: for control plane gossip
• 4789/udp: for VXLAN-based overlay networks


# Setup Manager Nodes
For creating 
 
# Setup Worker Nodes
