---
title: 快速入门 - 使用 Azure CLI 创建 Azure 专用终结点
description: 在本快速入门中了解 Azure 专用终结点
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: dbcb833e6f8b90cebd3d013e58168558bcd96827
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459971"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>快速入门：使用 Azure CLI 创建专用终结点
专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。 在本快速入门中，你将了解如何使用 Azure CLI 在虚拟网络中创建一个 VM，以及一个包含专用终结点的 SQL 数据库服务器。 然后，可以访问该 VM，并安全地访问专用链接资源（本示例中的专用 Azure SQL 数据库服务器）。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 Azure CLI，本快速入门要求使用 Azure CLI 2.0.28 或更高版本。 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

在创建任何资源之前，必须创建一个资源组以托管虚拟网络。 使用 [az group create](/cli/azure/group) 创建资源组。 此示例在 *westcentralus* 位置创建名为 *myResourceGroup* 的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
使用 [az network vnet create](/cli/azure/network/vnet) 创建虚拟网络。 此示例创建名为 *myVirtualNetwork* 的默认虚拟网络，它具有一个名为 *mySubnet* 的子网：

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>禁用子网专用终结点策略 
Azure 会将资源部署到虚拟网络中的子网，因此，你需要创建或更新子网，以禁用专用终结点网络策略。 使用 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 更新名为 *mySubnet* 的子网配置：

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>创建 VM 
使用 az vm create 创建 VM。 出现提示时，请提供要用作 VM 登录凭据的密码。 此示例创建名为 *myVm* 的 VM： 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 记下 VM 的公共 IP 地址。 在下一步中，此地址将用于从 Internet 连接到 VM。

## <a name="create-a-sql-database-server"></a>创建 SQL 数据库服务器 
使用 az sql server create 命令创建 SQL 数据库服务器。 请记住，你的 SQL Server 名称必须在 Azure 中是唯一的，因此请将括号中的占位符值替换为你自己的唯一值： 

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

请注意，SQL Server ID 类似于  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.```。你将在下一步中使用 SQL Server ID。 

## <a name="create-the-private-endpoint"></a>创建专用终结点 
为虚拟网络中的 SQL 数据库服务器创建专用终结点： 
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
## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域 
为 SQL 数据库服务器域创建专用 DNS 区域，并创建一个与虚拟网络关联的链接。 
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

从 Internet 连接到 VM *myVm*，如下所示：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。  选择“连接”按钮后，“连接到虚拟机”随即打开   。

1. 选择“下载 RDP 文件”  。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开下载的 .rdp* 文件。

    1. 出现提示时，选择“连接”  。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据   。

1. 选择“确定”  。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”   。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-sql-database-server-privately-from-the-vm"></a>以私密方式从 VM 访问 SQL 数据库服务器

在本部分中，将使用专用终结点从 VM 连接到 SQL 数据库服务器。

 1. 在 *myVM* 的远程桌面中，打开 PowerShell。
 2. 输入 nslookup myserver.database.windows.net 。你将收到类似于以下内容的消息： 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. 安装 SQL Server Management Studio 
 4. 在“连接到服务器”中，输入或选择以下信息：服务器类型：选择“数据库引擎”。
 服务器名称：选择 myserver.database.windows.net 用户名：输入在创建过程中提供的用户名。
 密码：输入在创建过程中提供的密码。
 记住密码：选择“是”。
 
 5. 选择“连接”  。
 6. 浏览左侧菜单中的“数据库”  。
 7. （可选）创建或查询 *mydatabase* 中的信息
 8. 关闭与 *myVm* 的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源 
如果不再需要资源组及其所有资源，可以使用 az group delete 将其删除： 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 专用链接](private-link-overview.md)
