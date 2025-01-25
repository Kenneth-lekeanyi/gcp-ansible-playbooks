# This adhoch command will install apache httpd in CentOs
ansible webservers -b yum -a "name=httpd state=present"

# This adhoc command will start the service called httpd
ansible webservers -b -m service -a "name=httpd state=started"

# Now, start the httpd apache. so do
ansible webservers -b -m service -a "name=httpd state=started"

# To stop the httpd apache. so do
ansible webservers -b -m service -a "name=httpd state=stopped"

# Using the "Copy module" to copy something into those clients
ansible atlanta -m ansible.builtin.copy -a "src=/etc/hosts dest=/tmp/hosts"
# now edit it to appear as follows before you run it in the Master
ansible webserver -m copy -a "src=/etc/hosts dest=/tmp/hosts"
ansible all -m copy -a "src=/home/ansible/application.py dest=/home/ansible/"

# 