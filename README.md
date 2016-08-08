Example Ansible scripts for building a full AWS stack
=============================================
These scripts build a multi-AZ set of servers including a VPC, load balancer and RDS database and deploy a simple PHP example application. Can be used as a base for automating your own deployments.

Basic usage
===========
1. Do initial set up (see below)
2. Create a regionironment specific .yml file e.g. example_vars.yml (you can create as many regionironments as you want/need, see below for more detail)
3. Build the AWS infrastructure using build playbook, passing in name of regionironment:

  ```
  ansible-playbook -i inventory/ec2.py build.yml -e "region=example"
  ```
4. Deploy the app to the infrastructure using deploy playbook:

  ```
  ansible-playbook -i inventory/ec2.py deploy.yml -e "region=us-east" --private-key keys/example_key.pem
  ```
  Note you may need to wait 1 min for the inventory cache to clear or you can run ```python inventory/ec2.py --refresh-cache``` first
5. Use the AWS console to get the DNS name of the load balancer and open it your browser


Initial set up
==============
1. Download source to a folder
2. Run setup script to ensure all required libraries installed:

  ```
  ./setup.sh
  ```
3. Create IAM user using AWS console. Give them "Power User" role and download AWS access key and access secret.
4. Create region.sh containing access key, access secret and your region:

  ```
  export AWS_ACCESS_KEY_ID=Axxxxxxxxxxxxxxxxxxxxxxxxxx
  export AWS_SECRET_ACCESS_KEY=0xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  export AWS_REGION=ap-southeast-2
  ```    
5. Run region.sh to set regionironment variables (NOTE: YOU WILL NEED TO DO THIS ONCE EVERY SESSION):

  ```
  source region.sh
  ```    
6. Test connectivity + set up (thsi can take a moment and should return some JSON if everything worked)

  ```
  python inventory/ec2.py
  ```

regionironment .yml file
=====================
The regionironment/region .yml file (region/xxxx_vars.yml) holds the specific varibles for the regionironment you want to build:

```
---
control_ip: 175.45.102.163/32

region: ap-southeast-2
zone_1: ap-southeast-2a
zone_2: ap-southeast-2b


server:
  instance_type: t2.micro
  zone_1_count: 1
  zone_2_count: 1

```

* control-ip - The IP address(es) that are allowed to SSH to the servers. You must set this to suit where you are running ansible from
* region - The region you wish to deploy to
* zone_1 - The first availability zone to deploy to
* zone_2 - The second availability zone to deploy to
* server.instance_type - The size of the servers to deploy
* zone_1_count - The number of servers to deploy to zone 1. If you edit this number and rerun build.yml, ansible will increase or decrease the number of servers in this zone. Can be set to 0.
* zone_2_count - The number of servers to deploy to zone 2. If you edit this number and rerun build.yml, ansible will increase or decrease the number of servers in this zone. Can be set to 0.


TODOs
=====
* Improve the handlers in deploy playbook so things are not restarted AFTER instance is added back into load balancer
* Figure out how to give each server a unique sequence number in it's name
* Add tags to ELB (not supported by Ansible yet)


# Setup from Dokku-ansible

This playbook provisions and configures a Dokku server.  

You need ansible 1.3+

First set up these regionironment variables:
```
export AWS_ACCESS_KEY_ID=5678
export AWS_SECRET_ACCESS_KEY=9101112
export ANSIBLE_HOST_KEY_CHECKING=False
And make sure you have your AWS keypair created and key saved.
```

Then run:

```
ansible-playbook install_dokku.yml -v -i ./hosts --private-key=~/.ssh/DokkuSydney.pem
```

