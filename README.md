# Ansible Project: MetalLB, OS, and Network Roles

## Roles
- **metallb-installer**: Installs and configures MetalLB on hosts in the specified group. Configures:
  - Node3 (172.16.10.152) is the Hetzner dedicated server with public IP.
  - Additional IPs assigned to node3:
    - 168.119.65.60 (MAC: 00:50:56:01:1D:DD)
    - 168.119.65.44 (MAC: 00:50:56:15:16:3B)
  - Gateway: 168.119.65.1
  - Netmask: 255.255.255.192
  - Broadcast: 168.119.65.63
- **os**: Handles OS configuration for all hosts.
- **network**: Configures network settings for the group `public_nodes`.

## Usage
- Edit your inventory and playbooks as needed.
- Each role follows standard Ansible structure.

## Getting Started
1. Place your inventory file in the `inventory_files` folder.
2. Create a playbook referencing the roles as needed.
3. Run with `ansible-playbook -i inventory playbook.yml`.
