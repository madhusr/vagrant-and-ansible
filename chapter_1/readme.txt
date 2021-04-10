
Getting started with Vagrant and Ansible. 
Simple example on how create a local VM instance and connect to it using plain SSH and using Ansible.

Provision the VM using vagrant up command. You should see an output as below, the ip details could vary in your system as I am using Hyper-V.
Even though I have specified a static IP address in the Vagrantfile, Hyper-V does not support it as of this writing. Hence it creates a completely different IP.

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

Now you have created a VM to which you can connect to using SSH. If you see the above output, vagrant creates an SSH key for passwordless login. 
During the VM creation it finds the in-secure key, removes it from the VM, creates a new key-pair and logs-in using new SSH key. Now, how do find the SSH public key used 
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

Login to VM using ssh -i private_key vagrant@192.168.192.76 and it should not prompt for the password. If it does then the public key is not the right one.


Let's use Ansible to connect to the VM we created and you can use the below command 

	ansible dev_servers -m ping -u vagrant -k

	Let's break downn the above command and understand each argument we supplied

	dev_servers is the group which we would like to ping to check if each server in this group is reachable or not.
	ping is ansible module used to connect with the servers and get a response back.
	vagrant is the user to be used for the ssh session to connect with the servers
	-k is the flag to prompt for password. Ideally this is discouraged in favour of keys.

	Note : Above command is not having the inventory or the file having the list of hosts defined for ansible to work with.
		   There is an entry in ansible.cfg file which specified the inventory file.
		   
				inventory = hosts          
	   
		If the inventory file is not defined in ansible.cfg file then the command would be 
		
				ansible -i hosts dev_servers -m ping -u afmgr -k
		
		Notice here we have specified the inventory file. Otherwise ansible would not be able to detect what is dev_servers.

Congratualtions !!! you have run your first ansible command to connect and get a response from the server, just by using ssh and python.   






