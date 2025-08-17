# Ankra Infrastructure Documentation

This document contains Ankra-specific infrastructure details and deployment configurations.

## Ankra Infrastructure Overview

Comprehensive Ansible automation for managing Ankra's multi-environment infrastructure including Proxmox virtualization, Kubernetes clusters, load balancers, and storage systems.

## 🏗️ Infrastructure Overview

This project manages infrastructure across two geographical locations:
- **DE (Germany)**: Production environment with Proxmox 8.4.1 + Ceph Quincy
- **UK (United Kingdom)**: Development/Testing environment with Proxmox 9.0.3 + Ceph Squid

## 🚀 Supported Environments

### DE Environment (Germany)
- **Location**: Hetzner dedicated servers
- **Proxmox Version**: 8.4.1 
- **Ceph Version**: Quincy
- **Network**: Private VLAN setup
- **Purpose**: Production workloads

### UK Environment (United Kingdom) 
- **Location**: OVH VPS cluster
- **Proxmox Version**: 9.0.3
- **Ceph Version**: Squid (latest)
- **Network**: Public IP with jump host
- **Purpose**: Development and testing

## 🎯 Host Groups

### **Public Nodes**
- `loadbalancer-dev`: Development load balancer (168.119.65.60)
- `loadbalancer-prod`: Production load balancer (168.119.65.44)

### **Kubernetes Clusters**
- **Development**: 9 nodes (3 controllers, 3 workers, 3 database)
- **Production**: 9 nodes (3 controllers, 3 workers, 3 database)
- **Infrastructure**: 6 nodes (3 controllers, 3 workers)

### **Proxmox Hosts**
- `srv01.de.ankra.dev`: German Proxmox host
- `srv01.uk.ankra.dev`: UK Proxmox host

## Current Network Configuration

### Virt-UK Environment
- **vmbr0**: Management network (172.16.20.0/24)
- **vmbr1**: Private cluster network (172.16.21.0/24) - VLAN 4000
- **vmbr2**: VM network (172.16.22.0/24)

#### Host Assignments
- srv01.virt-uk.ankra.dev: 172.16.20.104 (mgmt), 172.16.21.101 (cluster)
- srv02.virt-uk.ankra.dev: 172.16.20.102 (mgmt), 172.16.21.102 (cluster)
- srv03.virt-uk.ankra.dev: 172.16.20.103 (mgmt), 172.16.21.103 (cluster)

## Ceph Configuration

### Current Pools (virt-uk)
- `infra_k8s_data`: 32 PGs, size=2, min_size=1
- `virt_uk_data`: 32 PGs, size=3, min_size=2
- `virt_uk_vms`: 32 PGs, size=3, min_size=2

### Network Configuration
- Ceph public network: 172.16.21.0/24
- Ceph cluster network: 172.16.21.0/24

## User Accounts

### Admin Users (Current)
- **mark**: SSH key ending in ...UpQOUp, sudo access
- **mattias**: SSH key ending in ...XWbfi, sudo access

## Deployment History

### Recent Changes
- Renamed `ankra` role to `admin` for broader applicability
- Implemented VLAN 4000 for cluster communication
- Optimized Ceph PG counts for small clusters (32 PGs vs 128)
- Fixed repository conflicts between Proxmox and Ceph packages
- Improved network interface handling to prevent SSH disconnections

### Infrastructure Evolution
- Started with basic cluster setup
- Added comprehensive network architecture
- Integrated Ceph storage with optimized configurations
- Implemented role-based automation for all services

## Troubleshooting Notes

### Ceph PG Count Issues
- Original configuration used 128 PGs per pool
- With 3 OSDs and replication factor 3, this exceeded the 250 PG/OSD limit
- Solution: Reduced to 32 PGs per pool for optimal distribution

### Network Configuration Challenges
- DHCP server conflicts resolved by proper interface validation
- SSH connection drops during interface configuration fixed with better process management
- VLAN configuration requires proper switch support

### Repository Management
- APT conflicts between enterprise and no-subscription repos resolved
- Proper keyring management implemented for package verification
- Repository cleanup prevents conflicting package sources

## Access Information

### Jump Host Configuration
- Primary jump host: 51.89.192.98
- SSH config: `ansible_ssh_common_args: '-o ProxyJump=root@51.89.192.98'`

### Service Endpoints
- Proxmox web interface: https://[host-ip]:8006
- Ceph dashboard: https://[host-ip]:8443
- HAProxy stats: http://[loadbalancer-ip]/stats

## Security Considerations

### Network Security
- VLAN isolation between management and cluster traffic
- Firewall rules restrict Ceph communication to private network
- SSH access controlled through jump host

### Access Control
- Key-based authentication for all admin accounts
- Sudo access limited to authorized users
- Service-specific user accounts where applicable

---

This documentation is specific to Ankra's infrastructure and should be kept private. For general ProxSpray usage, refer to the main README.md file.
