# Ansible Automation: Java 8, Java 21 & Python 3 on AWS EC2

Automate Java and Python installation and version verification on Ubuntu EC2 instances using Ansible, YAML playbooks, and reusable roles.

## Project Overview

This project demonstrates configuration management and software installation on remote Ubuntu EC2 worker nodes from a centralized Ansible control node.

It covers Java 8, Java 21, and Python 3, with separate role-based task files to keep the automation modular and maintainable.

## Technologies Used

- **Cloud Platform:** AWS EC2
- **Operating System:** Ubuntu Linux
- **Configuration Management:** Ansible
- **Programming Languages:** Python, Java
- **Automation Format:** YAML
- **Version Control:** Git and GitHub
- **Remote Connectivity:** SSH

## Architecture

```text
Developer / Local Machine
          |
          v
       Git / GitHub
          |
          v
  Ansible Control Node
     (Ubuntu EC2)
          |
          | SSH
          v
    Worker EC2 Node
          |
          +-- Java 8
          |
          +-- Java 21
          |
          +-- Python 3
```

## Project Objectives

- Automate software installation on Ubuntu EC2 worker nodes.
- Install and manage multiple Java versions.
- Install and verify Python 3.
- Organize automation using reusable Ansible roles.
- Execute commands remotely using Ansible.
- Manage configuration files and playbooks with Git and GitHub.

## Project Structure

```text
Ansible-codes/
├── hosts.ini
├── main.yml
├── python.yml
├── README.md
└── roles/
    ├── java/
    │   └── tasks/
    │       └── main.yml
    └── python/
        └── tasks/
            └── main.yml
```

**Note:** This is the recommended structure. Ensure `python.yml` exists and create the Java role directory if you want both installations organized as separate roles. Your existing Java playbook can be renamed to `java.yml` if preferred.

## Environment Details

| Component | Configuration |
|---|---|
| Cloud | AWS EC2 |
| Control node | Ubuntu EC2 |
| Worker node | Ubuntu EC2 |
| Connection | SSH |
| Automation | Ansible |
| Java versions | Java 8 and Java 21 |
| Python version | Python 3.14.4 (observed) |

## 1. Configure the Ansible Inventory

Example `hosts.ini`:

```ini
[workers]
worker1 ansible_host=YOUR_WORKER_IP ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/ansible-master-key
```

Replace `YOUR_WORKER_IP` with your worker's current reachable IP address.

## 2. Install Java 8 and Java 21

Run the Java playbook from the Ansible control node:

```bash
ansible-playbook -i hosts.ini main.yml --private-key /home/ubuntu/ansible-master-key
```

Use this command if `main.yml` is your existing Java playbook. If you rename it to `java.yml`, use `java.yml` in the command instead.

### Verify Java 8

```bash
ansible worker1 -i hosts.ini \
  -m shell \
  -a "'/usr/lib/jvm/java-1.8.0-openjdk-amd64/bin/java' -version 2>&1" \
  --private-key /home/ubuntu/ansible-master-key
```

### Verify Java 21

```bash
ansible worker1 -i hosts.ini \
  -m shell \
  -a "'/usr/lib/jvm/java-21-openjdk-amd64/bin/java' -version 2>&1" \
  --private-key /home/ubuntu/ansible-master-key
```

Both commands check the specified Java executable without switching the default Java version.

## 3. Install Python 3 Using an Ansible Role

The Python role is defined in:

`roles/python/tasks/main.yml`

Example tasks:

```yaml
---
- name: Update apt cache
  ansible.builtin.apt:
    update_cache: yes

- name: Install Python 3
  ansible.builtin.apt:
    name: python3
    state: present

- name: Verify Python version
  ansible.builtin.command: python3 --version
  register: python_version
  changed_when: false

- name: Display Python version
  ansible.builtin.debug:
    msg: "{{ python_version.stdout }}"
```

Example `python.yml` playbook:

```yaml
---
- name: Install Python on worker nodes
  hosts: worker1
  become: yes

  roles:
    - python
```

Execute the playbook:

```bash
ansible-playbook -i hosts.ini python.yml \
  --private-key /home/ubuntu/ansible-master-key
```

## 4. Verify All Installed Versions

### Check Java 8 and Java 21

```bash
ansible worker1 -i hosts.ini \
  -m shell \
  -a "'/usr/lib/jvm/java-1.8.0-openjdk-amd64/bin/java' -version 2>&1; '/usr/lib/jvm/java-21-openjdk-amd64/bin/java' -version 2>&1" \
  --private-key /home/ubuntu/ansible-master-key
```

### Check Python 3

```bash
ansible worker1 -i hosts.ini \
  -m command \
  -a "python3 --version" \
  --private-key /home/ubuntu/ansible-master-key
```

## Expected Results

The worker node should have these versions installed and available:

| Software | Version |
|---|---|
| Java | 1.8.0 (Java 8) |
| Java | 21 |
| Python | 3.14.4 (observed) |

Actual versions depend on the packages installed on the worker node.

## Key Learning Outcomes

- AWS EC2 instance configuration
- Ansible inventory and SSH connectivity
- Ansible ad hoc commands
- Playbooks and YAML syntax
- Role-based automation using `tasks/main.yml`
- Java version management
- Python package installation
- Git commits and GitHub repository management

## Important Notes

- Java 8 and Java 21 can coexist; only one is selected as the default Java executable at a time.
- Python 3 is managed independently of Java.
- The Python role does not install, remove, or change Java.
- Use a valid SSH private key and restrict its file permissions.
- Verify SSH host keys before trusting a remote worker.
- The example Java paths apply to the environment where those paths were verified.

## Author

**Ram Golhar**

GitHub: [@ram-golhar-33](https://github.com/ram-golhar-33)

Repository: [Ansible-codes](https://github.com/ram-golhar-33/Ansible-codes)
