---
title: 使用 Azure CLI 的 Azure 流量管理器子网覆盖 |Microsoft Docs
description: 本文帮助你了解如何使用流量管理器子网替代来替代流量管理器配置文件的路由方法，以便通过预定义的 IP 范围到终结点的映射，基于最终用户 IP 地址将流量定向到某个终结点。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: c43b85f3c4b8d1b750e9f97e0be812bee683c702
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600783"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>使用 Azure CLI 的流量管理器子网替代

使用流量管理器子网替代可以更改配置文件的路由方法。  添加替代后，会使用预定义的 IP 范围到终结点的映射，基于最终用户的 IP 地址来定向流量。 

## <a name="how-subnet-override-works"></a>子网替代的工作原理

将子网替代添加到流量管理器配置文件时，流量管理器将首先检查是否存在用于最终用户的 IP 地址的子网替代。 如果找到一个，则将用户的 DNS 查询定向到相应的终结点。  如果找不到映射，流量管理器将回退到配置文件的原始路由方法。 

可将 IP 地址范围指定为 CIDR 范围（例如 1.2.3.0/24）或地址范围（例如 1.2.3.4-5.6.7.8）。 与每个终结点关联的 IP 范围对于该终结点必须是唯一的。 不同终结点之间的 IP 范围出现任何重叠会导致流量管理器拒绝配置文件。

有两种类型的路由配置文件支持子网替代：

* **地理** - 如果流量管理器找到了 DNS 查询的 IP 地址的子网替代，它会将该查询路由到终结点，而不管该终结点的运行状况如何。
* **性能** - 如果流量管理器找到了 DNS 查询的 IP 地址的子网替代，它只会将流量路由到正常的终结点。  如果子网替代终结点不正常，流量管理器将回退到性能路由试探法。

## <a name="create-a-traffic-manager-subnet-override"></a>创建流量管理器子网替代

若要创建流量管理器子网替代，可以使用 Azure CLI 将替代子网添加到流量管理器终结点。

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.28 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>使用子网替代更新流量管理器终结点。
使用 Azure CLI 通过 [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) 更新终结点。

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

可以在运行 [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) 时使用 **--remove** 选项，以便删除 IP 地址范围。

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>后续步骤

详细了解流量管理器[流量路由方法](traffic-manager-routing-methods.md)。

了解[子网流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
