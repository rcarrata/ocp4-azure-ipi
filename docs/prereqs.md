## Prerequisites for Launch the OCP4 Azure IPI

## Install Ansible and Azure Cli dependencies

```
virtualenv ansible-210
source ansible-210/bin/activate
pip install ansible==2.10
pip install -r https://raw.githubusercontent.com/ansible-collections/azure/dev/requirements-azure.txt
```

First add the vault directory and the vault yaml:

* Generate a Vault-File with the credentials of Azure and OCP4 PullSecret:

```
$ ansible-vault edit vault/azure.yml
```

* Fill inside the vault.yml with:

```
azure_subscription_id: SECRET
azure_client_id: SECRET
azure_secret: SECRET
azure_tenant: SECRET
ocp4_pull_secret: '<<< pull_secret_azure >>>'
```

* For obtain the pull_secret go to [OCP4 Install](https://cloud.redhat.com/openshift/install)

* Generate the .vault-password-file and put the password

```
touch .vault-password-file
echo "yourpasswordfancy" >> .vault-password-file
```

* Opt: Install the collection requirements

```
ansible-galaxy collection install -r collections/requirements.yml
```
