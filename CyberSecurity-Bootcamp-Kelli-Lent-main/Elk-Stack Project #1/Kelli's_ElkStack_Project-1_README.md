ElkStack Project June 2021

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.  

https://drive.google.com/file/d/10XqYug42SbKlKO5WhlGBh2PMQpnAiOgv/view?usp=sharing

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the configuration file may be used to install only certain pieces of it, such as Filebeat.

     ---
       - name: Config Elk VM w/docker
         hosts: ELK
         remote_user: azuser
         become: true
         tasks:
            # Use apt module installs
            - name: Install Packeges, Docker, Python3
               apt:
               force_apt_get: yes
               update_cache: yes
               name: docker.io
               state: present

            - name: Install Python3-pip
               apt:
               force_apt_get: yes
               name: python3-pip
               state: present

           # Use pip module (it will default to pip3)
           - name: install docker module
              pip:
              name: docker
              state: present

          # Use systcl module
          - name: Use more memory always on
             sysctl:
             name: vm.max_map_count
             value: 262144
             state: present
             reload: yes

         # Use docker_container module  
         - name: download and launch a docker elk container
            docker_container:
            name: elk
            image: sebp/elk:761
            state: started
            restart_policy: always
         # Please list the ports that ELK runs on
           published_ports:
             - 5601:5601
             - 9200:9200
             - 5044:5044

         # Use systemd module
         - name: Enable service docker on boot
            systemd:
            name: docker
            enabled: yes

This document contains the following details:
 - Description of the Topology
 - Access Policies
 - ELK Configuration
   - Beats in Use
   - Machines Being Monitored
 - How to Use the Ansible Build

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly stable and secure, in addition to restricting access to the network.
 - Load balancers help mitigate DDos attacks and "balancing traffic across the network(s).  
 - A jumpbox allows the user to access the network securely using SSH and a private/public key to enhance a secure connection.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system machines.
 - Filebeat the network and VMs data flow in/out of the network.
 - Metricbeat records machine metrics, such as, uptime, CPU usage.  Essentially how "healthy" a system is.

The configuration details of each machine may be found below.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.7   | Linux            |
| Web-1    | VM       | 10.0.0.8   |                  |
| Web-2    | VM       | 10.0.0.9   |                  |
| Web-3    | VM       | 10.0.0.4   |                  |

### Access Policies

The machines on the internal network are not exposed to the public Internet. Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
 - 20.94.204.242
 - 20.38.7.245
 - 98.207.119.233

Machines within the network can only be accessed by ssh to the jump box or elk stack.
 - The load balancer directly and the Web servers via a peering connection. The IP is 10.1.0.4.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes, SSH via Public | 10.1.0.4, 10.0.0.8,  |
|          | key                 | 10.0.0.9, 10.0.0.4,  |
|          |                     | 20.94.204.242,       |
|          |                     | 98.207.119.233       |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
the IT department can push out a configuration to the entire network without having to update each machine, one at a time.

The playbook implements the following tasks:
 - The install Docker by downloading the image file
 - Install Python3 using pip
 - Increase memory allotment
 - Download and launch the docker container
 - Set the container to continue service on boot 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.  

https://drive.google.com/file/d/1yMcZVg7ZmRGmNFVTwZ32F7n2ct9ftaXj/view?usp=sharing

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
 - 10.0.0.4, 10.0.0.8, 10.0.0.9

We have installed the following Beats on these machines:
 - filebeats
 - metricbeats

These Beats allow us to collect the following information from each machine:
 - The machines "health" by monitoring the VMs metrics as in traffic and CPU usage; as well as, collecting log data about the file system by monitoring traffic to the network and VMs and where from and how heavy the traffic is on each machine.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
 - Copy the elk stack playbook file to the /etc/ansible directory.
 - Update the host file to include the IP addresses of the VMs you are connecting to 
 - Run the playbook, and navigate to Kibana to check that the installation worked as expected.
 - The playbook is: install-elk-playbook.yml  
 - The playbook is located in the /etc/ansible directory
 - Update the Hosts.yml to make Ansible run the playbook on a specific machine. 
 - Install the ELK server as the portal for Kibana to access the VMs; install Filebeat on the VMs to monitor.
 - Navigate to this address to ensure the ELK Stack is up and running http://[your.VM.IP]:5601/app/kibana# CyberSecurity-Bootcamp-Kelli-Lent
