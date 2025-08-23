# ProxSpray 🚀

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Ansible](https://img.shields.io/badge/Ansible-2.18%2B-red.svg)](https://ansible.com)
[![Proxmox](https://img.shields.io/badge/Proxmox-VE%208%2B-orange.svg)](https://proxmox.com)
[![Maintained by Ankra](https://img.shields.io/badge/Maintained%20by-Ankra-blue.svg)](https://ankra.io)

**ProxSpray** is a fast, production-ready Ansible automation for deploying Proxmox clusters with Ceph, advanced networking, and more.

---

## 🚀 Quick Start

1. **Clone the repo**
  ```bash
  git clone https://github.com/ankraio/proxspray.git
  cd proxspray
  ```

2. **Copy and edit the example inventory**
  ```bash
  cp -r inventory-example inventory/my-cluster
  # Edit inventory/my-cluster/inventory and group_vars/all.yml
  ```

3. **Run the playbook**
  ```bash
  ansible-playbook -i inventory/my-cluster/inventory playbook.yml
  ```

4. **Check your cluster**
  ```bash
  ansible -i inventory/my-cluster/inventory proxmox -m shell -a "pvecm status"
  ansible -i inventory/my-cluster/inventory proxmox -m shell -a "ceph health"
  ```

---

## 🧩 Terraform Integration

The output of ProxSpray (API tokens, user credentials, and environment files in `~/tmp/.proxmox-terraform/`) works directly with [ankraio/proxmox-terraform](https://github.com/ankraio/proxmox-terraform) for seamless infrastructure-as-code workflows.

---

**Maintained by**: [Ankra Infrastructure Team](https://ankra.io)
