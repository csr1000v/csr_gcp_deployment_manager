# csr_gcp_deployment_manager

CSR1000v images are publicly available in GCP Marketplace
 
This YAML file gives an example to deploy a Cisco CSR1000V using Google Cloud Deployment Manager. It creates below components in your GCP
	
	- A VPC Network, regional (us-west1)
	
	- Two subnets, 1) public subnet (10.0.0.0/24, us-west1), 2) private subnet (10.0.1.0/24, us-west1). CSR will be deployed in public subnet, application VMs (which is not included in this YAML file) should be deployed in private.
	
	- CSR1000V VM: n1-standard-1 instance type, single NIC (10.0.0.10) in public subnet, with VM level SSH key, block project-level-ssh-key
	
	- A route entry (prefix 192.168.0.0/16, next hop CSR interface): this makes VM in private subnet to use CSR to reach out to DC side  (192.168.0.0/16)
	
	- An ingress firewall rules: tcp 22 (SSH) from anywhere, udp 500/4500 (IPSEC) from anywhere
 
Please note you need to create a SSH key pair first, steps can be found here (https://cloud.google.com/compute/docs/instances/adding-removing-ssh-keys). Then public key need to be passed as metadata to CSR in YAML file, once CSR boots up, you can use private key to login.

Deploying YAML with gcloud:
 
	-  gcloud deployment-manager deployments create <deployment-name> --config <file-path>.yaml

metadata:
      items:
        - key: block-project-ssh-keys
          value: 'true'
        - key: ssh-keys
          value: |
            <username>:ssh-rsa<space><ssh_public_key><space><username>
