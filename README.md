# 🚀 Ansible Tutorial

> **This guide will help you set up Ansible on a master machine and configure worker nodes for automation.**

---

## 📝 Prerequisites

- Replace usernames, passwords, and IP addresses as appropriate for your environment.
- Ensure SSH access is set up between the master and worker nodes.

---

## 1️⃣ Install Ansible on the Master Machine

```bash
sudo yum install ansible
```

---

## 2️⃣ Install Python on Worker Machines

```bash
sudo yum install python3
```

---

## 3️⃣ Set Hostnames

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

## 4️⃣ Update `/etc/hosts` on the Master

Edit the hosts file:
```bash
sudo vim /etc/hosts
```

Add the following lines (adjust IPs as needed):
```ini
10.100.100.129 ansibleworker1
10.100.100.130 ansibleworker2
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.72.131 master
192.168.72.132 worker
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

---

## 5️⃣ Configure Ansible

Edit the Ansible configuration file:
```bash
sudo vim /etc/ansible/ansible.cfg
```

Add or update the following sections:
```ini
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

## 6️⃣ Define Inventory Hosts

Edit the Ansible hosts file:
```bash
sudo vim /etc/ansible/hosts
```

Add the following lines:
```ini
[dev]
ansibleworker1
ansibleworker2
```

---

## 7️⃣ Add Password Variables

On the master machine, create the file `/etc/ansible/group_vars/dev.yml` with:
```yaml
ansible_become_pass: yourpassword
```

---

## 8️⃣ Test the Configuration

On the master machine, run:
```bash
ansible dev -m ping
reply
ansibleworker2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
ansibleworker1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

```


You should see a successful ping response from your worker nodes.

---

✨ **Happy Automating with Ansible!**
