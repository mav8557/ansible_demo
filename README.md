# ansible_demo
Ansible Demonstration for RITLUG


## Usage

You'll need to have VirtualBox and Vagrant installed to use this demo. Then just start vagrant from within the repository:

```bash
vagrant up
```

# Ad-Hoc Commands

Ansible ping module, to check that the VMs are on and that authentication is working.

```bash
ansible app -m ping -i inventory
ansible db -m ping -i inventory
ansible atlanta -m ping -i inventory

```

We can also run basic commands. If you don't pass -m, ansible will use the ansible.builtin.command module by default. -a sets the arguments, in that case just a basic shell command.

If you need more complex commands, run the shell module with -m shell, this supports more complex commands, but is generally less secure than the command module.

```bash
ansible atlanta -a "hostname" -i inventory.ini
ansible atlanta -a "uptime -p; hostname" -m shell -i inventory.ini
```

For each of these, the "changed" flag is set to true. Ansible cannot know the result of every shell command in past, present, or future existence, so it just assumes that your command changed the system somehow.

There are many other modules for that let you configure the system and also implement proper idempotence. An example is the yum module, which we should use since these VMs run CentOS 7.

```bash
ansible atlanta -a "cowsay hello RITLUG!" -i inventory.ini # no such file
ansible atlanta -m yum -a "name=cowsay state=present" -i inventory.ini --become
ansible atlanta -a "cowsay hello RITLUG!" -i inventory.ini # works!
```
Idempotence is a big feature with Ansible - we're not installing a package every time we run this, we're *ensuring* the package is installed - it only actually installs if it isn't there.

We passed --become to become root, since we need those permissions to install packages. Try running it without --become to see a common error message.

You can similarly ensure it is removed:

```bash
ansible atlanta -m yum -a "name=cowsay state=removed" -i inventory.ini --become
```

The command module has additional protections, try the following two commands and observe that the command module will not interpret the subcommand.

```bash
ansible atlanta -a "cowsay hello RITLUG from \$(hostname)" -i inventory.ini
ansible atlanta -a "cowsay hello RITLUG from \$(hostname)" -m shell -i inventory.ini
```

# Playbooks

Ansible Ad-Hoc tasks are nice, but if you can't run multiple tasks in succession then you still have to write scripts to do complex tasks. Plays are group of module calls, and one or more plays are rolled into playbooks.

You can run a playbook with the ansible-playbook command.

```bash
ansible-playbook playbook.yml -i inventory.ini
```

Inside the file there are plays to deploy nginx to the web hosts, and an IRC server.

# Roles

Despite the convenience of deploying with one command, this playbook is somewhat cluttered - there are plays to install nginx, irc, and other packages. We could split this into multiple playbooks, but then we'd lose the convenience of a single deploy command.

The best way, outside of including tasks from other files, is to use Roles. Roles are collections of variables, tasks, and configuration that "do one thing" - installing a load balancer for your organization, setting up your users and user policies, configuring SELinux, etc. Ansible roles are frequently shared on Ansible Galaxy, a registry for all things Ansible, but especially roles.

Roles are created with the ansible-galaxy utility.

```bash
ansible-galaxy init my-role-name
```

You can go into the directory and create tasks, variables, and files that are relevant to that role. Then you just call the role from a playbook. The idea is that the role holds what data it needs, but you put variables that you need for multiple roles inside the playbook that calls them.

Take a look at rolebook.yml, which separates the nginx and irc automation functionality into discrete roles that are called for each OS group.

```bash
ansible-playbook rolebook.yml -i inventory.ini
```

## Troubleshooting

You may need to change the IPs around in the Vagrantfile and inventory to get the VMs to start on your system. Vagrant should prompt you with valid IP ranges to use for the virtualbox provider.

Otherwise, just open up an issue and if I know the solution I'll try and post it.

