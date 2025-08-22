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

## Configuration Options

### Single Node vs Multi-Node Setup

This example is configured for a **3-node cluster** by default. Choose your deployment type:

#### **Multi-Node Cluster (Production)**
- **Hosts**: 3+ Proxmox nodes
- **High Availability**: Data replicated across multiple nodes
- **Configuration**: Use the active configuration in `group_vars/all.yml`
- **Ceph**: `size=3, min_size=2` (data stored on 3 nodes, minimum 2 required)

#### **Single Node (Development/Testing)**
- **Hosts**: 1 Proxmox node only
- **No Replication**: Data stored without redundancy
- **Configuration**: Uncomment the "OPTION 2" section in `group_vars/all.yml`
- **Ceph**: `size=1, min_size=1` (data stored on 1 node only)

**⚠️ Important**: Single node setups have no data redundancy. Use only for testing!

### Key Configuration Points

#### Network Configuration
- Update IP ranges in `group_vars/all.yml` to match your networks
- Modify `ceph_network` to use your private network range
- Update host-specific IPs in `host_vars/` files

#### User Management
- Add your SSH public keys to `admin_users` in `group_vars/all.yml`
- Configure user permissions and shell preferences

#### Cluster Settings
- **Multi-node**: `min_cluster_nodes: 3`, `proxmox_cluster_enabled: true`
- **Single-node**: `min_cluster_nodes: 1`, `proxmox_cluster_enabled: false`
- Set your cluster name in `proxmox_cluster_name`

## VLAN Configuration

This example uses VLAN 4000 for the private network (`vmbr1`). Make sure your network infrastructure supports this VLAN or change it to match your environment.

## Testing

Before running the full playbook, test connectivity:
```bash
ansible -i inventory all -m ping
```
