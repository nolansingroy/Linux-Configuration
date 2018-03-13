# Linux Configuration

# Description

The main purpose of this project is to configure virtual machine to host 
your web sites or applications, set up security measures on the system, and manage users.
Here we used Amazon Light Sail due to it being one of the cheapest virtual machine avaiable. 

# IP  Address 
```
http://ec2-54-212-242-59.us-west-2.compute.amazonaws.com/
```
# SSH Port 
```
Port 2200
```
# System Update
```
$ sudo apt- update
$ sudp apt-upgrade
```

# Disabling Port 22
First make sure you have port 2200  under custom tab within the Amazon Light Sail Developer Console
else you will get locked out. 
 Now Change the default port 22 to 2200 in file 
```
$ sudo nano /ect/ssh/sshd_config
```
The rest of the commands may now be used from a terminal loggin session

# Adding a User 
```
sudo useradd grader
```
now since we are using Amazon light sail we may download a new
ssh key for our user 'grader' and give the file stricted permissions
```
chmod 400 grader-keypair.pem
```
To retrieve the key 
```
ssh-keygen -y
```
Enter the location of the file and press Enter. The Terminal should now display
the key inwich you should copy to be placed in our authorized_keys file on the 
amazon instance running as user grader.

```
mkdir .ssh
chmod 700 .ssh
cd .ssh
touch authorized_keys
chmod 600 authorized_keys
nano authorized_keys 
```
Paste the key contents into the nano editor of authourized_keys and save!
Now you should be able to login using the ssh keys to the user grader.
```
sudo ssh -i grader.pem grader@54.212.242.59 -p 2200
```

# Managing Users
Give the new user sudo by adding them to the sudoer.d group
As root user run
```
$ nano /etc/sudoers
```
Now add grader to the file, here you should also see another user that 
the amazon light sail created for you as the time of setting up the instace
mines was called 'ubuntu'

```
# User privilege specification
root    ALL=(ALL:ALL) ALL
grader    ALL=(ALL:ALL) ALL
```

# Disabling Root Login
From the file /etc/ssh/sshd_config

Change line ``` PermitRootLogin without-password ```
to ``` PermitRootLogin no```
We should also uncomment and set this line from yes to no
```
PasswordAuthentication no
```
Run ``` service ssh restart```
for the effects to take place

# Configuration of the Uncomplicated Firewall (UFW)

If you were to run a 

# Installing Dependencies

```
python apt-get install flask python-pip

```
# Configuring your Apache2 served application


# Helpful Documentations

1. https://aws.amazon.com/premiumsupport/knowledge-center/new-user-accounts-linux-instance/
2. 
3. https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
