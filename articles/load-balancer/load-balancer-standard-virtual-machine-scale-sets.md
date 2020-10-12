---
title: Azure 标准负载均衡器和虚拟机规模集
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: 通过此学习路径完成 Azure 标准负载均衡器和虚拟机规模集的入门。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 41f1d6c18eab35bd1a41d4cfa98d0cbda69b35ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650326"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure 负载均衡器和 Azure 虚拟机规模集

使用虚拟机规模集和负载均衡器时，应考虑以下准则：

## <a name="port-forwarding-and-inbound-nat-rules"></a>端口转发和入站 NAT 规则：
  * 创建规模集后，无法为负载均衡器的运行状况探测所用的负载均衡规则修改后端端口。 为了更改端口，可以通过更新 Azure 虚拟机规模集来删除运行状况探测，更新端口，然后重新配置运行状况探测。
  * 在负载均衡器的后端池中使用虚拟机规模集时，会自动创建默认的入站 NAT 规则。
## <a name="inbound-nat-pool"></a>入站 NAT 池：
  * 每个虚拟机规模集必须有至少一个入站 NAT 池。 
  * 入站 NAT 池是入站 NAT 规则的集合。 一个入站 NAT 池不能支持多个虚拟机规模集。
  * 若要从现有虚拟机规模集中删除 NAT 池，需要先从规模集中删除 NAT 池。 下面显示了一个使用 CLI 的完整示例：
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>负载均衡规则：
  * 在负载均衡器的后端池中使用虚拟机规模集时，会自动创建默认的负载均衡规则。
## <a name="outbound-rules"></a>出站规则：
  *  若要为已被负载均衡规则引用的后端池创建出站规则，需要先在创建入站负载均衡规则时在门户中将“创建隐式出站规则”标记为“否”。 

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="创建负载均衡规则" border="true":::

可以使用以下方法部署一个包含现有 Azure 负载均衡器的虚拟机规模集。

* [使用 Azure 门户配置包含现有 Azure 负载均衡器的虚拟机规模集](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal)。
* [使用 Azure PowerShell 配置包含现有 Azure 负载均衡器的虚拟机规模集](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell)。
* [使用 Azure CLI 配置包含现有 Azure 负载均衡器的虚拟机规模集](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli)。
