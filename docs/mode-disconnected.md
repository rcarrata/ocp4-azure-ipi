# IPI Installation - Mode Disconnected

## Fill the vars

* Edit the vars-private.yml and customize to fill your needs:

```
vim vars/vars-disconnected.yml
```

## Execute the installation

```
ansible-playbook install-disconnected.yml --vault-password-file .vault-file-password
```

## OutboundMode
