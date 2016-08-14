This playbook provisions and configures a Dokku server.

You need ansible 1.3+

First set up these environment variables:

export AWS_ACCESS_KEY_ID=5678
export AWS_SECRET_ACCESS_KEY=9101112
export ANSIBLE_HOST_KEY_CHECKING=False
And make sure you have your AWS keypair created and key saved.
Then run:

ansible-playbook install_dokku.yml -v -i ./hosts --private-key=~/.ssh/DokkuSydney.pem

TODOs
=====
* Figure out how to attach EFS volumes
* Set mongo to use the EFS volumes
* Ensure mongo is using the right options
* Ensure the setup is idempotent
* (set up newrelic mongo?)
* ???
