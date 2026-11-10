# Ansible – Configuration Management

This section documents my hands-on learning of Ansible, focusing on configuration management and remote server administration.

The practical exercise involves using one Ubuntu machine as the Ansible Control Node to configure another Ubuntu machine (Managed Node).

## 1. Understanding Ansible

Ansible is an open-source automation tool used for configuration management, application deployment, provisioning, and task automation.

It allows administrators to manage multiple remote servers from a central machine without installing an Ansible agent on every managed server.

### Ansible Architecture

- **Control Node:** The main machine where Ansible is installed and commands are executed.
- **Managed Node:** The remote machine that Ansible configures.
- **Inventory:** A file containing the IP addresses or hostnames of managed machines.
- **Modules:** Reusable units that perform specific operations.
- **Playbooks:** YAML files containing instructions for automating multiple tasks.

## 2. Installing Ansible on the Control Node

Update the package repository:

```bash
sudo apt update
```

Install Ansible:

```bash
sudo apt install ansible -y
```

Verify the installation:

```bash
ansible --version
```

## 3. Configuring Passwordless SSH Authentication

Ansible communicates with managed nodes through SSH.

To avoid repeatedly entering passwords, configure SSH key-based authentication.

### Generate an SSH key pair

```bash
ssh-keygen
```

Follow the prompts to generate your SSH keys.

### Copy the public key to the target server 

```bash
ssh-copy-id ubuntu@<TARGET_SERVER_IP>
...or maually add the key to the authorized_keys in the target server
```

### Test the SSH connection

```bash
ssh ubuntu@<TARGET_SERVER_IP>
```

If successful, you should be able to access the target server without entering its account password each time.

## 4. Creating an Ansible Inventory File

An inventory file contains the list of servers Ansible will manage.

Create a file named:

```bash
vim inventory
```

Example:

```ini
172.31.23.241
```

You can also group servers according to their roles.

Example:

```ini
[webservers]
172.31.23.241

[dbservers]
172.23.45.21
```

In this example, `webservers` and `dbservers` are inventory groups.

Verify the inventory:

```bash
ansible -i inventory all --list-hosts
```

You know how you create a sh file if you want to run multiple shell commands, and sometimes you just run a single cmd in the  terminal directly.
Ansible Ad-hoc commands and playbook are similar in that approach. It is like choosing to write a shell command without the need of creating a .sh file.

## 5. Ansible Ad-hoc Commands

Ad-hoc commands are used to execute individual tasks on remote servers without creating a playbook. 

They are particularly useful for quick administrative operations.

### General Syntax

```bash
ansible -i <inventory_file> <host_pattern> -m <module> -a "<arguments>"
```

### Understanding the options

| Option | Description |
|---|---|
| `-i` | Specifies the inventory file |
| `all` | Targets all hosts in the inventory |
| `-m` | Specifies the Ansible module |
| `-a` | Supplies arguments to the module |

### Practical Example: Creating a File on a Target Server

```bash
ansible -i inventory all -m shell -a "touch devopsclass"
```

This command creates an empty file named `devopsclass` on all servers listed in the inventory.

An alternative using the dedicated file module:

## 6. Ansible Playbooks

While ad-hoc commands are useful for individual tasks, playbooks are more suitable for automating multiple operations.

Ansible playbooks are written in YAML and allow us to define a sequence of tasks that should be executed on managed nodes.

### Important Playbook Components

| Component | Description |
|---|---|
| `name` | Describes the play or task |
| `hosts` | Specifies the target machines |
| `become` | Enables privilege escalation |
| `tasks` | Contains the operations to execute |
| `apt` | Manages packages on Ubuntu/Debian |
| `service` | Manages system services |

## 7. Practical Exercise: Installing and Starting Nginx on a Target Server

**Objective:** Use Ansible from the main server to install and start Nginx on a remote Ubuntu machine.

### Step 1: Create the Playbook

Create a YAML file:

```bash
vim firstPlaybook.yml
```

Add the following configuration:

```yaml
---
- name: Install and Start nginx
  hosts: all
  become: true

  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

    - name: Start nginx
      service:
        name: nginx
        state: started
```

### Step 2: Execute the Playbook

Run the following command from the Ansible control node:

```bash
ansible-playbook -i inventory firstPlaybook.yml
```

Ansible will:

1. Connect to the target server through SSH.
2. Gather information about the target machine.
3. Install Nginx if it is not already installed.
4. Start the Nginx service.

### Step 3: Verify the Installation

Log in to the target server:

```bash
ssh ubuntu@<TARGET_SERVER_IP>
```

Check the Nginx service:

```bash
sudo systemctl status nginx
```

You should see that the Nginx service is active and running.


## 8. Understanding the Play Recap

After executing a playbook, Ansible displays a summary of the execution.

Example:

```text
PLAY RECAP
172.31.23.241 : ok=3 changed=1 unreachable=0 failed=0
```

| Status | Meaning |
|---|---|
| `ok` | Tasks completed successfully |
| `changed` | Tasks that made changes to the target server |
| `unreachable` | Ansible could not establish a connection |
| `failed` | Tasks that encountered errors |
| `skipped` | Tasks that were skipped |

In my practical exercise:

- Ansible successfully connected to the target server.
- Facts were gathered successfully.
- Nginx was installed.
- The Nginx service was started.
- No tasks failed.

## 9. Key Takeaways

Through this practical exercise, I learned how to:

- Install and configure Ansible on an Ubuntu server.
- Establish passwordless SSH authentication.
- Connect a control node to remote managed nodes.
- Create and manage inventory files.
- Group servers according to their roles.
- Execute ad-hoc commands.
- Understand Ansible modules and their arguments.
- Create and execute YAML-based playbooks.
- Automate Nginx installation and service management on a remote server.
- Interpret Ansible execution results.

## 10. Useful Resources

- [Official Ansible Documentation](https://docs.ansible.com/)
- [Ansible Inventory Guide](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html)
- [Ansible Ad-hoc Commands](https://docs.ansible.com/ansible/latest/command_guide/intro_adhoc.html)
- [Ansible Playbooks](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html)

---

*This practical exercise is part of my ongoing DevOps learning journey.*
