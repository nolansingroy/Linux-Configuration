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
# Managing Users
Give the new user sudo by adding them to the sudoer.d group
As root user run
```
$ nano /etc/sudoers
```
Now add grader to the file
```
# User privilege specification
root    ALL=(ALL:ALL) ALL
grader    ALL=(ALL:ALL) ALL
```

# Installing Dependencies

```
python apt-get install flask python-pip

```
# Configuring your Apache2 served application

