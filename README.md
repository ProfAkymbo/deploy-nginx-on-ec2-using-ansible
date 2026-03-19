## Step 1: Install Ansible on the Control Node (pre-requisite)
Install Ansible using your operating system's package manager. 
for wsl/Ubuntu/Debian:
bash
sudo apt update
sudo apt install ansible -y

for amazon-linux:
bash
sudo yum update
sudo yum install ansible -y

## Step 2: Copy your downloaded private pem key to your user home directory
nano your-keypair-name.pem
then copy and paste keypair, save and exit

## step 3: create inventory file to store host IPs
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

## Step 5: Configuration management with Ansible (main function)
Let’s update all servers, install ngnix and our website.
A Playbook is a YAML file that lists multiple tasks to be executed in order on all managed nodes.
to do this create playbook with,

## step 6: nano playbook.yaml or nano main.yml
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

## Step 7: run the command
ansible-playbook -i inventory playbook.yml

## Step 8: Test configuration, Open in browser:
http://SERVER-IP


