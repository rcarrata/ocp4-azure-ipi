# IPI Installation - Mode Connected

## Fill the vars

* Copy or generate the vars.yml and customize to fill your needs:

```
cp -pr vars/example-vars.yml vars/vars.yml
```

## Execute the installation

* Execute the installation with:

```
ansible-playbook install.yml --vault-password-file .vault-file-password
```
