Step 1: Install Ansible on the Control Node (pre-requisite)
Install Ansible using your operating system's package manager. 
for wsl/Ubuntu/Debian:
bash
sudo apt update
sudo apt install ansible -y

for amazon-linux:
bash
sudo yum update
sudo yum install ansible -y

Step 2: Copy your downloaded private pem key to your user home directory
nano your-keypair-name.pem
then copy and paste keypair, save and exit

step 3: create inventory file to store host IPs
nano inventory

[webservers]
3.50.120.4  #put your host IPs
10.0.1.15

[webservers:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=demoKey.pem

save and exit

run the ansible adhoc command below
ansible webservers -i inventory -m ping

Expected Output
10.0.1.14 | SUCCESS => {"ping": "pong"}
10.0.1.15 | SUCCESS => {"ping": "pong"}

Step 5: Configuration management with Ansible (main function)
Let’s update all servers, install ngnix and our website.
A Playbook is a YAML file that lists multiple tasks to be executed in order on all managed nodes.
to do this create playbook with,

step 6: nano playbook.yaml or nano main.yml
copy and paste

---
- name: Configure Web Servers
  hosts: webservers
  become: true

  tasks:
    - name: Install Apache (httpd)
      yum:
        name: httpd
        state: present

    - name: Start and enable Apache
      service:
        name: httpd
        state: started
        enabled: true

    - name: Create dynamic index.html
      copy:
        dest: /var/www/html/index.html
        content: |
            <h1>Hello from {{ inventory_hostname }}</h1>
            <p>Server configured by Ansible</p>


Step 7: run the command
ansible-playbook -i inventory playbook.yml

Step 8: Test configuration, Open in browser:
http://SERVER-IP


This is a cleaned-up, professional version of your instructions, formatted with standard GitHub Markdown. It uses clear headings, code blocks, and a logical flow for a README.md.
------------------------------
Ansible Quickstart Guide: Web Server Automation
This project demonstrates how to set up Ansible on a Control Node to manage and configure remote EC2 instances (Managed Nodes) using an automated playbook.
------------------------------
Prerequisites: Install Ansible
Install Ansible on your Control Node based on your Operating System.
For WSL / Ubuntu / Debian:

sudo apt update
sudo apt install ansible -y

For Amazon Linux:

sudo yum update
sudo yum install ansible -y

------------------------------
Step 1: Set Up Your SSH Private Key
Ansible needs your AWS private key to connect to the instances.

   1. Create the key file in your home directory:
   
   nano ~/your-keypair-name.pem
   
   2. Paste your private key content, save, and exit.
   3. Important: Set the correct permissions so SSH allows the key to be used:
   
   chmod 400 ~/your-keypair-name.pem
   
   
------------------------------
Step 2: Define Your Inventory
Create an inventory file to map your server IPs and connection variables.

nano inventory

Paste the following (replace IPs with your own):

[webservers]
3.50.120.4
10.0.1.15

[webservers:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/your-keypair-name.pem

------------------------------
Step 3: Verify Connection (Ad-Hoc Command)
Test the connection to all hosts in the webservers group using the ping module.

ansible webservers -i inventory -m ping

Expected Output:

3.50.120.4 | SUCCESS => {"ping": "pong"}10.0.1.15 | SUCCESS => {"ping": "pong"}

------------------------------
Step 4: Create the Configuration Playbook
A Playbook is a YAML file that defines the desired state of your servers. This playbook installs Apache and creates a custom landing page.

   1. Create the file:
   
   nano playbook.yml
   
   2. Paste the following configuration:

---
- name: Configure Web Servers
  hosts: webservers
  become: true  # Runs tasks with sudo privileges

  tasks:
    - name: Install Apache (httpd)
      yum:
        name: httpd
        state: present

    - name: Start and enable Apache
      service:
        name: httpd
        state: started
        enabled: true

    - name: Create dynamic index.html
      copy:
        dest: /var/www/html/index.html
        content: |
            <h1>Hello from {{ inventory_hostname }}</h1>
            <p>Server configured by Ansible</p>

------------------------------
Step 5: Run the Playbook
Execute the automation against your inventory.

ansible-playbook -i inventory playbook.yml

------------------------------
Step 6: Verify the Setup
Open your web browser and navigate to the public IP address of your servers:
http://<YOUR_INSTANCE_IP>
You should see a heading saying: "Hello from [Your Server IP]".
------------------------------

