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
ansible atlanta -a "hostname" -i inventory
ansible atlanta -a "uptime -p; hostname" -m shell
```

For each of these, the "changed" flag is set to true. Ansible does not parse and know the result of every shell command in existence, so it assumes that it changed the system somehow.

There are many other modules for that let you configure the system and also implement proper idempotence. An example is the yum module, which we should use since these VMs run CentOS 7.

```bash
ansible atlanta -a "cowsay hello RITLUG!" -i inventory # no such file
ansible atlanta -m yum -a "name=cowsay state=present"
ansible atlanta -a "cowsay hello RITLUG!" -i inventory # works!
```
Idempotence is a big feature with Ansible - we're not installing a package every time we run this, we're *ensuring* the package is installed - it only actually installs if it isn't there.

You can similarly ensure it is removed:

```bash
ansible atlanta -m yum -a "name=cowsay state=removed"
```

# Playbooks

# Roles

## Troubleshooting

You may need to change the IPs around in the Vagrantfile and inventory to get the VMs to start on your system. Vagrant should prompt you with valid IP ranges to use for the virtualbox provider.

Otherwise, just open up an issue and if I know the solution I'll try and post it.

