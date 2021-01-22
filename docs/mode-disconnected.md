# IPI Installation - Mode Disconnected

## Fill the vars

* Copy or generate the vars.yml and customize to fill your needs:

```
cp -pr varsnexample-vars.yml vars/vars-disconnected.yml
```

## Execute the installation

```
ansible-playbook install-private.yml --vault-password-file .vault-file-password
```
