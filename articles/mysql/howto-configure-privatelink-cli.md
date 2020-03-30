---
title: 专用链接 - Azure CLI - MySQL 的 Azure 数据库
description: 了解如何从 Azure CLI 为 MySQL 配置 Azure 数据库的专用链接
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: f83f52f1c1800803c5e1d47f1931f7b13b2c11de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368003"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>使用 CLI 为 MySQL 创建和管理 Azure 数据库的专用链接

专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。 在本文中，您将学习如何使用 Azure CLI 在 Azure 虚拟网络中创建 VM，以及使用 Azure 专用终结点为 MySQL 服务器创建 Azure 数据库。

> [!NOTE]
> 此功能在所有 Azure 区域都可用，其中 MySQL 的 Azure 数据库支持通用和内存优化定价层。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 Azure CLI，本快速入门要求使用 Azure CLI 2.0.28 或更高版本。 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

在创建任何资源之前，必须创建一个资源组以托管虚拟网络。 使用 [az group create](/cli/azure/group) 创建资源组。 本示例*在西欧*位置创建名为*myResourceGroup*的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
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

## <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器 
使用 az mysql 服务器创建命令为 MySQL 创建 Azure 数据库。 请记住，MySQL Server 的名称必须在 Azure 中是唯一的，因此请将括号中的占位符值替换为您自己的唯一值： 

```azurecli-interactive
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

请注意，MySQL 服务器 ID ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.```类似于您将在下一步中使用 MySQL 服务器 ID。 

## <a name="create-the-private-endpoint"></a>创建专用终结点 
为虚拟网络中的 MySQL 服务器创建专用终结点： 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域 
为 MySQL 服务器域创建专用 DNS 区域，并与虚拟网络创建关联链接。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> 客户 DNS 设置中的 FQDN 不会解析为配置的专用 IP。 您必须为配置的 FQDN 设置 DNS 区域，[如下所示](../dns/dns-operations-recordsets-portal.md)。

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM *myVm*，如下所示：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。**** 选择“连接”按钮后，“连接到虚拟机”随即打开********。

1. 选择**下载 RDP 文件**。 Azure 会创建远程桌面协议 (*.rdp*) 文件，并将其下载到计算机。

1. 打开 downloaded.rdp** 文件。

    1. 出现提示时，选择“连接”****。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 您可能需要选择**更多选项** > **"使用其他帐户**"来指定创建 VM 时输入的凭据。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”********。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>从 VM 私下访问 MySQL 服务器

1. 在  *myVM* 的远程桌面中打开 PowerShell。

2. 输入  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`。 

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. 使用任何可用的客户端测试 MySQL 服务器的专用链路连接。 在下面的示例中，我使用[MySQL 工作台](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html)执行操作。


4. 在 **"新建连接**"中，输入或选择此信息：

    | 设置 | “值” |
    | ------- | ----- |
    | 连接名称| 选择您选择的连接名称。|
    | 主机名 | 选择*mydemoserver.privatelink.mysql.database.azure.com* |
    | 用户名 | 输入 MySQL 服务器创建期间提供的用户名*username@servername*。 |
    | 密码 | 输入 MySQL 服务器创建期间提供的密码。 |
    ||

5. 选择“连接”。

6. 浏览左侧菜单中的数据库。

7. （可选）从 MySQL 数据库中创建或查询信息。

8. 关闭远程桌面连接到 myVm。

## <a name="clean-up-resources"></a>清理资源 
如果不再需要资源组及其所有资源，可以使用 az group delete 将其删除： 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>后续步骤
- 了解有关什么是[Azure 专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
