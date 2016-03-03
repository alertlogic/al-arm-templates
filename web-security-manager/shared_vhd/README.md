# Deploy an Alert Logic Web Security Manager VM

This template allows you to deploy an Alert Logic Web Security Manager VM, using the latest version.

## Getting Started

#### Requirements
[Create Azure Account](https://account.windowsazure.com/Home/Index)

[Install Azure CLI tools](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/)

[Azure Storage Account](https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/#create-a-storage-account)

[Azure Storage Access Key](https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/#manage-your-storage-access-keys)

#### Prepare for deployment

If you do not have a pre-existing Storage Account and Blob Container available, follow the steps below to create them.

 * Install Azure CLI tools from the link in the requirements section
 * Authenticate your session with Azure from CLI:
 
 ```
 # azure login
 info:    Executing command login
 info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code FAYFT6AS6 to authenticate.
 info:    Added subscription Alert Logic
 +
 info:    login command OK
 #
 ```
 
 * Click the 'Azure Storage Account' link in the requirements section and follow the steps to create a new storage account.
 * Create a Blob service container within your storage account by clicking Blobs -> + Container
 * Copy your storage account access key:
 
 > View and copy storage access keys
 >
 > In the Azure Portal, navigate to your storage account and click the Keys icon to view, copy, and regenerate your account access keys. The Access Keys blade also includes pre-configured connection strings using your primary and secondary keys that you can copy to use in your applications
 
 
 * Open a terminal or command prompt and set azure cli mode to asm
 
 ```
 # azure config mode asm
 info:    Executing command config mode
 info:    New mode is asm
 info:    config mode command OK
 #
 ```
 
 *  Copy the Alert Logic Web Security Manager vhd to your storage account
  
 > azure vm disk upload http://alertlogic.blob.core.windows.net/wsmimage/al-wsm-image-latest.vhd [storage account URL]/[blob container name]/[filename].vhd [storage-account-key]

 ```
 # azure vm disk upload http://alertlogic.blob.core.windows.net/wsmimage/al-wsm-image-latest.vhd http://storageaccountname.blob.core.windows.net/containername/al-wsm-image_latest.vhd [your-storage-access-key]
 info:    Executing command vm disk upload
 warn:    Any existing blob will be overwritten at http://storageaccountname.blob.core.windows.net/containername/al-wsm-image_latest.vhd
 +  Copying image: 100% (42949673472/42949673472)
 info:    vm disk upload command OK
 #
 ```


#### Deploy from browser


<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Falertlogic%2Fal-arm-templates%2Fmaster%2Fweb-security-manager%2Fshared_vhd%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>
<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Falertlogic%2Fal-arm-templates%2Fmaster%2Fweb-security-manager%2Fshared_vhd%2Fazuredeploy.json" target="_blank">
    <img src="http://armviz.io/visualizebutton.png"/>
</a>

###### Required fields
| Field | Description | Example |
| ----- |:-----------:| :------:|
| STORAGEACCOUNTNAME | Name of your new or existing storage account | mystorageaccount |
| BLOBCONTAINERNAME | Blob container within your storage account | myblobcontainer |
| OSDISKVHDURI | Fully qualified URL for Web Security Manager VHD copied to your account in previous steps | http://mystorageaccount.blob.core.windows.net/myblobcontainer/al-wsm-image_latest.vhd |
| VMNAME | Name of the new Web Security Manager VM being deployed | awesome-new-web-security-manager-vm |
| VIRTUALNETWORKNAME | Name of new or existing Virtual Network within your selected resource group | myvnet |
| VIRTUALNICNAME | Unique name for Virtual Network Interface | myvirtnic |
| PUBLICNICNAME | Unique name for Public Network Interface | mypubnic |
| SUBNETNAME | Existing subnet within your selected virtual network. If you are creating a new Virtual Network, this name can be unique. | mysubnet |
| VMSIZE | Size of the new Web Security Manager VM being deployed(Defaults to Standard_A3) | Standard_A3 |
| Resource Group | Resource Group used to deploy new Web Security Manager VM(Use an existing Resource Group or create a new one) | myResourceGroup |

#### Deploy from cli

* Set azure cli config mode to arm

```
# azure config mode arm
info:    Executing command config mode
info:    New mode is asm
info:    config mode command OK
#
```

* Get location list

```
# azure location list
info:    Executing command location list
warn:    The "location list" commands is changed to list subscription's locations. For old information, use "provider list or show" commands.
info:    Getting locations...
data:    Name                Display Name         Latitude  Longitude
data:    ------------------  -------------------  --------  ---------
data:    eastasia            East Asia            22.267    114.188  
data:    southeastasia       Southeast Asia       1.283     103.833  
data:    centralus           Central US           41.5908   -93.6208 
data:    eastus              East US              37.3719   -79.8164 
data:    eastus2             East US 2            36.6681   -78.3889 
data:    westus              West US              37.783    -122.417 
data:    northcentralus      North Central US     41.8819   -87.6278 
data:    southcentralus      South Central US     29.4167   -98.5    
data:    northeurope         North Europe         53.3478   -6.2597  
data:    westeurope          West Europe          52.3667   4.9      
data:    japanwest           Japan West           34.6939   135.5022 
data:    japaneast           Japan East           35.68     139.77   
data:    brazilsouth         Brazil South         -23.55    -46.633  
data:    australiaeast       Australia East       -33.86    151.2094 
data:    australiasoutheast  Australia Southeast  -37.8136  144.9631 
data:    southindia          South India          12.9822   80.1636  
data:    centralindia        Central India        18.5822   73.9197  
data:    westindia           West India           19.088    72.868   
info:    location list command OK
#
```

* Find your subscription id

```
# azure account list
info:    Executing command account list
data:    Name                    Id                                    Current  State  
data:    ----------------------  ------------------------------------  -------  -------
data:    Alert Logic             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false    Enabled
info:    account list command OK
#
```

* Set azure cli to use your subscription

```
# azure config set subscription
info:    Executing command config set
info:    Setting "subscription" to value "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
info:    Changes saved
info:    config set command OK
#
```

* Create a new resource group

> azure group create -n "your-group-name" -l "location-of-new-group"

```
# azure group create -n "myResourceGroup" -l "West US"
info:    Executing command group create
+ Getting resource group myResourceGroup                                       
+ Creating resource group myResourceGroup                                      
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    
info:    group create command OK
#
```

* Create a group deployment

> azure group deployment create --template-uri https://raw.githubusercontent.com/alertlogic/al-arm-templates/master/web-security-manager/shared_vhd/azuredeploy.json myResourceGroup myDeployment

```
# azure group deployment create --template-uri https://raw.githubusercontent.com/alertlogic/al-arm-templates/master/web-security-manager/shared_vhd/azuredeploy.json myResourceGroup myDeployment
info:    Executing command group deployment create
info:    Supply values for the following parameters
storageAccountName: storageaccountname
blobContainerName: containername
osDiskVhdUri: http://storageaccountname.blob.core.windows.net/containername/al-wsm-image_latest.vhd
vmName: myNewWSMVM
virtualNetworkName: myvnet
virtualNicName: myvirtnic
publicNicName: mypubnic
subnetName: mysubnet
vmSize: Standard_A3
+ Initializing template configurations and parameters                          
+ Creating a deployment                                                        
info:    Created template deployment "myDeployment"
+ Waiting for deployment to complete                                           
data:    DeploymentName     : myDeployment
data:    ResourceGroupName  : myResourceGroup
data:    ProvisioningState  : Succeeded
data:    Timestamp          : 2016-03-01T18:44:41.7279061Z
data:    Mode               : Incremental
data:    Name                         Type          Value                                                              
data:    ---------------------------  ------------  -------------------------------------------------------------------
data:    storageAccountName           String        storageaccountname                                                          
data:    blobContainerName            String        containername                                                                
data:    osDiskVhdUri                 String        http://storageaccountname.blob.core.windows.net/containername/al-wsm-image_latest.vhd
data:    adminUsername                String        alertlogic                                                         
data:    adminPassword                SecureString  undefined                                                          
data:    vmName                       String        myNewWSMVM                                                          
data:    virtualNetworkName           String        myvnet                                                         
data:    virtualNicName               String        myvirtnic                                                      
data:    publicNicName                String        mypubnic                                                      
data:    subnetName                   String        mysubnet
data:    vmSize                       String        Standard_A3                                                        
info:    group deployment create command OK
# 
```

Your new deployment should successfully create the 'myNewWSMVM' VM

## Additional information

#### Reference

[Alert Logic Web Security Manager for Azure](http://docs.alertlogic.com/#cloud/microsoft-azure-web-security-manager-premier-paas-manual.htm)

#### Deployment Parameters:
```
storageAccountName: (Storage account where the Web Security Manager VM will be deployed)
blobContainerName: (Blob container to deploy Alert Logic Web Security Manager)
osDiskVhdUri: (Link to Alert Logic Web Security Manager VHD)
adminUsername: (admin username for the VM)
adminPassword: (admin password for the VM)
vmName: (name of the newly created virtual machine)
virtualNetworkName: (Name of new or existing Virtual Network within your selected resource group)
virtualNicName: (Unique name for Virtual Network Interface)
publicNicName: (Unique name for Public Network Interface)
subnetName: (Existing subnet within your selected virtual network. If you are creating a new Virtual Network, this name can be unique.)
vmSize: (Defaults to Standard_A3)
```

