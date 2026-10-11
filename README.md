# Ansible Python Automation Using Roles

## Objective

Automate the installation and verification of Python 3 on Ubuntu EC2 worker nodes using Ansible roles.

## Tools and Technologies

- AWS EC2
- Ubuntu Linux
- Ansible
- Git
- GitHub
- YAML
- Python 3

## Project Architecture

**Local Development → Git → GitHub → Ansible Control Node → Ubuntu EC2 Worker**

## Project Structure

```text
Ansible-codes/
├── hosts.ini
├── python.yml
├── README.md
└── roles/
    └── python/
        └── tasks/
            └── main.yml
```

## Role Description

The `roles/python/tasks/main.yml` file performs the following tasks:

1. Updates the Ubuntu APT package cache.
2. Installs Python 3 if it is not already installed.
3. Checks the installed Python version.
4. Displays the Python version in the Ansible output.

## Implementation Steps

### 1. Configure the Inventory

Add the worker node's IP address and SSH connection settings to `hosts.ini`.

### 2. Create the Ansible Role

Create the directory `roles/python/tasks/` and define the installation tasks in `main.yml`.

### 3. Execute the Playbook

```bash
ansible-playbook -i hosts.ini python.yml
```

### 4. Verify Python Installation

```bash
ansible worker1 -i hosts.ini -m command -a "python3 --version"
```

## Expected Result

Ansible connects to the Ubuntu EC2 worker, ensures Python 3 is installed, and displays the Python version.

## Key Learning Outcomes

- Ansible role structure and task organization
- YAML syntax and playbook execution
- Remote package installation using APT
- Infrastructure automation on AWS EC2
- Git and GitHub version control

## Important Note

This project automates Python 3 installation only. It does not install, remove, or configure Java.
