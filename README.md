Ansible summary
1-in master machine
sudo yum install ansible
2-worker machine 
 sudo yum install python3
3-in master machine change host name
  hostnamectl set-hostname master
4-in master go to 
 sudo vim /etc/host
 10.100.100.129 ansibleworker1
10.100.100.130 ansibleworker2
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.72.131 master
192.168.72.132 worker
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

5-go to 
  sudo vim /etc/ansible/ansible.cfg 
  add below lines

  [defaults]
inventory = /etc/ansible/hosts
remote_user = ammar
ask_sudo_pass = False
ask_pass      = False
host_key_checking = False
 
forks = 5

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False

6- go to /etc/ansible/hosts
sudo vim 
add below line
[dev]
ansibleworker[1:2] ansible_become_pass=ahli

7- test configuration by running in master machine 
ansible dev -m ping

# Ansible Tutorial

This guide will help you set up Ansible on a master machine and configure worker nodes for automation.

---

## Prerequisites

- Replace usernames, passwords, and IP addresses as appropriate for your environment.
- Ensure SSH access is set up between the master and worker nodes.

---

## 1. Install Ansible on the Master Machine

```bash
sudo yum install ansible
```

---

## 2. Install Python on Worker Machines

```bash
sudo yum install python3
```

---

## 3. Set Hostnames

On the **master machine**:

```bash
sudo hostnamectl set-hostname master
```

On **worker 1**:

```bash
sudo hostnamectl set-hostname ansibleworker1
```

On **worker 2**:

```bash
sudo hostnamectl set-hostname ansibleworker2
```

---

## 4. Update `/etc/hosts` on the Master

Edit the hosts file:

```bash
sudo vim /etc/hosts
```

Add the following lines (adjust IPs as needed):

```
10.100.100.129 ansibleworker1
10.100.100.130 ansibleworker2
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.72.131 master
192.168.72.132 worker
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

---

## 5. Configure Ansible

Edit the Ansible configuration file:

```bash
sudo vim /etc/ansible/ansible.cfg
```

Add or update the following sections:

```
[defaults]
inventory = /etc/ansible/hosts
remote_user = ammar
ask_sudo_pass = False
ask_pass = False
host_key_checking = False
forks = 5

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```

---

## 6. Define Inventory Hosts

Edit the Ansible hosts file:

```bash
sudo vim /etc/ansible/hosts
```

Add the following lines:

```
[dev]
ansibleworker[1:2] ansible_become_pass=ahli
```

---

## 7. Test the Configuration

On the master machine, run:

```bash
ansible dev -m ping
```

You should see a successful ping response from your worker nodes.

---

# Ansible-Tutorial
# Ansible-Tutorial
