# IPI Installation - Mode Connected

## Fill the vars

* Edit the vars.yml and customize to fill your needs:

```
vim vars/vars.yml
```

## Execute the installation

* Execute the installation with:

```
ansible-playbook install.yml --vault-password-file .vault-file-password
```
