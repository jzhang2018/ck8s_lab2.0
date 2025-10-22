##
# set controller (there are still some manual steps)
ansible-playbook -i inventory/controller.yml playbooks/setup-controller.yml

# set system hostnames
# - it also adds all system hostnames to /etc/hosts for DNS resolution
ansible-playbook -i inventory/hosts.yml playbooks/set-hostnames.yml

# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
# cluster-wide passwordless SSH playbooks (order matters), will be refctored into role
# playbook 0: Prepopulate known_hosts for all nodes (IP, hostname), 127.0.0.1 and localhost. So ssh won't prompt for SSH host key verification
ansible-playbook -i inventory/hosts.yml playbooks/populate-known-hosts.yml

# playbook 1: backup existing ssh keys
ansible-playbook -i inventory/hosts.yml playbooks/backup-ssh-keys.yml

# playbook 2: generate ssh keys - id_ed25519 (personal) and juju keypair
ansible-playbook -i inventory/hosts.yml playbooks/generate-ssh-keys.yml

# playbook 3: add public personal key and juju key to authorized_keys for all hosts
ansible-playbook -i inventory/hosts.yml playbooks/self-ssh-setup.yml

# playbook 4: cluster-wide nodes SSH key distribution - each node's authorized_keys should have all nodes personal key
ansible-playbook -i inventory/hosts.yml playbooks/nodes-ssh-key-distribution.yml

# playbook 5: distribute juju public key to all nodes
ansible-playbook -i inventory/hosts.yml playbooks/juju-key-distribution.yml

# playbook 6: full ssh validation
ansible-playbook -i inventory/hosts.yml playbooks/full-ssh-validation.yml
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

# disable swap space
ansible-playbook -i inventory/hosts.yml playbooks/disable-swap.yml

# time sync
ansible-playbook -i inventory/hosts.yml playbooks/time-sync.yml

# check firewall rules
ansible-playbook -i inventory/hosts.yml playbooks/firewall-ck8s.yml --check

# verify nodes to juju-controller connectivities
ansible-playbook -i inventory/hosts.yml playbooks/verify-juju-ctrl-conn.yml