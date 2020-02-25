---
title: Azure Database for MySQL （预览版） CLI 安装方法的专用链接
description: 了解如何从 Azure CLI 配置 Azure Database for MySQL 的专用链接
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 7d9a401bfbf1f0c63995c8f7773abb6e8e874e7e
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561691"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-cli"></a>使用 CLI 创建和管理 Azure Database for MySQL （预览版）的专用链接

专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。 在本文中，你将了解如何使用 Azure CLI 在 Azure 虚拟网络中创建 VM，并使用 Azure 私有终结点在 Azure Database for MySQL 服务器中创建 VM。

> [!NOTE]
> 此功能适用于所有 Azure Database for MySQL 支持常规用途和内存优化定价层的 Azure 区域。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 Azure CLI，本快速入门要求使用 Azure CLI 2.0.28 或更高版本。 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

在创建任何资源之前，必须创建一个资源组以托管虚拟网络。 使用 [az group create](/cli/azure/group) 创建资源组。 此示例在 " *westeurope* " 位置创建名为 " *myResourceGroup* " 的资源组：

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
Azure 会将资源部署到虚拟网络中的子网，因此，你需要创建或更新子网，以禁用专用终结点网络策略。 使用 *az network vnet subnet update* 更新名为 [mySubnet](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 的子网配置：

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
使用 az MySQL server create 命令创建 Azure Database for MySQL。 请记住，MySQL 服务器的名称必须在 Azure 中是唯一的，因此请将占位符中的占位符值替换为你自己的唯一值： 

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

请注意，MySQL 服务器 ID 类似于在下一步中将使用 MySQL 服务器 ID ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.```。 

## <a name="create-the-private-endpoint"></a>创建专用终结点 
在虚拟网络中为 MySQL 服务器创建专用终结点： 
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
为 MySQL server 域创建专用 DNS 区域，并创建与虚拟网络的关联链接。 
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
> "Customer DNS" 设置中的 FQDN 不会解析为配置的专用 IP。 需要为配置的 FQDN 设置 DNS 区域[，如下所示。](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM *myVm*，如下所示：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开 downloaded.rdp 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” **“使用其他帐户”，以指定在创建 VM 时输入的凭据** > 。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

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

3. 使用任何可用的客户端测试 MySQL 服务器的专用链接连接。 在下面的示例中，我使用了[MySQL 工作台](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html)来执行该操作。


4. 在 "**新建连接**" 中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 连接名称| 选择所选的连接名称。|
    | 主机名 | 选择*mydemoserver.privatelink.mysql.database.azure.com* |
    | 用户名 | 输入用户名作为在创建 MySQL server 过程中提供 *username@servername* 。 |
    | 密码 | 输入在创建 MySQL server 期间提供的密码。 |
    ||

5. 选择“连接”。

6. 浏览左侧菜单中的数据库。

7. 同时创建或查询 MySQL 数据库中的信息。

8. 关闭与 myVm 的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源 
如果不再需要资源组及其所有资源，可以使用 az group delete 将其删除： 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>后续步骤
- 了解[什么是 Azure 专用终结点的](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)详细信息
