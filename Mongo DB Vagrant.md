# How to connect To your mongo database inside a Vagrant Virtual Machine from The Windows Host
## 1. Preparation
First of all, you need to navigate to your vagrant vbox folder and activate your vagrant virtual machine by running **vagrant up**. 
Use **vagrant ssh** to access to the shell of the VM.
In our case, i navigate to the folder where my .**vbox** file is located.

    cd Path-to-your-vbox-file/
    vagrant up
## 2. Enabling Port 27017 of the VM
You have to enable the accessbility of the 27017 (which will be used by mongodb) from extern applications and software.
When connected to the virtual machine, run **sudo su** to log as root first.
After that, run **iptables -A INPUT -p tcp --dport 27017 -j ACCEPT to accept** connection to the port 27017 of the VM.

    $ sudo su
    # iptables -A INPUT -p tcp --dport 27017 -j ACCEPT to accept
You can verify the state of the port by running **iptables -L** and seeing it in the result 

    # iptables -L
![result of iptables -L](https://i.ibb.co/XscgNgR/Capture-d-cran-2024-05-05-205015.png)
## 3. Configuring Mongo to accept connections from all IPS
- Login as root 
- Edit the file located in /etc/mongod.conf using nano (or any other of your editor)
and search for the line containing **bindIp :** and change *127.0.0.1* to *0.0.0.0*

`nano /etc/mongod.conf`
![enter image description here](https://i.ibb.co/jb6Lxz0/Capture-d-cran-2024-05-05-212157.png)
## 4. Create a custom alias config for your Vagrant VM Profile
In Windows, go to the folder **Your-username/.ssh** and search for *config* file, otherwise, create the config file (without extension)
Add those lines in it to create the alias **oracle-21c-vagrant**

    Host oracle-21c-vagrant
	    HostName 127.0.0.1
	    User vagrant
	    Port 2222
	    UserKnownHostsFile /dev/null
	    StrictHostKeyChecking no
	    PasswordAuthentication no
		IdentityFile Your-username/.vagrant.d/boxes/package.box/0/virtualbox/vagrant_private_key
		IdentitiesOnly yes
		LogLevel FATAL
		PubkeyAcceptedKeyTypes +ssh-rsa
		HostKeyAlgorithms +ssh-rsa

## 5. Use Portforwarding to access mongo DB from external
Use this command to forward the port **27018** (do not choose 27017 because it maybe already used by the mongodb in the host machine) to 27017 of the Vagrant Virtual Machine (the mongo database itself) and using the alias **oracle-21c-vagrant**, created previously

    ssh -L 27018:127.0.0.1:27017 oracle-21c-vagrant

## 6. Testing and Enjoying
Now you can access to the mongo DB inside the Vagrant VM using the port **27018** of the host machine (Windows in our case) via mongoshell or via mongoDB Compass
**Notes** : mongodb inside the vagrant VM is in **version 3** so you have to downgrade your mongoshell and/or your mongoDBCompass to use it. I downloaded Mongo Compass version *1.28.4* to access that *Mongo 3*

You can repeat the same technique if you want to access other services inside the VM from your Windows Host Machine.




















