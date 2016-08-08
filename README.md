Example Ansible scripts for building a full AWS stack
=============================================

Basic usage
===========
1. Do initial set up (see below)
2. Create a region specific .yml file e.g. example_vars.yml (you can create as many regions as you want/need, see below for more detail)
3. Build the AWS infrastructure using build playbook, passing in name of region:

  ```
  ansible-playbook -i inventory/ec2.py build.yml -e "region=us-east"
  ```
4. Deploy the app to the infrastructure using deploy playbook:

  ```
  ansible-playbook -i inventory/ec2.py deploy.yml -e "region=us-east" --private-key keys/example_key.pem
  ```
  Note you may need to wait 1 min for the inventory cache to clear or you can run ```python inventory/ec2.py --refresh-cache``` first


Initial set up
==============
1. Download source to a folder
2. Run setup script to ensure all required libraries installed:
  ```
  ./setup.sh
  ```
3. (optional) Create IAM user using AWS console. Give them "Power User" role and download AWS access key and access secret. 
4. (optional) Create region.sh containing access key, access secret and your region: 

  ```
  export AWS_ACCESS_KEY_ID=Axxxxxxxxxxxxxxxxxxxxxxxxxx
  export AWS_SECRET_ACCESS_KEY=0xxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  export AWS_REGION=ap-southeast-2
  ```    
5. (optional) Run region.sh to set regionironment variables (NOTE: YOU WILL NEED TO DO THIS ONCE EVERY SESSION):

  ```
  source region.sh
  ```    
6. Test connectivity + set up (thsi can take a moment and should return some JSON if everything worked)

  ```
  python inventory/ec2.py
  ```

Region .yml file
=====================
The region.yml file (region/xxxx_vars.yml) holds the specific varibles for the region you want to build:

```
---
control_ip: 175.45.102.163/32

region: us-east
zone_1: us-east-1a
zone_2: us-east-1b

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
* Update security group to expose mongo to the right places
* Figure out how to inject mongo multi-host config
* Figure out how to give each server a unique sequence number in it's name (?)
* Ideally, automate the multiregion thing
* Figure out how to attach EFS volumes
* Set mongo to use the EFS volumes
* Ensure the setup is idempotent
* ???
