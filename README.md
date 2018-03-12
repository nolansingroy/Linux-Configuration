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

# Disabling Port 22
First make sure you have port 2200  under custom tab within the Amazon Light Sail Developer Console
else you will get locked out. 

# Security 


# Adding a User 
```
sudo useradd grader
```
# Managing Users
Give the new user sudo by adding them to the sudoer.d group
As root user run
```
```
Now add this line to the file 
```
```
# Installing Dependencies
```
python apt-get install flask python-pip

```
# Configuring your Apache2 served application

