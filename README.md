# kubernetes-coreos-do
Walkthrough for setup Kubernetes on CoreOS on DigitalOcean.

### Create Master Instance
1. Create a new droplet via DO interface, and choose following options 
1. Droplet Hostname: master
1. Select Size: Any
1. Select Region: Any with private networking support
1. Available Settings: "Private Networking", "Enable User Data"
1. Put [cloud-config](https://github.com/SergeyCherepanov/kubernetes-coreos-do/blob/master/cloud-config-master.yml) to user data textarea
1. Select Image: CoreOS (stable)
1. Choose you SSH key
1. Press a "Create a Droplet" button

### Prepare cloud-config for node (minion) Instance:
When the droplet will be created, login to system:
  
    ssh core@%MASTER_DROPLET_EXTERNAL_IP%

Take and remember internal ip address (Note: if you use other cloud provider, network interface may be different from eth1, if is true, you also have to  change it in all cloud-configs):

    echo $(ip addr | grep inet | grep eth1 | grep global | awk '{print $2}' | sed -e 's/\/.*$//')
    
Replace all %MASTER_IP% placeholders in node [cloud-config](https://github.com/SergeyCherepanov/kubernetes-coreos-do/blob/master/cloud-config-node.yml) to the master ip.

### Create node (minion) Instance
Create a new droplet via DO interface, and choose following options 

1. Droplet Hostname: node-001
1. Select Size: Anyone you want
1. Select Region: Must be same as the master node
1. Available Settings: "Private Networking", "Enable User Data"
1. Put your changed cloud-config with MASTER IP to user data textarea
1. Select Image: CoreOS (stable)
1. Choose you SSH key
1. Press a "Create a Droplet" button

### Completion

Login to your master instance:

    ssh core@%MASTER_DROPLET_EXTERNAL_IP%

Download Kubectl:

    sudo /usr/bin/wget -N -P /opt/bin https://storage.googleapis.com/kubernetes-release/release/v0.15.0/bin/linux/amd64/kubectl
    sudo chmod +x /opt/bin/kubectl 
  
Check whether the all your nodes registered in Kubernetes

    /opt/bin/kubectl get nodes 
