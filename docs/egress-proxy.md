# Egress Mode - Azure Proxy

## 1 Install and Configure Azure Firewall with Ansible (recommended)

### 1.1 Execute Simple Install

* Simple & Primary Option:

```
ansible-playbook install-private.yml -e "egress=proxy" --vault-password-file .vault-file-password
```

## 1.2 Checking the Azure Firewall

[Check Firewall](/docs/check_firewall)
