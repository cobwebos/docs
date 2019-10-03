---
title: 使用 Azure CLI 的 Azure 流量管理器子网覆盖 |Microsoft Docs
description: 本文将帮助你了解如何使用流量管理器子网替代来替代流量管理器配置文件的路由方法，以根据最终用户 IP 地址将流量定向到基于最终用户 IP 地址的终结点。
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: d285db22ef92128e025a677ea7f86d623dfe130c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351178"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>使用 Azure CLI 的流量管理器子网覆盖

流量管理器子网替代允许更改配置文件的路由方法。  添加替代会根据最终用户的 IP 地址，使用预定义的 IP 范围将流量定向到终结点映射。 

## <a name="how-subnet-override-works"></a>子网替代的工作原理

将子网替代添加到流量管理器配置文件时，流量管理器将首先检查是否存在用于最终用户的 IP 地址的子网替代。 如果找到一个，则将用户的 DNS 查询定向到相应的终结点。  如果找不到映射，流量管理器将回退到配置文件的原始路由方法。 

可以将 IP 地址范围指定为 CIDR 范围（例如，1.2.3.0/24）或作为地址范围（例如，1.2.3.4-5.6.7.8）。 与每个终结点关联的 IP 范围对该终结点必须是唯一的。 不同终结点之间 IP 范围的任何重叠将导致流量管理器拒绝该配置文件。

支持子网替代的路由配置文件分为两种类型：

* **地理位置**-如果流量管理器找到 DNS 查询的 IP 地址的子网替代，它会将查询路由到终结点的运行状况。
* **性能**-如果流量管理器找到 DNS 查询的 IP 地址的子网替代，它将仅将流量路由到终结点（如果该终结点正常运行）。  如果子网替代终结点不正常，流量管理器将回退到性能路由试探法。

## <a name="create-a-traffic-manager-subnet-override"></a>创建流量管理器子网替代

若要创建流量管理器子网替代，可以使用 Azure CLI 将替代的子网添加到流量管理器终结点。

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.28 版或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>用子网替代更新流量管理器终结点。
使用 Azure CLI 通过[az network 流量管理器终结点更新](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)来更新终结点。

```azurecli

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

通过使用 **--remove**选项运行[az network 流量管理器终结点更新](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)，可以删除 IP 地址范围。

```azurecli

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