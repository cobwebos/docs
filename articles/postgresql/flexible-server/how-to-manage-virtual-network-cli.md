---
title: 管理虚拟网络-Azure CLI Azure Database for PostgreSQL-灵活的服务器
description: 使用 Azure CLI 为 Azure Database for PostgreSQL 灵活的服务器创建和管理虚拟网络
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 727eb4cd7e7c3de090e1573cb5358ef23118385e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933332"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>使用 Azure CLI 为 Azure Database for PostgreSQL 灵活的服务器创建和管理虚拟网络

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 灵活的服务器支持两种类型的相互排斥的网络连接方法来连接到你的灵活服务器。 这两个选项如下：

* 公共访问（允许的 IP 地址）
* 专用访问（VNet 集成）

在本文中，我们将重点介绍如何使用 Azure CLI ** (VNet 集成) ** 创建 PostgreSQL 服务器。 通过 *私有访问 (VNet 集成) *，你可以将灵活的服务器部署到你自己的 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)中。 Azure 虚拟网络提供专用的安全网络通信。 在专用访问中，与 PostgreSQL 服务器的连接仅限于虚拟网络中的。 若要了解详细信息，请参阅 [ (VNet 集成) 的私有访问权限 ](./concepts-networking.md#private-access-vnet-integration)。

在 Azure Database for PostgreSQL 灵活的服务器中，你只能在创建服务器的过程中将服务器部署到虚拟网络和子网。 将灵活的服务器部署到虚拟网络和子网后，不能将其移动到另一个虚拟网络、子网或 *公共访问 (允许的 IP 地址) *。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以在单独的浏览器标签页中通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 打开 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后选择 Enter 来运行它。  

如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>先决条件

需要使用 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令登录账户。 请注意 **ID** 属性，它是指 Azure 帐户的 **订阅 id** 。

```azurecli-interactive
az login
```

使用 [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) 命令选择帐户下的特定订阅。 记下**az login**输出中的**ID**值，以用作命令中的 "**订阅**参数" 的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>使用 CLI 创建 Azure Database for PostgreSQL 灵活的服务器
你可以使用 `az postgres flexible-server` 命令创建具有 *私有访问权限的灵活服务器 (VNet 集成) *。 此命令使用 (VNet 集成) 的私有访问权限作为默认连接方法。 如果未提供任何虚拟网络和子网，则会为你创建一个。 你还可以使用子网 id 提供已有的虚拟网络和子网。 <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> 使用 CLI 创建灵活的服务器有多种选项，如以下示例中所示。

>[!Important]
> 使用此命令会将子网委托给 **DBforPostgreSQL/flexibleServers**。 这种委托意味着只有 Azure Database for PostgreSQL 灵活的服务器才能使用该子网。 不能在委派子网中使用其他 Azure 资源类型。
>
请参阅 Azure CLI 参考文档 <!--FIXME --> 以获取可配置 CLI 参数的完整列表。 例如，在下面的命令中，可以选择指定资源组。

- 使用默认的虚拟网络、具有默认地址前缀的子网创建灵活的服务器
    ```azurecli-interactive
    az postgres flexible-server create
    ```
<!--- Create a flexible server using already existing virtual network and subnet
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```-->
- 使用现有虚拟网络、子网和子网 ID 创建灵活的服务器。 提供的子网不应部署任何其他资源，并且此子网将委托给 **DBforPostgreSQL/flexibleServers**（如果尚未委托）。
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > 虚拟网络和子网应与灵活服务器位于同一区域和订阅中。
<!--  
- Create a flexible server using new virtual network, subnet with non-default address prefix
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --vnet-address-prefix 10.0.0.0/24 --subnet mySubnet --subnet-address-prefix 10.0.0.0/24
    ```-->
请参阅 Azure CLI 参考文档 <!--FIXME --> 以获取可配置 CLI 参数的完整列表。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for PostgreSQL-灵活服务器的网络](./concepts-networking.md)。
- [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 灵活的服务器虚拟网络](./how-to-manage-virtual-network-portal.md)。
- 了解 [Azure Database for PostgreSQL 灵活的服务器虚拟网络的](./concepts-networking.md#private-access-vnet-integration)详细信息。