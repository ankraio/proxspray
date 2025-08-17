# Inventory Example

This directory contains example configurations for setting up a ProxSpray environment.

## Quick Setup

1. Copy this entire directory to create your environment:
   ```bash
   cp -r inventory-example inventory/my-environment
   ```

2. Edit the files to match your infrastructure:
   - `inventory`: Update hostnames and IP addresses
   - `group_vars/all.yml`: Configure cluster settings and user accounts
   - `host_vars/*.yml`: Set host-specific network configurations

## Key Configuration Points

### Network Configuration
- Update IP ranges in `group_vars/all.yml` to match your networks
- Modify `ceph_network` to use your private network range
- Update host-specific IPs in `host_vars/` files

### User Management
- Add your SSH public keys to `admin_users` in `group_vars/all.yml`
- Configure user permissions and shell preferences

### Cluster Settings
- Set your cluster name in `proxmox_cluster_name`
- Adjust `min_cluster_nodes` based on your setup
- Configure Ceph pools based on your storage requirements

## VLAN Configuration

This example uses VLAN 4000 for the private network (`vmbr1`). Make sure your network infrastructure supports this VLAN or change it to match your environment.

## Testing

Before running the full playbook, test connectivity:
```bash
ansible -i inventory all -m ping
```
