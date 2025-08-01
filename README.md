
# Ansible Automation Project

This project automates infrastructure for Hetzner and internal nodes using Ansible roles and groups.

## Inventory Structure
- `loadbalancer-dev` and `loadbalancer-prod`: Public nodes with additional IPs and MACs.
- `srv01.de.ankra.dev`: Proxmox host (used as SSH bastion and for interface/DHCP config).
- `worker-01`, `worker-02`: Development and production workers with custom HTTP/HTTPS ports.

## Roles
- **metallb-installer**: Installs and configures MetalLB with custom IPs, MACs, gateway, netmask, and broadcast.
- **os**: Installs and configures OS packages for all hosts.
- **network**: Manages network settings for public nodes.
- **haproxy**: Installs and configures HAProxy, forwarding traffic to development/production workers.
- **nfs**: Installs and enables NFS server.
- **proxmox_interfaces**: Configures Proxmox bridge interfaces, iptables, NAT, and DHCP server files.

## Usage
1. Place your inventory file in the `inventory_files` folder.
2. Edit `group_vars/all.yml` for interface and network variables.
3. Reference roles in your playbook as needed.
4. Run:
   ```sh
   ansible-playbook -i inventory_files/inventory playbook.yml
   ```

## Example Playbook
```yaml
- name: Setup Infrastructure
  hosts: all
  become: true
  roles:
    - os
    - network
    - metallb-installer
    - haproxy
    - nfs
    - proxmox_interfaces
```

## Notes
- SSH bastion is configured via `ansible_ssh_common_args` in inventory.
- All roles follow best practices for Ansible structure and YAML syntax.
