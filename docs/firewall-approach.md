## Outbound Type - User Defined Routing - Azure Firewall

The simplest solution to securing outbound addresses lies in use of a firewall device that can control outbound traffic based on domain names. Azure Firewall, for example, can restrict outbound HTTP and HTTPS traffic based on the FQDN of the destination. You can also configure your preferred firewall and security rules to allow these required ports and addresses.

IMPORTANT: Outbound type of UDR requires there is a route for 0.0.0.0/0 and next hop destination of NVA (Network Virtual Appliance) in the route table. The route table already has a default 0.0.0.0/0 to Internet, without a Public IP to SNAT just adding this route will not provide you egress.

When using an outbound type of UDR, a load balancer public IP address for inbound requests is not created unless a service of type loadbalancer is configured. A public IP address for outbound requests is never created by AKS if an outbound type of UDR is set.

```
VNET=$(az network vnet list --query "[?contains(name, '${CLUSTER_NAME}')].{Name:name}" -o tsv)
RG=$(az network vnet list --query "[?contains(name, '${CLUSTER_NAME}')].{ResourceGroup:resourceGroup}" -o tsv)
```

## Create vnet for FW

```
az network vnet create -g ${RG} -n fw-vnet --address-prefix 10.1.0.0/16 --subnet-name AzureFirewallSubnet --subnet-prefix 10.1.1.0/24
```

## Peer vnets
### fw-net -> cluster-net
```
az network vnet peering create -g ${RG} -n fw2cluster --vnet-name fw-vnet --remote-vnet ${VNET} --allow-vnet-access
```

### cluster-net -> fw-net with forwarding
```
az network vnet peering create -g ${RG} -n cluster2fw --vnet-name ${VNET} --remote-vnet fw-vnet  --allow-forwarded-traffic --allow-vnet-access
```

## Create Firewall
```
export FW="myFirewall"
az extension add -n azure-firewall
az network firewall create -g ${RG} -n ${FW} --location eastus
```

## create static IP

```
az network public-ip create --name fw-pip --resource-group ${RG} \
  --location eastus \
  --allocation-method static \
  --sku standard
```

## Configure FW public ip
```
az network firewall ip-config create \
    --firewall-name ${FW} \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group ${RG} \
    --vnet-name fw-vnet
```

## Update config

```
az network firewall update \
    --name ${FW} \
    --resource-group ${RG}
```

# Get private ip of FW

```
fwprivaddr="$(az network firewall ip-config list -g ${RG} -f ${FW} --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

## Create new table route
```
az network route-table create \
    --name Firewall-rt-table \
    --resource-group ${RG} \
    --location eastus \
    --disable-bgp-route-propagation true
```

## Create default route
```
az network route-table route create \
  --resource-group ${RG} \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

## Associate the route table to the cluster subnets
## get subnets from the used vnet
```
subnets=`az network vnet subnet list --vnet-name ${VNET} -g ${RG} -o tsv --query '[].{Name:name}'`
```
#
```
for subnet in ${subnets}
do
az network vnet subnet update \
    --resource-group ${RG} \
    --vnet-name ${VNET} \
    -n ${subnet} \
    --route-table Firewall-rt-table
done
```

## Nat Rule
```
az network firewall nat-rule create --collection-name exampleset --destination-addresses 20.72.148.63 --destination-ports 22 --firewall-name myFirewall --name inboundrule --protocols Any --resource-group ocp4-rg --source-addresses '*' --translated-port 22 --action Dnat --priority 100 --translated-address 10.10.99.4
```


# Configure APP rules

# Get subnet prefixes
```
addressPrefix=`az network vnet subnet list --vnet-name ${VNET} -g ${RG} -o tsv --query '[].{AddressPrefix:addressPrefix}'`
```

# Example of *.google.com
```
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name ${FW} \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *google.com \
   --source-addresses ${addressPrefix} \
   --priority 200 \
   --action Allow
```

# Allow azure / microsoft / windows stuff
```
az network firewall application-rule create \
   --collection-name azure_ms \
   --firewall-name ${FW} \
   --name azure \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *azure.com *microsoft.com *microsoftonline.com *windows.net \
   --source-addresses ${addressPrefix} \
   --priority 300 \
   --action Allow
```

# Allow redhat / openshift / quay stuff
```
az network firewall application-rule create \
   --collection-name redhat \
   --firewall-name ${FW} \
   --name redhat \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *redhat.com *redhat.io *quay.io *openshift.com \
   --source-addresses ${addressPrefix} \
   --priority 400 \
   --action Allow
```

# Allow github
```
az network firewall application-rule create \
   --collection-name github \
   --firewall-name ${FW} \
   --name github \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *github.com \
   --source-addresses ${addressPrefix} \
   --priority 500 \
   --action Allow
```

# Allow docker.io
```
az network firewall application-rule create \
   --collection-name docker \
   --firewall-name ${FW} \
   --name docker \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *docker.io *docker.com \
   --source-addresses ${addressPrefix} \
   --priority 600 \
   --action Allow
```
