# Virtual-Cluster
Virtual Machine Setup for Docker, Ansible, and Nagios Monitoring

Create new virtual machines based on the following setup:

 

Virtual Machine Specifications:

Using Oracle VM VirtualBox

VM Names: webserver03, webserver02, webserver01, and MainServer

Memory Size: 2048MB (for MainServer, more memory is recommended)

Disk Size: 20GB with thin provisioning

 

Network Setting:

Network Adapter 1: Host-Only (for internal communication)

Network Adapter 2: NAT (for external access)

IP address: Assign static IP addresses in the Host-Only network range.

Network Mask: 255.255.255.0

Gateway: IP of the NAT adapter (usually assigned by the DHCP of the NAT network)

DNS: Use a public DNS like 8.8.8.8

Operating System:

OS: Ubuntu 22.04 LTS

Installation Type: Minimal install

 

Network Configuration and Check:

ip a to check IP addresses.

ping -c 4 google.com to test internet connectivity.

 

System Update:

sudo apt-get update && sudo apt-get upgrade -y

Disable UFW Firewall (optional):

sudo ufw disable

Ansible Setup on MainServer:

Install Ansible:

sudo apt-get install ansible -y

Setup SSH Keys:

ssh-keygen

ssh-copy-id user@vm_ip_address for each VM.

Configure Ansible Inventory:

Edit /etc/ansible/hosts and add VMs under [webservers] group.

Docker Setup on VMs:

Install Docker:

sudo apt-get install docker.io -y

Run Hello-World Container (to test Docker):

sudo docker run hello-world

Nagios Setup on MainServer:

Install Nagios:

Follow Nagios installation steps for Ubuntu.

Configure Nagios for VM Monitoring:

Edit Nagios configuration files to add host entries for each VM.

On the mainserver:

Ansible Playbooks Setup and Execution

1. Update Packages Playbook (update_web_servers.yml)

Purpose: To update all packages on web servers to the latest version.

Content:

- name: Update all packages to the latest version on webservers

  hosts: webserver01, webserver02, webserver03

  become: true

 

  tasks:

    - name: Update apt cache

      ansible.builtin.apt:

        update_cache: yes

        cache_valid_time: 3600 # Cache valid time in seconds

 

    - name: Upgrade all apt packages

      ansible.builtin.apt:

        upgrade: dist

 

 

Execution Command: ansible-playbook update_web_servers.yml

 

Deploy Nginx using Docker Playbook (deploy_nginx.yml)

Purpose: To deploy the Nginx web server in Docker containers on VMs.

Content:

- name: Deploy Nginx web server using Docker

  hosts: webserver01, webserver02, webserver03

  become: yes

 

  tasks:

    - name: Pull Nginx Docker image

      docker_image:

        name: nginx

        state: present

 

    - name: Create Nginx container

      docker_container:

        name: nginx

        image: nginx

        state: started

        restart_policy: always

        ports:

          - "80:80"

 

 

Deploying Services with Ansible:

Nginx Deployment Playbook (deploy_nginx.yml):

Ansible playbook to deploy Nginx in Docker containers.

Execute with ansible-playbook deploy_nginx.yml.

Package Update Automation:

Playbook for Updating Packages (update_web_servers.yml):

Write a playbook to update all software packages.

Execute with ansible-playbook update_web_servers.yml.

 

Accessing Nginx and Nagios Web Pages

For Nginx:

Ensure Nginx is Running: Before trying to access the web page, make sure the Nginx containers are running on your VMs. You can verify this by executing the command sudo docker ps on each VM. Look for containers running the Nginx image.

 

Access Nginx Web Page:

 

Open a web browser.

Enter the URL in the format: http://<VM_IP_ADDRESS>

Replace <VM_IP_ADDRESS> with the actual IP address of each VM where the Nginx container is running.

For example, if your VM IP address is 192.168.56.101, the URL will be http://192.168.56.101.

For Nagios:

Ensure Nagios is Running: Verify that the Nagios service is up and running on your main server. You can check this by running sudo systemctl status nagios on the main server.

Access Nagios Web Page:

Open a web browser.

Enter the URL in the format: http://<MAIN_SERVER_IP_ADDRESS>/nagios

Replace <MAIN_SERVER_IP_ADDRESS> with the IP address of your main server where Nagios is installed.

For example, if your main server's IP address is 192.168.56.100, the URL will be http://192.168.56.100/nagios.

Log in using the credentials you set up for Nagios. If you haven’t changed the default, it’s usually nagiosadmin for the username.



From here you have created all virtual machines and can push playbooks to update each one and also run the nginx webservers. Also, you can use nagios to monitor the health of your virtual machines as well. 

