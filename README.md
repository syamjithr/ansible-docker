# ansible-docker
## This simple ansible playbook used to install docker on ubuntu and creates two test container.
vim docker-ubuntu.yaml
```
---
- name: Installing docker on ububtu
  hosts: container
  become: true
  vars:
    containers: 2
    container_name: docker_ubuntu
    container_image: ubuntu:18.04
    container_command: test

  tasks:
   - name: Install aptitude using apt
     apt: name=aptitude state=latest update_cache=yes force_apt_get=yes

   - name: Install required system packages
     apt: name={{ item }} state=latest update_cache=yes
     loop: [ 'apt-transport-https', 'ca-certificates', 'curl', 'software-properties-common', 'python3-pip', 'virtualenv', 'python3-setuptools']

   - name: Add Docker GPG apt Key
     apt_key:
       url: https://download.docker.com/linux/ubuntu/gpg
       state: present

   - name: Add Docker Repository
     apt_repository:
       repo: deb https://download.docker.com/linux/ubuntu bionic stable
       state: present

   - name: Update apt and install docker-ce
     apt: update_cache=yes name=docker-ce state=latest

   - name: Install Docker Module for Python
     pip:
       name: docker

   - name: Pull default Docker image
     docker_image:
       name: "{{ container_image }}"
       source: pull

   - name: Create default containers
     docker_container:
       name: "{{ container_name }}{{ item }}"
       image: "{{ container_image }}"
       command: "{{ container_command }}"
       state: present
     with_sequence: count={{ containers }}
```
##### execution 
```
ansible-playbook -i inventory.txt docker-ubuntu.yaml 
```
