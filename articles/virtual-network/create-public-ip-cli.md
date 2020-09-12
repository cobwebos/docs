---
title: 创建公共 IP-Azure CLI
description: 了解如何使用 Azure CLI 创建公共 IP
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 4d0934dd30f9738e2a67d4aff23c96a48aaaa61b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89301667"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>快速入门：使用 Azure CLI 创建公共 IP 地址

本文介绍如何使用 Azure CLI 创建公共 IP 地址资源。 有关此可以关联到哪些资源的详细信息，基本 SKU 和标准 SKU 之间的差异，以及其他相关信息，请参阅 [公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)。  对于此示例，我们将仅重点介绍 IPv4 地址;有关 IPv6 地址的详细信息，请参阅 [适用于 Azure VNet 的 IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)。

## <a name="prerequisites"></a>先决条件

- 本地安装的 Azure CLI 或 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果选择在本地安装并使用 CLI，本快速入门要求 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

在**eastus2**位置，使用[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)创建**myResourceGroup**资源组。

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**标准 SKU-使用区域**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>以下命令适用于 API 版本2020-08-01 或更高版本。  有关当前正在使用的 API 版本的详细信息，请参阅 [资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)。

使用[az network 公共 ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)在**myResourceGroup**中创建名为**myStandardZRPublicIP**的标准区域冗余公共 ip 地址。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> 对于早于2020-08-01 的 API 版本，请在上面运行命令，而无需指定区域参数来创建区域冗余的 IP 地址。 
>

若要在**myResourceGroup**中区域2名为**myStandardZonalPublicIP**的中创建标准区域性公共 IP 地址，请使用以下命令：

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

请注意，上述区域选项仅适用于具有 [可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)的区域中的有效选择。

# <a name="standard-sku---no-zones"></a>[**标准 SKU-无区域**](#tab/option-create-public-ip-standard)

>[!NOTE]
>以下命令适用于 API 版本2020-08-01 或更高版本。  有关当前正在使用的 API 版本的详细信息，请参阅 [资源提供程序和类型](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)。

使用[az network 公共 ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)在**myResourceGroup**中创建一个名为**myStandardPublicIP**的非区域性资源的标准公共 ip 地址。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
此选择在所有区域中有效，并且是区域中标准公共 IP 地址的默认选择，不 [可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

使用[az network 公共 ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)在**myResourceGroup**中创建名为**MYBASICPUBLICIP**的基本静态公共 ip 地址。  基本公共 Ip 没有可用性区域的概念。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard
    --allocation-method Static
```
如果 IP 地址在一段时间内可以接受，则可以通过将分配方法更改为 "动态" 来选择 **动态** ip 分配。

---

## <a name="additional-information"></a>其他信息 

有关上面列出的各个变量的详细信息，请参阅 [管理公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- 将 [公共 IP 地址与虚拟机](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)关联。
- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。