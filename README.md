
# ProxSpray 🚀

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Ansible](https://img.shields.io/badge/Ansible-2.18%2B-red.svg)](https://ansible.com)
[![Ankra](https://img.shields.io/badge/Proxmox-VE%208%2B-green.svg)](https://www.ankra.io)

## 🛠️ Advanced Features

### Network Segmentation
- **VLAN Support**: Configurable VLAN tagging for secure cluster communication
- **Traffic Isolation**: Separate networks for management, cluster, and VM traffic
- **Firewall Integration**: Automated firewall rules for network security
- **DHCP Management**: Automated DHCP configuration for VM networks

### Storage Optimization
- **Intelligent PG Calculation**: Automatic placement group calculation based on cluster size
- **Performance Tuning**: Optimized Ceph configurations for various workloads
- **Pool Management**: Automated creation and configuration of storage pools
- **Backup Integration**: Hooks for backup and disaster recovery systems

### High Availability Features
- **Cluster Formation**: Automated Proxmox cluster setup and maintenance
- **Load Balancing**: HAProxy configuration with health checks
- **Monitoring Stack**: Prometheus metrics and log aggregation
- **Failover Support**: Automatic failover and recovery mechanismscom)

**ProxSpray** is a comprehensive Ansible automation framework for deploying and managing Proxmox VE clusters with integrated Ceph storage, advanced networking, and infrastructure services.

## ✨ Features

- 🔄 **Automated Proxmox VE Cluster Setup** - Deploy multi-node Proxmox clusters with zero manual intervention
- 💾 **Integrated Ceph Storage** - Automated Ceph cluster configuration with optimized pool management
- 🌐 **Advanced Network Architecture** - VLAN-based network segmentation with bridge interfaces
- 👥 **User Management** - Centralized admin user provisioning with SSH key management
- ⚖️ **Load Balancing** - HAProxy integration for high availability
- 📊 **Monitoring & Logging** - Built-in Prometheus and centralized logging support
- 🔒 **Security Hardening** - Production-ready security configurations
- 🎯 **Production Ready** - Battle-tested configurations with proper resource allocation

## 📋 Prerequisites

- **Target Hosts**: 3+ servers with Debian/Ubuntu and Proxmox VE installed
- **Control Machine**: Ansible 2.18+ installed
- **Authentication**: SSH key-based authentication configured
- **Privileges**: Root or sudo access on target hosts
- **Network**: Proper VLAN support if using advanced networking features

## 🏗️ Architecture Overview

ProxSpray implements a robust three-tier network architecture designed for production environments:

```
┌─────────────────┬─────────────────┬─────────────────┐
│     vmbr0       │     vmbr1       │     vmbr2       │
│  Public/Mgmt    │   Private/Ceph  │   VM Network    │
│  192.168.1.0/24 │ 192.168.10.0/24 │ 192.168.20.0/24 │
│                 │   (VLAN 4000)   │                 │
└─────────────────┴─────────────────┴─────────────────┘
```

- **vmbr0**: Management network for SSH access and Proxmox web interface
- **vmbr1**: Private network for Proxmox cluster and Ceph communication (VLAN tagged)
- **vmbr2**: Dedicated network for virtual machines with DHCP services

### Key Design Principles

- **Network Isolation**: VLAN segmentation ensures secure cluster communication
- **High Availability**: Multi-node design with automatic failover capabilities
- **Scalability**: Modular role-based architecture supports easy expansion
- **Security**: Defense-in-depth approach with multiple security layers

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/ankraio/proxspray.git
cd proxspray
```

### 2. Prepare Your Environment

Copy the example inventory and customize for your infrastructure:

```bash
cp -r inventory-example inventory/my-cluster
```

### 3. Configure Hosts

Edit `inventory/my-cluster/inventory`:
```ini
[proxmox]
pve01.example.com ansible_host=192.168.1.101
pve02.example.com ansible_host=192.168.1.102
pve03.example.com ansible_host=192.168.1.103
```

### 4. Configure Variables

Edit `inventory/my-cluster/group_vars/all.yml`:
```yaml
# Cluster configuration
proxmox_cluster_name: "my-cluster"
ceph_network: "192.168.10.0/24"

# Admin users
admin_users:
  - name: admin
    public_key: "ssh-rsa AAAAB3NzaC1yc2E... your-public-key-here"
    sudo: true
```

### 5. Deploy Infrastructure

```bash
# Full deployment
ansible-playbook -i inventory/my-cluster/inventory playbook.yml

# Specific components only
ansible-playbook -i inventory/my-cluster/inventory playbook.yml --tags proxmox_cluster
```

### 6. Verify Deployment

```bash
# Check cluster status
ansible -i inventory/my-cluster/inventory proxmox -m shell -a "pvecm status"

# Check Ceph health
ansible -i inventory/my-cluster/inventory proxmox -m shell -a "ceph health"
```

## 📁 Role Breakdown

### Core Infrastructure Roles

#### `admin` - User & Access Management
- **Purpose**: Centralized user account management across all infrastructure
- **Features**:
  - Creates admin users with SSH key authentication
  - Configures sudo access and shell preferences
  - Manages user groups and permissions
- **Key Files**: User creation, SSH key deployment, sudo configuration

#### `os` - Operating System Configuration
- **Purpose**: Base OS hardening and package management
- **Features**:
  - Installs essential system packages (curl, vim, etc.)
  - Performs basic OS configuration and updates
  - Prepares system for service deployment

#### `network` - Network Interface Management
- **Purpose**: Advanced network configuration for complex topologies
- **Features**:
  - Manages static and DHCP interface configurations
  - Configures network bridges and VLAN tagging
  - Handles network restart and validation

### Proxmox Ecosystem Roles

#### `proxmox` - Repository & Package Management
- **Purpose**: Proxmox VE repository configuration and package updates
- **Features**:
  - Manages Proxmox and Ceph repository configurations
  - Handles enterprise vs. no-subscription repositories
  - Ensures clean package state and GPG key management
- **Key Components**: APT repository templates, keyring management

#### `proxmox_interfaces` - Bridge & Network Setup
- **Purpose**: Creates and manages Proxmox bridge interfaces with advanced networking
- **Features**:
  - Creates vmbr0, vmbr1 (VLAN), and vmbr2 bridge interfaces
  - Configures DHCP services for VM networks
  - Implements firewall rules for network segmentation
  - Sets up NAT and traffic forwarding
- **Network Architecture**: Three-tier bridge design with VLAN isolation

#### `proxmox_cluster` - Cluster Formation & Management
- **Purpose**: Orchestrates Proxmox cluster creation and node joining
- **Features**:
  - Automated cluster initialization and node joining
  - Hostname resolution and SSH key management
  - Certificate management and cluster validation
  - Handles clone misconfigurations and cluster healing
- **Advanced Features**: Split-brain prevention, automatic failover configuration

#### `proxmox_ceph` - Ceph Storage Integration
- **Purpose**: Deploys and manages Ceph distributed storage
- **Features**:
  - Creates optimized Ceph pools with proper PG allocation
  - Configures replication and min_size parameters
  - Manages Ceph authentication and user permissions
  - Calculates optimal placement group counts for cluster size
- **Storage Pools**: Separate pools for different workload types

### Service & Application Roles

#### `haproxy` - Load Balancer Management
- **Purpose**: High-availability load balancing for applications
- **Features**:
  - Configures HAProxy for development and production environments
  - Manages SSL termination and backend health checks
  - Provides template-based configuration management

#### `security` - System Hardening
- **Purpose**: Implements security best practices and hardening
- **Features**:
  - Firewall configuration and access control
  - SSH security hardening
  - Service isolation and permission management

#### `node_exporter` - Monitoring Integration
- **Purpose**: Prometheus-based monitoring and metrics collection
- **Features**:
  - Deploys Node Exporter for system metrics
  - Configures service monitoring and alerting
  - Integrates with monitoring stack

#### `fluentbit` - Log Management
- **Purpose**: Centralized logging and log forwarding
- **Features**:
  - Configures Fluent Bit for log collection
  - Manages log parsing and forwarding rules
  - Integrates with centralized logging systems

#### `nfs` - Network File System
- **Purpose**: Shared storage services
- **Features**:
  - Configures NFS server and client setups
  - Manages shared directories and permissions

## 📁 Project Structure

```
proxspray/
├── inventory-example/          # Example configuration templates
│   ├── inventory              # Sample host definitions
│   ├── group_vars/all.yml    # Global configuration variables
│   └── host_vars/            # Per-host specific configurations
├── roles/                     # Ansible roles (see Role Documentation)
│   ├── admin/                # User and access management
│   ├── proxmox/              # Proxmox repository management
│   ├── proxmox_cluster/      # Cluster formation and management
│   ├── proxmox_ceph/         # Ceph storage integration
│   ├── proxmox_interfaces/   # Network bridge configuration
│   ├── haproxy/              # Load balancer management
│   ├── security/             # System hardening
│   └── ...                   # Additional service roles
├── playbook.yml              # Main deployment playbook
├── production.yml            # Production-specific playbook
├── requirements.txt          # Python dependencies
└── README.md                 # This documentation
```

## ⚙️ Configuration Examples

### Multi-Environment Setup

ProxSpray supports multiple environments with separate inventories:

```
inventory/
├── production/
│   ├── inventory
│   ├── group_vars/all.yml
│   └── host_vars/
├── staging/
│   ├── inventory
│   ├── group_vars/all.yml
│   └── host_vars/
└── development/
    ├── inventory
    ├── group_vars/all.yml
    └── host_vars/
```

## �️ Advanced Features

### VLAN Network Segmentation
- Automatic VLAN 4000 configuration for cluster communication
- Traffic isolation between management, cluster, and VM networks
- Advanced firewall rules for network security

### Ceph Storage Optimization
- Automatic PG count calculation based on cluster size
- Optimized pool configurations for different workload types
- Built-in monitoring and health checks

### High Availability
- Multi-master cluster configuration
- Automatic failover and recovery
- Split-brain prevention mechanisms

### Production Hardening
- Security-first configuration approach
- Automated certificate management
- Comprehensive logging and monitoring

## 📊 Monitoring & Health Checks

### Built-in Health Checks
- Cluster node status validation
- Ceph health monitoring
- Network connectivity tests
- Service availability checks


### Additional Resources
- **Examples**: See `inventory-example/` for configuration templates
- **Troubleshooting**: Common issues and solutions below
- **Best Practices**: Recommended configurations and deployment patterns
- **Advanced Configuration**: Custom setups and integrations

## 🧪 Testing

```bash
# Syntax check
ansible-playbook --syntax-check -i inventory-example/inventory playbook.yml

# Check mode (dry run)
ansible-playbook --check -i inventory/my-cluster/inventory playbook.yml

# Test connectivity
ansible -i inventory/my-cluster/inventory all -m ping

# Verify specific components
ansible-playbook -i inventory/my-cluster/inventory playbook.yml --tags verify
```

## 🔧 Maintenance & Operations

### Regular Maintenance
```bash
# Update all systems
ansible-playbook -i inventory/my-cluster/inventory playbook.yml --tags update

# Check cluster health
ansible all -i inventory/my-cluster/inventory -m shell -a "pvecm status"

# Monitor Ceph health
ansible all -i inventory/my-cluster/inventory -m shell -a "ceph health detail"
```

### Scaling Operations
```bash
# Add new nodes
ansible-playbook -i inventory/my-cluster/inventory playbook.yml --limit new_nodes

# Remove nodes (ensure proper cleanup)
ansible-playbook -i inventory/my-cluster/inventory playbook.yml --tags remove-node
```

## 🐛 Troubleshooting

### Common Issues & Solutions

#### Cluster Formation Problems
```bash
# Check cluster status
ansible all -i inventory -m shell -a "pvecm status"

# Verify network connectivity on cluster network
ansible all -i inventory -m shell -a "ping -c 3 <cluster_ip>"

# Check corosync configuration
ansible all -i inventory -m shell -a "corosync-cfgtool -s"
```

#### Ceph Storage Issues
```bash
# Check Ceph health
ansible all -i inventory -m shell -a "ceph health detail"

# Verify OSD status
ansible all -i inventory -m shell -a "ceph osd tree"

# Check placement groups
ansible all -i inventory -m shell -a "ceph pg stat"
```

#### Network Configuration Problems
```bash
# Verify bridge configuration
ansible all -i inventory -m shell -a "brctl show"

# Check VLAN configuration
ansible all -i inventory -m shell -a "cat /proc/net/vlan/config"

# Test connectivity between bridges
ansible all -i inventory -m shell -a "ip route show"
```

#### SSH and Authentication Issues
```bash
# Test SSH connectivity
ansible all -i inventory -m ping

# Verify SSH keys
ansible all -i inventory -m shell -a "ssh-add -l"

# Check sudo permissions
ansible all -i inventory -m shell -a "sudo -l"
```

### Debug Mode
Enable verbose output for troubleshooting:
```bash
ansible-playbook -vvv -i inventory/my-cluster/inventory playbook.yml
```

### Recovery Procedures
```bash
# Restart Proxmox services
ansible all -i inventory -m service -a "name=pveproxy state=restarted"
ansible all -i inventory -m service -a "name=pvedaemon state=restarted"

# Restart Ceph services
ansible all -i inventory -m service -a "name=ceph-mon@{{ ansible_hostname }} state=restarted"
ansible all -i inventory -m service -a "name=ceph-mgr@{{ ansible_hostname }} state=restarted"

# Network interface reset (use with caution)
ansible all -i inventory -m shell -a "systemctl restart networking"
```

---

**Maintained by**: Ankra Infrastructure Team
**Last Updated**: August 2025
