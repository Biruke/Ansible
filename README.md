# Arista Fabric Read-Only Audit (Ansible)

A production-grade, 100% read-only Ansible automation project for auditing Arista Spine-Leaf fabric devices.

---

## 🌐 Target Fabric Inventory

| Device Name | Role | Management IP | Group |
|---|---|---|---|
| **spine1** | Spine 1 | `192.168.12.129` | `spines` |
| **spine2** | Spine 2 | `192.168.12.130` | `spines` |
| **bl1** | Border Leaf 1 | `192.168.12.131` | `border_leafs` |
| **bl2** | Border Leaf 2 | `192.168.12.132` | `border_leafs` |
| **leaf1** | Leaf 1 | `192.168.12.133` | `leafs` |
| **leaf2** | Leaf 2 | `192.168.12.134` | `leafs` |

All switches belong to the parent group: **`arista_fabric`**.

---

## 📁 Project Structure

```text
arista-ansible-readonly/
├── ansible.cfg                          # Connection & defaults (timeout, forks, host key check)
├── inventory.yml                        # YAML inventory defining the 6 switches & groups
├── group_vars/
│   └── arista_fabric.yml                # EOS connection settings & safe credentials
├── playbooks/
│   ├── 01_ping_and_version.yml          # Collects EOS version, model, serial & uptime
│   ├── 02_interface_status.yml          # Audits IP interfaces and port status
│   ├── 03_bgp_evpn_summary.yml          # Inspects BGP IPv4 and EVPN neighbor state
│   └── 04_fabric_audit_readonly.yml     # Complete fabric health report (saved to outputs/)
├── outputs/                             # Auto-saved audit logs per switch (git-ignored)
├── .gitignore                           # Prevents committing secrets or large output logs
└── README.md
```

---

## 🔒 Safety Guarantee (Read-Only)

- **Zero configuration changes**: All playbooks use `arista.eos.eos_command` solely with operational `show` commands.
- **Never modifies running-config or startup-config**.

---

## 🛠️ Prerequisites

Install the required Ansible Arista EOS collection on your execution machine / WSL / jump server:
```bash
ansible-galaxy collection install arista.eos
ansible-galaxy collection install ansible.netcommon
```

---

## 🚀 How to Run

### Option A: Pass credentials via command line prompts (Most Secure)
```bash
# Run version check across all 6 switches:
ansible-playbook -i inventory.yml playbooks/01_ping_and_version.yml -u admin -k -K

# Run full health audit (generates reports in outputs/):
ansible-playbook -i inventory.yml playbooks/04_fabric_audit_readonly.yml -u admin -k -K
```

### Option B: Target only Spines or Leafs
```bash
# Target only spines:
ansible-playbook -i inventory.yml playbooks/01_ping_and_version.yml --limit spines -u admin -k

# Target only leafs:
ansible-playbook -i inventory.yml playbooks/01_ping_and_version.yml --limit leafs -u admin -k
```

### Option C: Using Environment Variables
```bash
export ARISTA_USER="admin"
export ARISTA_PASSWORD="yourPasswordHere"

ansible-playbook -i inventory.yml playbooks/04_fabric_audit_readonly.yml
```

---

## 💻 VS Code & GitHub Workflow

1. **Open in VS Code**:
   - Open VS Code → **File** → **Open Folder** → Select `arista-ansible-readonly`.
   - Install the **Ansible** and **YAML** extensions in VS Code for syntax highlighting and linting.
2. **Review & Edit**:
   - Add new `show` commands in `playbooks/` as needed.
3. **Commit & Push to GitHub**:
   - Use **GitHub Desktop** or the VS Code Source Control tab (`Ctrl+Shift+G`).
   - Stage changes, write a commit message (e.g., `Add Arista fabric read-only audit playbooks`), and click **Push**.
