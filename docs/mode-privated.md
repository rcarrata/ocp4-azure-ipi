# IPI Installation - Mode Privated

## Fill the vars

* Copy or generate the vars.yml and customize to fill your needs:

```
cp -pr vars/example-vars.yml vars/vars-privated.yml
```

## Execute the installation

```
ansible-playbook install-private.yml --vault-password-file .vault-file-password
```

NOTE: This will use the proxy by default as egress / OutboundType. For other egress modes check the
egress-firewall, egress-lb and egress-nat documentation.


