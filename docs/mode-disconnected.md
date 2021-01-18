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
