# Instant Monitoring (Or how to deploy Nagios without worries)
Ansible plays to easily setup Nagios NRPE

## Steps

### Launch the VMs
vagrant up monitor server and nodes

### Create a user with sudo grants on the VMs and set the public key (this is just for easy play)
Since i don't like to write the user password e-v-e-r-y time a playbook is executed, i just create a user on all the VMs that will use my ssh public key (Actually, it will use any key that resides in the path "~/.ssh/id\_rsa.pub") and i give sudo with no password to that user. Less risky options available but not in this repository :)

ansible-playbook plays/set-keys.yml --ask-pass

### Install the Nagios server. 
This machine will be the monitoring box. 
Things installed:
* Nagios core (compiled)
* Nagios plugins (compiled)
* Nagios NRPE plugin
* ...And all the friends that Nagios need to run happy (Apache, php, etc...)

Also, it sets the user and password required to open the Dashboard url. In this case, user/password are: nagiosadmin/nagiosadmin

ansible-playbook plays/install-nagios-server.yml

### Set the Percona repository on the nodes (And install MySQL)
We need something to be subject of monitoring! In this step, the Percona repository is installed and also the Percona Server will be set up

ansible-playbook plays/basic-mysql.yml

### Install Nagios NRPE on the nodes.
All the monitored nodes needs to run the NRPE server and the Nagios plugins. It's just a couple of packages available in the repo.
Additionaly, the Percona Nagios Plugins are installed. This guys will perform the MySQL monitoring magic

ansible-playbook plays/install-nrpe-nodes.yml

### Configure Nagios to check services on the nodes
This step is the reason for all this trouble. This playbook tells the Nagios server the hosts that needs to check and the services on thoses hosts to monitor.

ansible-playbook plays/configure-services.yml
