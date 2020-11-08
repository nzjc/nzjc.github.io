---
id: 201
title: 'Step by step guide: Preparing a Debian VM for Junos Automation'
date: 2017-11-08T10:53:49+00:00
author: james
excerpt: |
layout: post
guid: https://normally.online/?p=201
permalink: /step-by-step-guide-preparing-a-debian-vm-for-junos-automation/
categories:
  - Uncategorized
---
This is a bit specific, and, like most of my posts - a cheap way for me to remember something next time I need to do it.

I am currently obsessed with network automation. My favourite 'stack' at the moment is Ansible, git and the Juniper Ansible libraries. There are a thousand ways to skin this particular cat, but for my current project (enforcing 'golden config' across a large number of devices) - this limited number of tools does the job.

As with most cool new tech, there are hundreds of posts and docs, most of which are similar enough to give the illusion of cohesion, but all critically different when it comes to the nitty-gritty, causing confusion and angst. At least, that's my impression.

So - if you want a Junos Automation machine, ready to attack your network with Python and Ansible, follow along.

<!--end_excerpt-->

I'm using Debian 8, a fresh install. Splat these commands in to set up the bits you will need. I have tested these and find they work, resolving the dependencies and resulting in no errors.

<pre class="lang:default decode:true">sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install python-pip ansible git software-properties-common
sudo pip install -U pip setuptools
sudo ansible-galaxy install Juniper.junos
sudo pip2 install git+https://github.com/Juniper/py-junos-eznc.git</pre>

You'll end up with Ansible installed in /etc/ansible, the freshest Juniper library for Python (version 2.7) interaction via Ansible. You'll also have git installed, one for installing the Junos EZNC package and for future use.

My end goal here is to use this system to completely automate my network, but for the time being - we're good to start using Ansible to take baby steps towards that goal.

I have created a directory called lab-automation, and in it three sub-directories. One called scripts (for my playbooks and shell scripts), one called logs and the other called configs, for my configs! I have created a basic Ansible playbook, which uses the previously installed Juniper.junos role, and connects to my lab routers (mx1-mx4, as defined in the /etc/ansible/hosts file and my /etc/hosts file).

<pre class="lang:default decode:true " title="Config Getter YML">---

- name: config getter
  hosts: all
  gather_facts: no
  connection: local
  roles:
    - Juniper.junos
  tasks:

    - name: Pull Down The Configs
      junos_get_config:
        host: "{{ inventory_hostname }}"
        user: "neteam"
        logfile: ../logs/get_config.log
        format: text
        dest: "../configs/{{ inventory_hostname }}.jconf"</pre>

This will run through all my defined hosts, and using the Juniper role (installed previously, living in /etc/ansible/roles) - grab my router configs and store them in 'configs' directory. Note, I am using a username (same as my Linux user) and SSH key authentication, because I hate passwords and refuse to learn how to use them in Ansible 🙂

If I run this playbook, what happens?

<pre class="lang:default decode:true ">neteam@lab-ansible:~/lab-automation$ ansible-playbook scripts/config_getter.yml

PLAY [config getter] **********************************************************

TASK: [Pull Down The Configs] *************************************************
ok: [mx3]
ok: [mx1]
ok: [mx2]
ok: [mx4]

PLAY RECAP ********************************************************************
mx1                        : ok=1    changed=0    unreachable=0    failed=0
mx2                        : ok=1    changed=0    unreachable=0    failed=0
mx3                        : ok=1    changed=0    unreachable=0    failed=0
mx4                        : ok=1    changed=0    unreachable=0    failed=0</pre>

Great. My files are pulled down from the network. I can do all kinds of fun things with the Juniper Ansible library - and so can you. Check it out [here](http://junos-ansible-modules.readthedocs.io/en/1.4.3/index.html).