# Ansible Playbooks

- playbooks-linux: Install and manage services
- playbooks-infra: Build Proxmox VMs
- playbooks-network: Manage network appliances (Cisco, Opnsense)
- playbooks-macos: Manage MacOS hosts
- playbooks-cloud: AWS instance management
- playbooks-desktop: Manage Linux desktop hosts
- playbooks-other: Anything else


## Provisioning VMs

- Update playbooks-infra/create-vm-guest.yml with appropriate vars for CT
- ``ansible-playbook playbooks-infra/create-vm-guest.yml -i inventory/hosts.ini --ask-vault-pass``

```bash
# bl @ Bryans-MacBook-Pro in ~/dev/ansible on git:master o [15:15:51] 
$ ansible-playbook playbooks-infra/create-vm-guest.yml -i inventory/hosts.ini --ask-vault-pass
Vault password: 

PLAY [Create VMs] *************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************
ok: [10.2.2.5]

TASK [Include vars] ***********************************************************************************************************
ok: [10.2.2.5]

TASK [Create new container] ***************************************************************************************************
ok: [10.2.2.5] => (item={'ct': 100, 'hostname': 'dns-1', 'memory': 1024, 'storage': 16, 'ip': '10.2.2.100/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 101, 'hostname': 'dns-2', 'memory': 1024, 'storage': 16, 'ip': '10.2.2.101/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 102, 'hostname': 'docker-svcs', 'memory': 4096, 'storage': 64, 'ip': '10.2.2.102/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 103, 'hostname': 'docker-dash', 'memory': 4096, 'storage': 64, 'ip': '10.2.2.103/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 104, 'hostname': 'postgres', 'memory': 2048, 'storage': 64, 'ip': '10.2.2.104/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 105, 'hostname': 'gittea', 'memory': 4096, 'storage': 32, 'ip': '10.2.2.105/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
changed: [10.2.2.5] => (item={'ct': 106, 'hostname': 'uptime-kuma', 'memory': 512, 'storage': 16, 'ip': '10.2.2.106/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})

TASK [Start containers] *******************************************************************************************************
ok: [10.2.2.5] => (item={'ct': 100, 'hostname': 'dns-1', 'memory': 1024, 'storage': 16, 'ip': '10.2.2.100/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 101, 'hostname': 'dns-2', 'memory': 1024, 'storage': 16, 'ip': '10.2.2.101/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 102, 'hostname': 'docker-svcs', 'memory': 4096, 'storage': 64, 'ip': '10.2.2.102/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 103, 'hostname': 'docker-dash', 'memory': 4096, 'storage': 64, 'ip': '10.2.2.103/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 104, 'hostname': 'postgres', 'memory': 2048, 'storage': 64, 'ip': '10.2.2.104/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
ok: [10.2.2.5] => (item={'ct': 105, 'hostname': 'gittea', 'memory': 4096, 'storage': 32, 'ip': '10.2.2.105/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})
changed: [10.2.2.5] => (item={'ct': 106, 'hostname': 'uptime-kuma', 'memory': 512, 'storage': 16, 'ip': '10.2.2.106/8', 'gateway': '10.0.0.1', 'dns': '8.8.8.8'})

PLAY RECAP ********************************************************************************************************************
10.2.2.5                   : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

After VM(s) are up, add to ``inventory/inventory-vm-guest.ini``

```ini
[deployment]
uptime-kuma       ansible_host=10.2.2.106 ansible_user=root
```

Now run the ``playbooks-linux/base-config.yml``

``ansible-playbook playbooks-linux/base-config.yml -i inventory/inventory-vm-guest.ini ``

```bash
# bl @ Bryans-MacBook-Pro in ~/dev/ansible on git:master x [17:04:55] 
$ ansible-playbook playbooks-linux/base-config.yml -i inventory/inventory-vm-guest.ini        

PLAY [deployment] *************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************
ok: [uptime-kuma]

TASK [Create bl user] *********************************************************************************************************
changed: [uptime-kuma]

TASK [Ensure .ssh directory exists] *******************************************************************************************
changed: [uptime-kuma]

TASK [Download authorized_keys] ***********************************************************************************************
[WARNING]: Module remote_tmp /root/.ansible/tmp did not exist and was created with a mode of 0700, this may cause issues when
running as another user. To avoid this, create the remote_tmp dir with the correct permissions manually
changed: [uptime-kuma]

TASK [Install packages] *******************************************************************************************************
changed: [uptime-kuma]

TASK [Generate SSH key] *******************************************************************************************************
[WARNING]: Module remote_tmp /home/bl/.ansible/tmp did not exist and was created with a mode of 0700, this may cause issues
when running as another user. To avoid this, create the remote_tmp dir with the correct permissions manually
changed: [uptime-kuma]

TASK [Download dotfiles] ******************************************************************************************************
changed: [uptime-kuma] => (item={'url': 'https://raw.githubusercontent.com/blurer/dotfiles/refs/heads/main/editors/.vimrc', 'dest': '/home/bl/.vimrc'})
changed: [uptime-kuma] => (item={'url': 'https://raw.githubusercontent.com/blurer/dotfiles/refs/heads/main/shell/.bashrc', 'dest': '/home/bl/.bashrc'})

TASK [Allow passwordless sudo for bl user] ************************************************************************************
changed: [uptime-kuma]

TASK [Disable root login] *****************************************************************************************************
changed: [uptime-kuma]

TASK [Set timezone] ***********************************************************************************************************
changed: [uptime-kuma]

RUNNING HANDLER [Restart SSH] *************************************************************************************************
changed: [uptime-kuma]

PLAY RECAP ********************************************************************************************************************
uptime-kuma                : ok=11   changed=10   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

login to VM to ensure that bashrc, ets are present

```bash
# bl @ Bryans-MacBook-Pro in ~/dev/ansible on git:master x [17:06:46] 
$ ssh 10.2.2.106
Welcome to Ubuntu 22.04 LTS (GNU/Linux 6.8.12-5-pve x86_64)
<...>
[bl@uptime-kuma ~] $ 
```

go back into ``inventory/inventory-vm-guest.ini`` to swap the user to ``bl`` from ``root``

```ini
[deployment]
uptime-kuma       ansible_host=10.2.2.106 ansible_user=bl
```

run the ``set-dns.yml`` via ``ansible-playbook playbooks-linux/set-dns.yml -i inventory/inventory-vm-guest.ini``

```bash
# bl @ Bryans-MacBook-Pro in ~/dev/ansible on git:master x [17:08:48] C:4
$ ansible-playbook playbooks-linux/set-dns.yml -i inventory/inventory-vm-guest.ini

PLAY [deployment] *************************************************************************************************************

TASK [set dns servers in resolv.conf] *****************************************************************************************
changed: [uptime-kuma]

PLAY RECAP ********************************************************************************************************************
uptime-kuma                : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

Now the VM is ready to deploy sfotware to. 