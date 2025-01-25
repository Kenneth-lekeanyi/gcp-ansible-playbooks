Fist of all generate your SSH Key in your Local. so that you pass it in metadata to give ssh access to the VMs in the console. so in your local terminal,
# Use this to list and see what is in you .ssh Directory. Do
ls ~/ .ssh

# Now run this command to generate the key. som do
ssh-keygen -t  rsa

# Now, cat the ssh public key to get the content of the key. So do
cat ~/ .ssh/id_rsa.pub

# Now, log into the instance
# Method 1.
chmod 400 ~/.ssh/id_rsa
# Then you edit this command accordingly
ssh -i ~/.ssh/id_rsa USER_NAME@VM_EXTERNAL_IP
ssh -i ~/.ssh/id_rsa lekeanyi@34.124.121.178
# it says "Are you sure you want to continue connecting? 
Type "yes"
You are now logged in

# method 2. 
just copy the External IP address and do this to log in
ssh 34.124.121.178
##########################################

## setting up Ansible friendly environment
Master set up
sudo su

# There is a repository in linux called Epel-release. we need to install it. But it will most likely be there already. so just to confirm, do
sudo yum install epel-release -y

# Once the repository is installed, install Ansible with yum:
sudo yum install ansible -y

# Now, check the version by doing
ansible --version

# Now, create a user called ansible with this command
sudo useradd ansible

# Now, create a password for this user. so we are here using a password based-user. so do
sudo passwd ansible

# Key in the password. so we use a simple password which is
ansible

# Now, we need to edit the sshd_config file to set NO password for authentication. so run this sed command to edit it directly without getting into the text editor of the sshd_config file. so do
sudo sed -i 's/.*PasswordAuthentication no/Passwordauthentication yes/g' /etc/ssh/sshd_config

# Also change the Permit Root Login string and set it to "yes". So run this command to change the Permit Root Login by uncommenting it. so do
sudo sed -i 's/.*#PermitRootLogin yes/PermitRootLogin yes/g' /etc/ssh/sshd_config

# Now, still in both the appserver and the webserver, use echo to add NO PASSWORD in the sudoers file so  that it will not be asking for Authorization. So switch to root first by doing
sudo su 
# Then do 
sudo su echo "ansible  ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers

# Now, exit from root. so do
exit

# To view the content of the file, cat it as follows
cat /etc/ssh/sshd_config



# Now, get into the /etc/ansible file. So do
cd /etc/ansible

# Now, do ls to see the "ansible.config", "hosts" and "roles"
ls

# Now, get into the inventory File (hosts) and update the inventory file by creating groups. so do
sudo vi hosts
# At the very beginning of the file or on line 1, change to insert mode. So do
press i on your keyboard.
Then press "ENTER" 3 TIMES to move down a bit
# go back to line 1 so to create the group. To create this groups. We are going to create two groups: one for appserver and another one for webserver. So do 
[webservers]
10.128.0.18 {this is the internal ip of the webserver}

[appservers]  {this is the internal ip of the appserver}
10.128.0.18
# Nowan chnge to escape mode. so do
esc then save and quite. so do :wq!

# Now in both the webserver-client and in appserver-client, run this command to add the User Ansible in both clients. So run this in both of them
sudo useradd ansible

# Now, password the user in both servers also. so do
sudo passwd ansibleansible

# input this password in both of them
ansible

# # Now, in both the webserver and the appserver, we need to edit the sshd_config file to set NO password for authentication. so run this sed command to edit it directly without getting into the text editor of the sshd_config file. so in both the webserver and the appserver, do
sudo sed -i 's/.*PasswordAuthentication no/Passwordauthentication yes/g' /etc/ssh/sshd_config

# In both the webserver and the appserver, also change the Permit Root Login string and set it to "yes". So run this command to change the Permit Root Login by uncommenting it. so run this command in both webserver and appserver.
sudo sed -i 's/.*#PermitRootLogin yes/PermitRootLogin yes/g' /etc/ssh/sshd_config

# Now, still in both the appserver and the webserver, use echo to add NO PASSWORD in the sudoers file so  that it will not be asking for Authorization. So switch to root first by doing
sudo su 
# Then do 
sudo su echo "ansible  ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers

# Now, Restart SSHD deamon Service. So do
service sshd restart 

# Now, in the Master only, exit untill you get to your local terminal. so just contnue to type "exit" untill you get to your local terminal. So do
exit 
exit 
exit

# Now, still in the Master, login as a User "ansible" in order to generate those keys Files. To ssh into the Master as a user, do 
ssh ansible@34.28.8.38         {use the external ip of the Master}

# Now, in the webserver, exit untill you get to your local terminal. so just contnue to type "exit" untill you get to your local terminal. So do
exit 
exit 
exit

# Now, still in the webserver, login as a User "ansible" in order to generate those keys Files. To ssh into the websever as a user, do 
ssh ansible@34.128.214.46         {use the external ip of the webserver }

# Now, in the appserver, exit untill you get to your local terminal. so just contnue to type "exit" untill you get to your local terminal. So do
exit 
exit 
exit

# Now, still in the appserver, login as a User "ansible" in order to generate those keys Files. To ssh into the websever as a user, do 
ssh ansible@34.122.193.119         {use the external ip of the appserver}

# you can now test, although we have not yet generated the ssh keys. To test, run the ping command
asible -m ping webservers
# Expected to fail because we have not yet generated the ssh keys

# Now, ONLY in the MASTER,while still logged in as user ansible in the master, run this command to generate the ssh key.
ssh-keygen -t rsa

# Now, change the permission of that key just generated in order to give full priviledges to the user. So run this command
chmod 700 /home/ansible/.ssh

# Now, use this command to copy and share the key or ID to the webserver, so as to connect with it. so do
ssh-copy-id ansible@34.123.214.46       {This is a one time thing. so you can use either the private ip or the external ip of the webserver}

# provide the password
ansible  {this is the password of the webserver and not that of the Master}

# # Now, use this command to copy and share the key or ID to the appserver, so as to connect with it. so do
ssh-copy-id ansible@34.122.193.119       {This is a one time thing. so you can use either the private ip or the external ip of the appserver}

# provide the password
ansible   {this is the password of the appserver and not that of the Master}

# You can now test by trying to ping the webservers. so do
ansible -m ping webservers

# You can now test by trying to ping the appservers. so do
ansible -m ping appservers

# To test all, do
ansible -m ping all

# in Ansible, we have [ansible Adhoc Commands] and [Ansible Playbook Commands]. Ansible Playbook Commands starts with 
ansible-playbook.......

# We are going to install httpd in the webserver targeted group using ansible push method. But before that, go to the webserver terminal and check if apache httpd is installed there. So go there and do
sudo systemctl status httpd

# Ansible adhoc commands starts with "ansible" e.g
ansible <group> -b -m yum -a "name=httpd state=present"
# { when you run this command in the Master, it will get executed in the group of target client "webservers" that you will mention in the group}. So it will be
ansible <webservers> -b -m yum -a "name=httpd state=present" 
# {so this ansible command will install apache httpd in the webservers group}

# Now, start the httpd apache. so do
ansible webservers -b -m service -a "name=httpd state=started"

# To stop the httpd apache. so do
ansible webservers -b -m service -a "name=httpd state=stopped"

# This command will install wget in your OS
sudo yum install wget -y

# The command to execute a playbook is 
ansible-playbook <insert playbook name here>
ansible-playbook install-apache.yaml
ansible-playbook install-apache.yaml -v




