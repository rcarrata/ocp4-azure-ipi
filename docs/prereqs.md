## Prerequisites for Launch the OCP4 Azure IPI

## Install Azure CLI and configure your account

Follow the [Installation Guide for Azure CLI](https://docs.microsoft.com/es-es/cli/azure/install-azure-cli)

```
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc

echo -e "[azure-cli]
name=Azure CLI
baseurl=https://packages.microsoft.com/yumrepos/azure-cli
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc" | sudo tee /etc/yum.repos.d/azure-cli.repo

sudo dnf install azure-cli
```

```
az login
```

## Configure your Azure Account for OpenShift Installer

* Fill and create the Azure Creds for the Service Principal inherit by the Openshift Installer

```
cat ~/.azure/osServicePrincipal.json
{"subscriptionId":"xxxx","clientId":"xxxx","clientSecret":"xxxx","tenantId":"xxxx"}
```

## Install Ansible and Azure Cli dependencies

```
pip install virtualenv
virtualenv ansible-210
source ansible-210/bin/activate
pip install ansible==2.10
pip install selinux
ansible-galaxy collection install azure.azcollection
pip install -r ~/.ansible/collections/ansible_collections/azure/azcollection/requirements-azure.txt
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
echo "yourpasswordfancy" >> .vault-file-password
```

* Choose the installation mode that you like and enjoy!
