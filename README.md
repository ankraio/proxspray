
# Ankra Infrastructure Automation

Comprehensive Ansible automation for managing Ankra's multi-environment infrastructure including Proxmox virtualization, Kubernetes clusters, load balancers, and storage systems.

## 🏗️ Infrastructure Overview

This project manages infrastructure across two geographical locations:
- **DE (Germany)**: Production environment with Proxmox 8.4.1 + Ceph Quincy
- **UK (United Kingdom)**: Development/Testing environment with Proxmox 9.0.3 + Ceph Squid

## 📁 Project Structure

```
ansible/
├── inventory/
│   ├── de/                     # German infrastructure
│   │   ├── inventory          # Host definitions
│   │   ├── group_vars/
│   │   │   └── all.yml       # DE-specific variables
│   │   └── host_vars/        # Individual host configs
│   └── uk/                    # UK infrastructure
│       ├── inventory         # Host definitions
│       ├── group_vars/
│       │   └── all.yml      # UK-specific variables
│       └── host_vars/       # Individual host configs
├── roles/                    # Ansible roles
├── playbook.yml             # Main playbook
└── README.md               # This file
```

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

## 🔧 Ansible Roles

### **Core Infrastructure**
| Role | Description | Targets |
|------|-------------|---------|
| `os` | Base OS configuration and packages | All hosts |
| `network` | Network interface configuration | Public nodes |
| `security` | Security hardening and firewall | All hosts |

### **User Management**
| Role | Description | Features |
|------|-------------|----------|
| `ankra` | User account management | SSH keys, sudo access, shell config |

### **Load Balancing**
| Role | Description | Features |
|------|-------------|----------|
| `haproxy` | Load balancer configuration | Multi-environment routing, SSL termination |

### **Virtualization & Storage**
| Role | Description | Features |
|------|-------------|----------|
| `proxmox` | Proxmox VE repository management | No-subscription repos, version-specific |
| `proxmox_interfaces` | Network bridge configuration | DHCP, NAT, interface management |
| `proxmox_ceph` | Ceph storage cluster | Pool creation, user management, RBD |

### **Monitoring & Logging**
| Role | Description | Features |
|------|-------------|----------|
| `node_exporter` | Prometheus metrics collection | System monitoring |
| `fluentbit` | Log aggregation and forwarding | Centralized logging |

### **Services**
| Role | Description | Features |
|------|-------------|----------|
| `nfs` | Network File System | Shared storage |

## 🌍 Environment Configuration

### **DE Environment (Production)**
```yaml
proxmox_version: "8.4.1"     # Stable LTS
ceph_version: "quincy"       # Proven stable (17.x)
ansible_ssh_common_args: '-o ProxyJump=root@168.119.65.46'
```

### **UK Environment (Development)**
```yaml
proxmox_version: "9.0.3"     # Latest features
ceph_version: "squid"        # Newest release (19.x)
ansible_ssh_common_args: '-o ProxyJump=root@51.89.192.98'
```

## 🚀 Usage

### **Run Complete Infrastructure Setup**
```bash
# German environment
ansible-playbook -i inventory/de/inventory playbook.yml

# UK environment  
ansible-playbook -i inventory/uk/inventory playbook.yml
```

### **Run Specific Roles**
```bash
# User management only
ansible-playbook -i inventory/de/inventory playbook.yml --tags ankra

# Proxmox configuration only
ansible-playbook -i inventory/de/inventory playbook.yml --tags proxmox

# HAProxy load balancer only
ansible-playbook -i inventory/de/inventory playbook.yml --tags haproxy

# Ceph storage only
ansible-playbook -i inventory/de/inventory playbook.yml --tags ceph
```

### **Target Specific Hosts**
```bash
# Development cluster only
ansible-playbook -i inventory/de/inventory playbook.yml --limit development

# Production cluster only
ansible-playbook -i inventory/de/inventory playbook.yml --limit production

# Proxmox hosts only
ansible-playbook -i inventory/de/inventory playbook.yml --limit proxmox
```

## 👥 User Management

The `ankra` role manages user accounts across all infrastructure:

```yaml
ankra_users:
  - name: mark
    public_key: "ssh-ed25519 AAAA..."
    sudo: true
    shell: /bin/bash
  - name: mattias  
    public_key: "ssh-ed25519 AAAA..."
    sudo: true
    shell: /bin/bash
```

## 🗄️ Storage Configuration

### **Ceph Pools**
- `infra_k8s_data`: Infrastructure Kubernetes storage
- `prod_k8s_data`: Production Kubernetes storage (DE only)
- `dev_k8s_data`: Development Kubernetes storage (DE only)

### **Pool Configuration**
```yaml
ceph_pools:
  - name: infra_k8s_data
    size: 2          # Replication factor
    min_size: 1      # Minimum replicas for I/O
```

## 🔐 Security Features

- **SSH key-based authentication** for all users
- **Passwordless sudo** for administrative users
- **Bastion host access** via ProxyJump
- **Firewall configuration** via security role
- **Enterprise repository disabling** for cost savings

## 📊 Monitoring Stack

- **Node Exporter**: System metrics collection
- **Fluent Bit**: Log aggregation and forwarding
- **HAProxy stats**: Load balancer monitoring

## 🔗 Network Architecture

### **DE Network Segments**
- `172.16.10.0/24`: Development network
- `10.1.100.0/24`: Production network  
- `10.1.110.0/24`: Infrastructure network

### **UK Network Segments**
- `172.16.20.0/24`: Development network

### **Public IPs**
- **DE**: 168.119.65.44 (prod), 168.119.65.60 (dev)
- **UK**: Managed via different public IP range

## 🔄 Maintenance

### **Update System Packages**
```bash
ansible-playbook -i inventory/de/inventory playbook.yml --tags os
```

### **Update Proxmox/Ceph**
```bash
ansible-playbook -i inventory/de/inventory playbook.yml --tags proxmox,ceph
```

### **Reconfigure Load Balancers**
```bash
ansible-playbook -i inventory/de/inventory playbook.yml --tags haproxy
```

## 📋 Prerequisites

- Ansible 2.9+
- SSH access to bastion hosts
- Proper SSH key configuration
- Python 3.x on target hosts

## 🔧 Development

This project follows Ansible best practices:
- Role-based organization
- Environment-specific variables
- Idempotent operations
- Tag-based execution
- Proper error handling

---

**Maintained by**: Ankra Infrastructure Team  
**Last Updated**: August 2025
