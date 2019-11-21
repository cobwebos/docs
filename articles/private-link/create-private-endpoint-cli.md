---
title: Create an Azure private endpoint using Azure CLI| Microsoft Docs
description: Learn about Azure private endpoint
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 467b2426ccd69a27adc9df7ee3ff0304886b8f4a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224866"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Create a private endpoint using Azure CLI
Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link Resources. In this Quickstart, you will learn how to create a VM on a virtual network, a SQL Database Server with a Private Endpoint using Azure CLI. Then, you can access the VM to and securely access the private link resource (a private Azure SQL Database server in this example). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 Azure CLI，本快速入门要求使用 Azure CLI 2.0.28 或更高版本。 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

Before you can create any resource, you have to create a resource group to host the Virtual Network. 使用 [az group create](/cli/azure/group) 创建资源组。 This example creates a resource group named *myResourceGroup* in the *westcentralus* location:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
Create a Virtual Network with [az network vnet create](/cli/azure/network/vnet). This example creates a default Virtual Network named *myVirtualNetwork* with one subnet named *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Disable subnet private endpoint policies 
Azure deploys resources to a subnet within a virtual network, so you need to create or update the subnet to disable private endpoint network policies. Update a subnet configuration named *mySubnet* with [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>创建 VM 
Create a VM with az vm create. When prompted, provide a password to be used as the sign-in credentials for the VM. This example creates a VM named *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Note the public IP address of the VM. 在下一步中，此地址将用于从 Internet 连接到 VM。

## <a name="create-a-sql-database-server"></a>Create a SQL Database Server 
Create a SQL Database Server with the az sql server create command. Remember that the name of your SQL Server must be unique across Azure, so replace the placeholder value in brackets with your own unique value: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Note the SQL Server ID is similar to ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` You will use the SQL Server ID in the next step. 

## <a name="create-the-private-endpoint"></a>Create the Private Endpoint 
Create a private endpoint for the SQL Database server in your Virtual Network: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Configure the Private DNS Zone 
Create a Private DNS Zone for SQL Database server domain and create an association link with the Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

Connect to the VM *myVm* from the internet as follows:

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 创建远程桌面协议 (.rdp) 文件，并下载到计算机。

1. 打开下载的 .rdp* 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Access SQL Database Server privately from the VM

In this section, you will connect to the SQL Database Server from the VM using the Private Endpoint.

 1. In the Remote Desktop of *myVM*, open PowerShell.
 2. Enter nslookup myserver.database.windows.net  You'll receive a message similar to this: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Install SQL Server Management Studio 
 4. In Connect to server, enter or select this information: Server type: Select Database Engine.
 Server name: Select myserver.database.windows.net Username: Enter a username provided during creation.
 Password: Enter a password provided during creation.
 Remember password: Select Yes.
 
 5. 选择“连接”。
 6. Browse **Databases** from left menu.
 7. (Optionally) Create or query information from *mydatabase*
 8. Close the remote desktop connection to *myVm*.

## <a name="clean-up-resources"></a>清理资源 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>后续步骤
- Learn more about [Azure Private Link](private-link-overview.md)
 
