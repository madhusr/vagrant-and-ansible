
# Getting started with Vagrant and Ansible

The whole project is divided into several chapters. The first chapter is all about creating a virtual machine
using Vagrant and connecting to it through SSH and Ansible.

### Activities / Tasks  :

	- Provision a virtual machine using Vagrant
	- Verify virtual machine is up and running
	- Identify IP address of the provisioned virtual machine
	- Identify key for passwordless login to the virtual machine
	- Connect to virtual machine using SSH and Ansible

### Provision Virtual Machine using Vagrant

Clone repository and navigate to chapter_1 and do the initialization.
This would create a file named Vagrantfile which is the master file for
all vagrant operations.

		vagrant init

Provision the VM using vagrant up command.

		vagrant up

You should see an output as below, the ip details could vary in your system.
Even though the required static IP address of the VM has been specified in the Vagrantfile, it is totally ignored by Hyper-V which is a defect in Hyper-V. So be mindful of looking for the IP address once the VM is provisioned.

	Bringing machine 'app1' up with 'hyperv' provider...
	==> app1: Verifying Hyper-V is enabled...
	==> app1: Verifying Hyper-V is accessible...
	==> app1: Importing a Hyper-V instance
		app1: Creating and registering the VM...
		app1: Successfully imported VM
		app1: Please choose a switch to attach to your Hyper-V instance.
		app1: If none of these are appropriate, please open the Hyper-V manager
		app1: to create a new virtual switch.
		app1:
		app1: 1) UbuntuSwitch
		app1: 2) New Virtual Switch
		app1: 3) Default Switch
		app1:
		app1: What switch would you like to use? 3
		app1: Configuring the VM...
	==> app1: Starting the machine...
	==> app1: Waiting for the machine to report its IP address...
		app1: Timeout: 120 seconds
		app1: IP: 192.168.192.76
	==> app1: Waiting for machine to boot. This may take a few minutes...
		app1: SSH address: 192.168.192.76:22
		app1: SSH username: vagrant
		app1: SSH auth method: private key
		app1:
		app1: Vagrant insecure key detected. Vagrant will automatically replace
		app1: this with a newly generated keypair for better security.
		app1:
		app1: Inserting generated public key within guest...
		app1: Removing insecure key from the guest if it's present...
		app1: Key inserted! Disconnecting and reconnecting using new SSH key...
	==> app1: Machine booted and ready!
	==> app1: Setting hostname...

### Verify virtual machine is up and running

To verify if the virtual machine is provisioned and is running, run the following command.
It should see app1 with status as running.

		vagrant status

### Identify IP address of the provisioned Virtual Machine

The IP address of each of the virtual machine is logged during the provisioning of the VM and in my case it is

		app1: IP: 192.168.192.76

### Identify key for Passwordless login to Virtual Machine

Now you have created a VM to which you can connect to using SSH. If you see the above output, vagrant creates an SSH key for password-less login.
During the VM creation vagrant finds the in-secure key, removes it from the VM, creates a new key-pair and logs-in using new SSH key. Now, how do find the SSH public key used
by Vagrant to login. Well, run the command vagrant ssh-config and you should see the below output which provides the location of the public key.

		Host app1
		HostName 192.168.192.76
		User vagrant
		Port 22
		UserKnownHostsFile /dev/null
		StrictHostKeyChecking no
		PasswordAuthentication no
		IdentityFile C:/plab/learning-sessions/onAnsible/chapter_1/.vagrant/machines/app1/hyperv/private_key
		IdentitiesOnly yes
		LogLevel FATAL

### Connect to virtual machine using SSH

Login to VM using SSH and it should not prompt for the password. If you still want to use password then the default password would be vagrant.

		ssh -i private_key vagrant@192.168.192.76

### Connect to virtual machine using Ansible

Let's use Ansible to connect to the VM we created and you can use the below command

	ansible dev_servers -m ping -u vagrant -k

Let's break downn the above command and understand each argument we supplied

	- dev_servers is the server group to perform activities on.
	- ping is ansible module used to ping the servers defined under dev_servers group.
	- vagrant is the ssh user used to connect to the server.
	- (-k) is the flag to prompt for password. Ideally this is discouraged in favour of keys.

Above command is not having the inventory or the file having the list of hosts defined for ansible to work with. There is an entry in ansible.cfg file which specified the inventory file.

				inventory = hosts          

If the inventory file is not defined in ansible.cfg file then the command would be

				ansible -i hosts dev_servers -m ping -u afmgr -k

Congratualtions !!! you have provisioned a Virtual Machine using Vagrant and connected to it using Ansible.

In this chapter we learn how to use Vagrant to provision a simple VM and use Ansible command module to ping the server. Ansible has lot of other modules and Adhoc commands which will be explored in the later chapters.
