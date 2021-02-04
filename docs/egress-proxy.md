# Egress Mode - Azure Proxy

## 1 Install and Configure Azure Firewall with Ansible (recommended)

```
ansible-playbook install-private.yml -e "egress=proxy" --vault-password-file .vault-file-password
```

## 1.2 Checking the Azure Firewall

[Check Firewall](/docs/check_firewall)

## 2. Diagram Openshift Install using the Azure Proxy Outbound

<img align="center" width="750" src="../pics/egress_azure_proxy.png">
