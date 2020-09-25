---
title: 管理虚拟网络-Azure CLI Azure Database for MySQL-灵活的服务器
description: 使用 Azure CLI 为 Azure Database for MySQL 灵活的服务器创建和管理虚拟网络
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 5cd35b896419dd30a8a4a18056ac1ccd48d7df6c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331703"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>使用 Azure CLI 为 Azure Database for MySQL 灵活的服务器创建和管理虚拟网络

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供

Azure Database for MySQL 灵活服务器支持两种类型的互斥网络连接方法来连接到灵活服务器。 这两个选项如下：

- 公共访问（允许的 IP 地址）
- 专用访问（VNet 集成）

在本文中，我们将重点介绍如何使用 Azure CLI ** (VNet 集成) ** 创建 MySQL 服务器。 通过 *私有访问 (VNet 集成) *，你可以将灵活的服务器部署到你自己的 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)中。 Azure 虚拟网络提供专用的安全网络通信。 在 "专用访问" 中，到 MySQL 服务器的连接仅限在虚拟网络中。 若要了解详细信息，请参阅 [ (VNet 集成) 的私有访问权限 ](./concepts-networking.md#private-access-vnet-integration)。

在 Azure Database for MySQL 灵活的服务器中，你只能在创建服务器的过程中将服务器部署到虚拟网络和子网。 将灵活的服务器部署到虚拟网络和子网后，不能将其移动到另一个虚拟网络、子网或 *公共访问 (允许的 IP 地址) *。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以在单独的浏览器标签页中通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 打开 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后选择 Enter 来运行它。  

如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>先决条件

需要使用 [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) 命令登录账户。 请注意 **ID** 属性，它是指 Azure 帐户的 **订阅 id** 。

```azurecli-interactive
az login
```

使用 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 命令选择帐户下的特定订阅。 记下**az login**输出中的**ID**值，以用作命令中的 "**订阅**参数" 的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>使用 CLI 创建 Azure Database for MySQL 灵活的服务器
你可以使用 `az mysql flexible-server` 命令创建具有 *私有访问权限的灵活服务器 (VNet 集成) *。 此命令使用 (VNet 集成) 的私有访问权限作为默认连接方法。 如果未提供任何虚拟网络和子网，则会为你创建一个。 你还可以使用子网 ID 提供已有的虚拟网络和子网。 <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> 使用 CLI 创建灵活的服务器有多种选项，如以下示例中所示。

>[!Important]
> 使用此命令会将子网委托给 **DBforMySQL/flexibleServers**。 该委派意味着只有 Azure Database for MySQL 灵活服务器才能使用该子网。 不能在委派子网中使用其他 Azure 资源类型。
>

请参阅 Azure CLI [参考文档](/cli/azure/mysql/flexible-server) ，了解可配置 CLI 参数的完整列表。 例如，在下面的命令中，可以选择指定资源组。

- 使用默认的虚拟网络、具有默认地址前缀的子网创建灵活的服务器
    ```azurecli-interactive
    az mysql flexible-server create
    ```
<!--- Create a flexible server using already existing virtual network and subnet
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```-->
- 使用现有虚拟网络、子网和子网 ID 创建灵活的服务器。 提供的子网不应部署任何其他资源，并且此子网将委托给 **DBforMySQL/flexibleServers**（如果尚未委托）。
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > 虚拟网络和子网应与灵活服务器位于同一区域和订阅中。
<!--
- Create a flexible server using new virtual network, subnet with non-default address prefix
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --vnet-address-prefix 10.0.0.0/24 --subnet mySubnet --subnet-address-prefix 10.0.0.0/24
    ```-->
请参阅 Azure CLI [参考文档](/cli/azure/mysql/flexible-server) ，了解可配置 CLI 参数的完整列表。


## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for MySQL 灵活的服务器中的网络](./concepts-networking.md)。
- [使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-portal.md)。
- 了解 [Azure Database for MySQL 灵活的服务器虚拟网络](./concepts-networking.md#private-access-vnet-integration)的详细信息。
