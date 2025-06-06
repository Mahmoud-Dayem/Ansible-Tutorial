# 🚀 Ansible Tutorial Project

Welcome! This repository demonstrates how to automate Linux administration tasks using [Ansible](https://www.ansible.com/).  
You'll find playbooks for user management, password updates, file handling, and more.

---

## 📁 Project Structure

```
ansible-tutorial/
├── Ansible-Tutorial/
│   ├── ansible.cfg
│   ├── hosts
│   └── README.md
├── changepasswor.yaml
├── handler.yaml
├── playbook.yaml
├── secrets.yaml
├── updatepassword.yaml
├── usercreate.yaml
├── userscreate.yaml
```

---

## 📝 Prerequisites

- **Ansible installed** on the control/master node.
- **Python 3** installed on all managed nodes.
- SSH access set up between master and worker nodes.
- Inventory and configuration files set up (see below).

---

## ⚙️ Configuration

### 1. Install Ansible

```bash
sudo yum install ansible
```

### 2. Install Python on Worker Nodes

```bash
sudo yum install python3
```

### 3. Set Hostnames

```bash
sudo hostnamectl set-hostname master         # On master
sudo hostnamectl set-hostname ansibleworker1 # On worker 1
sudo hostnamectl set-hostname ansibleworker2 # On worker 2
```

### 4. Update `/etc/hosts` on Master

```ini
10.100.100.129 ansibleworker1
10.100.100.130 ansibleworker2
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.72.131 master
192.168.72.132 worker
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

### 5. Ansible Configuration (`ansible.cfg`)

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

### 6. Inventory File (`hosts`)

```ini
[dev]
ansibleworker1
ansibleworker2
```

---

## 🔑 Password Management

- **Best Practice:** Store sensitive variables (like passwords) in an encrypted file using [Ansible Vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html).
- Example:  
  Encrypt your secrets file:
  ```bash
  ansible-vault create secrets.yaml
  ```
  Add:
  ```yaml
  new_password: your_secure_password
  ```

---

## 📚 Playbooks Overview

### 1. `usercreate.yaml` – Create a User and Grant Sudo

Creates user `basma`, adds to group `dev`, and grants passwordless sudo.

```yaml
- name: Create user Basma and set sudo permissions
  hosts: all
  become: yes
  tasks:
    - name: Create the dev group (if it doesn't exist)
      group:
        name: dev
        state: present

    - name: Create user Basma and add to the dev group
      user:
        name: basma
        groups: dev
        append: yes
        shell: /bin/bash

    - name: Grant sudo privileges to Basma
      copy:
        content: "basma ALL=(ALL) NOPASSWD: ALL"
        dest: /etc/sudoers.d/basma
        mode: "0440"
```

---

### 2. `userscreate.yaml` – Create Multiple Users

Creates users `user1` to `user10` with `/bin/bash` shell.

```yaml
- name: Create multiple users
  hosts: all
  become: yes
  tasks:
    - name: Create users from user1 to user10
      user:
        name: "{{ item }}"
        state: present
        shell: /bin/bash
      loop:
        - user1
        - user2
        - user3
        - user4
        - user5
        - user6
        - user7
        - user8
        - user9
        - user10
```

---

### 3. `changepasswor.yaml` – Change a User's Password (Hardcoded)

Changes `basma`'s password to `'ahli'` (hashed).

```yaml
- name: Change user password
  hosts: all
  become: yes
  tasks:
    - name: Update Basma's password
      user:
        name: basma
        password: "{{ 'ahli' | password_hash('sha512') }}"
```

---

### 4. `updatepassword.yaml` – Change a User's Password (From Vault)

Reads `new_password` from `secrets.yaml` (should be encrypted with Ansible Vault).

```yaml
- name: Change user password
  hosts: all
  become: yes
  vars_files:
    - secrets.yaml
  tasks:
    - name: Print message
      ansible.builtin.debug:
        msg: "Hello, {{ new_password }}!"
    - name: Update Basma's password
      user:
        name: basma
        password: "{{ new_password | password_hash('sha512') }}"
```

**Run with:**
```bash
ansible-playbook updatepassword.yaml --ask-vault-pass
```

---

### 5. `playbook.yaml` – Basic Playbook Example

Pings hosts and prints the `new_password` variable.

```yaml
- name: My first play
  hosts: dev
  vars_files:
    - secrets.yaml
  tasks:
   - name: Ping my hosts
     ansible.builtin.ping:

   - name: Print message
     ansible.builtin.debug:
       msg: "Hello, {{ new_password }}!"
```

---

### 6. `handler.yaml` – Using Handlers

Writes to `/etc/notes` and uses a handler to print its contents.

```yaml
- name: Write and read a file using handler
  hosts: dev
  become: yes
  tasks:
    - name: Write to /etc/notes
      copy:
        content: "iam Mahmoud abdedayem"
        dest: /etc/notes
        mode: "0644"
      notify: Print file contents  # Triggers the handler

  handlers:
    - name: Print file contents
      command: cat /etc/notes
      register: file_content

    # To show the file content, uncomment:
    # - name: Show the file content
    #   debug:
    #     msg: "{{ file_content.stdout }}"
```

---

## 🛠️ Useful Ansible Commands

- **Check number of modules:**
  ```bash
  ansible-doc -l | wc -l
  ```
- **Check inventory graph:**
  ```bash
  ansible-inventory --graph
  ```
- **Run a shell command:**
  ```bash
  ansible dev -m shell -a "kill -9 1193"
  ```
- **Install a package:**
  ```bash
  ansible dev -m package -a "name=curl state=present"
  ```
- **Show help for a module:**
  ```bash
  ansible-doc package
  ```
- **Run a playbook:**
  ```bash
  ansible-playbook playbook.yaml
  ```
- **Check uptime:**
  ```bash
  ansible dev -m command -a "uptime -p"
  ```
- **Show system facts:**
  ```bash
  ansible all -m setup
  ```
- **Encrypt a file with Ansible Vault:**
  ```bash
  ansible-vault encrypt file.yaml
  ```
- **Run a playbook with vault password prompt:**
  ```bash
  ansible-playbook updatepassword.yaml --ask-vault-pass
  ```

---

## 💡 Tips

- **Never store plain-text passwords in your playbooks or inventory.** Use Ansible Vault for secrets.
- **Group variables** can be stored in `group_vars/<group>.yml` for better organization.
- **Handlers** are triggered by tasks and run at the end of a play unless notified otherwise.

---

## ✨ Happy Automating with Ansible!

For more, see the [Ansible Documentation](https://docs.ansible.com/).


-ansible galaxy
ansible-galaxy role init myrole
apb runrole.yaml
-usefull commands
ansible-doc -l

ansible-galaxy collection list
ansible dev -m copy -a 'content="hello eyad mohamed mazen ammar " dest=/etc/mtd'
ansible dev -m package -a "name=nmap state=latest"
ansible dev -m service -a "name=httpd state=started enabled=yes"
ansible-doc modulename
ansible-navigator doc user
ansible-vault create sensitive_data
ansible-vault view sensitive_data
ansible-vault edit sensitive_data
ansible-vault decrypt sensitive_data
ansible-vault rekey sensitive_data
ansible-playbook playbookname.yaml --ask-vault-pass







