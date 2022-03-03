# eden-repository
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![elk diagram1](https://user-images.githubusercontent.com/29513107/156135548-50df46bb-02e0-47d6-86a6-1811f76f8b82.jpg)


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the file may be used to install only certain pieces of it, such as Filebeat.



  - anisble-playbook /etc/anisble/pentest.yml
  
1)name: Config Web VM with Docker hosts: webservers become: true tasks:

2)name: Install docker.io apt: force_apt_get: yes update_cache: yes name: docker.io state: present

3)name: Install pip3 apt: force_apt_get: yes name: python3-pip state: present

4)name: Install Python Docker module pip: name: docker state: present

5)name: download and launch a docker web container docker_container: name: dvwa image: cyberxsecurity/dvwa state: started restart_policy: always published_ports: 80:80

6)name: Enable docker service systemd: name: docker enabled: yes


   
   - anisbleplaybook elk-install.yml

1)name: Configure Elk VM with Docker

2)name: Install docker.io apt: update_cache: yes name: docker.io state: present

3)name: Install pip3 apt: force_apt_get: yes name: python3-pip state: present

4)name: Install Docker python module pip: name: docker state: present

5)name: Use more memory sysctl: name: vm.max_map_count value: "262144" state: present reload: yes

6)name: download and launch a docker elk container docker_container: name: elk image: sebp/elk:761 state: started restart_policy: always published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

7)name: Enable service docker on boot systemd: name: docker enabled: yes



  - filebeat-playbook.yml

1)name: Installing and Launch Filebeat hosts: webservers become: yes tasks:

2)name: Download filebeat .deb file command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb

3)name: Install filebeat .deb command: dpkg -i filebeat-7.4.0-amd64.deb

4)name: Drop in filebeat.yml copy: src: /etc/ansible/files/filebeat-config.yml dest: /etc/filebeat/filebeat.yml

5)name: Enable and Configure System Module command: filebeat modules enable system

6)name: Setup filebeat command: filebeat setup

7)name: Start filebeat service command: service filebeat start

8)name: Enable service filebeat on boot systemd: name: filebeat enabled: yes



  - metric-playbook.yml

1)name: Install metric beat hosts: webservers become: true tasks:

2)name: Download metricbeat command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb

3)name: install metricbeat command: dpkg -i metricbeat-7.4.0-amd64.deb

4)name: drop in metricbeat config copy: src: /etc/ansible/files/metricbeat-config.yml dest: /etc/metricbeat/metricbeat.yml

5)name: enable and configure docker module for metric beat command: metricbeat modules enable docker

6)name: setup metric beat command: metricbeat setup

7)name: start metric beat command: service metricbeat start

8)name: Enable service metricbeat on boot systemd: name: metricbeat


This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build



### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly effective, in addition to restricting access to the network.

- What aspect of security do load balancers protect? What is the advantage of a jump box?

They prevent unwanted traffic from  reaching the application. The advantage of a jump box is it allows access to admins before making a connection to an untrusted server or connection.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the configuration and system files.

- What does Filebeat watch for?

To log files and or log any events.

- What does Metricbeat record? 

Records statistics from the server


| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| JumpBox  | Gateway    | 10.0.0.6 | Linux            |
| Web 1    | Webserver  | 10.0.0.7 | Linux            |
| Web 2    | Webserv    | 10.0.0.8 | Linux            |
| Elk-Net  | Monitoring | 10.1.0.4 | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

24.15.152.69

Machines within the network can only be accessed by Jumpbox.

- Which machine did you allow to access your ELK VM?

My own computer

- What was its IP address? 

24.15.152.69

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 24.15.152.69         |
| Web-1    | No                  | 24.15.152.69         |
| Web-2    | No                  | 24.15.152.69         |
| Elk-net  | Yes                 | 24.15.152.69         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because process become repeatable.

- What is the main advantage of automating configuration with Ansible?
  
It lets all the virtual machines be modified with ease.


The playbook implements the following tasks:

1-Installed Docker.io 

2-Install python3-pip 

3-Install Docker Python Module 

4-Install and launch Docker elk container

5-Enable service docker


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![ELK-server](https://user-images.githubusercontent.com/29513107/156128040-5777d8e2-8d9f-4a4f-b446-8d684ea0f493.PNG)


### Target Machines & Beats
This ELK server is configured to monitor the following machines:

- List the IP addresses of the machines you are monitoring. 

10.1.0.4 , 10.0.0.6 , 10.0.0.7 , 10.0.0.8 


We have installed the following Beats on these machines:
- Specify which Beats you successfully installed. 

Filebeat and metricbeat.


These Beats allow us to collect the following information from each machine:

Metricbeat lets you see the integrity of yout files in your server through stats and metrics. For example:Your cpu usage. Filebeat lets you log events which you can see on elk for a visual representation. An example may be the files made by MySQl databases.


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the ansible configuration file to run playbooks.
- Update the ansible host file to include the following.

[webservers]

10.0.0.7 ansible_python_interpreter=/usr/bin/python3

10.0.0.8 ansible_python_interpreter=/usr/bin/python3


[elk]

10.1.0.4 ansible_python_interpreter=/usr/bin/python3

- Run the playbook, and navigate to Jumpbox to check that the installation worked as expected.

- Which file is the playbook? Where do you copy it?

anisble-playbook elk-install.yml

- Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?

filebeat-playbook.yml to /etc/ansible

- Which URL do you navigate to in order to check that the ELK server is running?

http://24.15.152.69:5601/app/kibana
