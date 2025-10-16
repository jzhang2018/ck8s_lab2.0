##
# set system hostnames
ansible-playbook -i inventory/hosts.yml playbooks/set-hostnames.yml

# disable swap space
ansible-playbook -i inventory/hosts.yml playbooks/disable-swap.yml

# time sync
ansible-playbook -i inventory/hosts.yml playbooks/time-sync.yml

# check firewall rules
ansible-playbook -i inventory/hosts.yml playbooks/firewall-ck8s.yml --check

# apply firewall rules
ansible-playbook -i inventory/hosts.yml playbooks/firewall-ck8s.yml