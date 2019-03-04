# Ansible Playbooks

A collection of dirty, but useful Ansible playbooks that I have scaped together. 

* updateCentos.yml - Updates yum cache and pacakges.
* updateUbuntu.yml - Updates apt cache, packages, and autoremove.
* f2b.yml - Install fail2ban, starts the service, and enables at boot. [CentOS]
* web.yml - Install apache, start the service, and enables at boot. [CentOS]
* template.yml - Template syntax that includes output of given command.

## Next Steps..
* Make my Ansible inventory variable based to allow for playbook execution to not fail by region. 
*Example: EU has mixture of Ubuntu and CentOS systems, however playbook fails because of a Ubuntu host having playbook for CentOS executed.*
* Create playbooks for most used applications - vim, zsh, git, etc
* Create playbooks to update hosts on a schedule - i.e. update all cache and packages at 0200 GMT daily. 

