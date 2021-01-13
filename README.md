# Openshift 4 Azure IPI installation

Ansible Repository for deploy Openshift 4 clusters with IPI installation in connected, private and disconnected mode

There is different approaches for each mode, but always an IPI installation is used.

## Prereqs

## Installation Modes

### Connected Installation

```
ansible-playbook install.yml --vault-password-file .vault-file-password
```

### Private Installation

```
ansible-playbook install-private.yml --vault-password-file .vault-file-password
```

### Disconnected Installation

```
ansible-playbook install-private.yml --vault-password-file .vault-file-password
```


## Quick Install




