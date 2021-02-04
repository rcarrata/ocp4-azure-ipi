# Egress Default - Load Balancer

## 1 Install and Configure Azure with Load Balancer as Egress Outbound

```
ansible-playbook install-private.yml -e "egress=proxy" --vault-password-file .vault-file-password
```

## 2. Diagram Openshift Install using the Azure Load Balancer Outbound

 <img align="center" width="750" src="../pics/egress_azure_lb.png">
