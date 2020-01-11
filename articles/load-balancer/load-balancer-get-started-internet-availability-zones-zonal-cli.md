---
title: 创建具有区域性前端 Azure CLI 的负载均衡器
titleSuffix: Azure Load Balancer
description: 了解如何使用 Azure CLI 创建带有区域性前端的标准负载均衡器
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 09c9aa2807fbf8dc862bc1bf91c9e9bc97c49122
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894587"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>使用 Azure CLI 创建具有区域性前端的标准负载均衡器

本文分步指导你创建具有区域性前端的公用[标准负载均衡器](https://aka.ms/azureloadbalancerstandard)。 具有区域性前端意味着，任何入站或出站流都将由某个地区中的单个区域提供服务。 可以通过在负载均衡器的前端配置中使用区域性的标准公共 IP 地址来创建具有区域性前端的负载均衡器。 若要了解可用性区域如何与标准负载均衡器配合工作，请参阅[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 CLI，请确保已安装了最新的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 并已使用 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) 登录到 Azure 帐户。

> [!NOTE]
> 选择 Azure 资源、区域和 VM 大小系列时可使用可用性区域支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。 


## <a name="create-a-resource-group"></a>创建资源组

使用以下命令创建资源组：

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>创建公共标准 IP 地址

使用以下命令创建区域性标准公共 IP 地址：

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>创建负载均衡器

使用以下命令通过上一步中创建的标准公共 IP 创建公共标准负载均衡器：

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>在端口 80 上创建 LB 探测

使用以下命令创建负载均衡器运行状况探测：

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>为端口 80 创建 LB 规则

使用以下命令创建负载均衡器规则：

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>后续步骤
- 详细了解[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。



