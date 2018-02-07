---
title: "使用 Azure CLI 通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准 | Microsoft Docs"
description: "了解如何使用 Azure CLI 通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 180cf891611f148a1b9acd112fbcddab8eb54de3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>使用 Azure CLI 通过区域冗余的前端创建公共负载均衡器标准

本文指导逐步通过区域冗余前端使用公共 IP 标准地址创建公共[负载均衡器标准](https://aka.ms/azureloadbalancerstandard)。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>注册可用性区域、负载均衡器标准和公共 IP 标准预览

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.17 或更高版本。  若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

>[!NOTE]
[负载均衡器标准 SKU](https://aka.ms/azureloadbalancerstandard) 目前处于预览状态。 在预览期，该功能的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 请将正式版[负载均衡器基本 SKU](load-balancer-overview.md) 用于生产服务。 

> [!NOTE]
> 可用性区域处于预览状态，已准备好在开发和测试方案中使用。 可为选择 Azure 资源、区域和 VM 大小系列提供支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。  

为负载均衡器的前端公共 IP 地址选择区域或区域冗余选项之前，必须先完成[注册可用性区域预览版](https://docs.microsoft.com/azure/availability-zones/az-overview)中的步骤。

确保已安装了最新的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 并已使用 [az login](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest#az_login) 登录到 Azure 帐户。

## <a name="create-a-resource-group"></a>创建资源组

使用以下命令创建资源组：

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>创建公共 IP 地址

使用以下命令创建公共 IP 标准：

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>创建负载均衡器

使用以下命令创建公共负载均衡器标准（包含上一步中创建的标准公共 IP）：

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>在端口 80 上创建 LB 探测

使用以下命令创建负载均衡器运行状况探测：

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>为端口 80 创建 LB 规则

使用以下命令创建负载均衡器规则：

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>后续步骤
- 了解如何[在可用性区域中创建公共 IP](../virtual-network/create-public-ip-availability-zone-cli.md)



