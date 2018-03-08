# Ansible Role SSH Cluster

This is an ansible role for installing SSH keys between Hosts. This might be useful for rsync over SSH or other tools.

## Usage

Example usage:
```yaml
- hosts: webservers
  vars:
    ssh_targets: "{{ groups['webservers'] }}"
  roles:
    - role: ccremer.ssh-cluster
```

This will enable root access between all hosts in group `webservers`. e.g. Server A can access Server B and C over SSH. Server
B can access Server A and C, and so on.
SSH keys will be generated if they don't exist yet and inserted into `~/.ssh/authorized_keys` on the target machines.
Also, the current host keys of the target machines will get added to `~/.ssh/known_hosts`, so that you do not need to confirm first connection, while still having host key checks enabled.


## Advanced Usage

Advanced usage (with default values):
```yaml
- hosts: server.a.localdomain
  vars:
    # This definition basically says that server A can access server B and C, but B and C cannot access A.
    # The actual default is empty list []
    ssh_targets:
      - server-b.localdomain
      - server-c.localdomain
    # Setting the user is theoretically implemented, but UNTESTED (I needed root access)
    ssh_user: root
    ssh_known_hosts_rehash: true
  roles:
    - role: ccremer.ssh-cluster
```

## Gotchas

- This role does not setup the initial connection of your Ansible machine to the configured hosts.
- This role rehashes the host names in `~/.ssh/known_hosts`, which is the default in debian/ubuntu. If you use and rely on
  unhashed hostnames in this file, disable it by setting `ssh_known_hosts_rehash` to false.
