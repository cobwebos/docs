---
title: "为群集创建 Azure 负载均衡器规则"
description: "配置 Azure 负载均衡器，为 Azure Service Fabric 群集打开端口。"
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/22/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 8496bd61d0133a428ce8e522faef5b538f19d4fc
ms.contentlocale: zh-cn
ms.lasthandoff: 08/25/2017

---

# <a name="open-ports-for-a-service-fabric-cluster"></a>为 Service Fabric 群集打开端口

使用 Azure Service Fabric 群集部署的负载均衡器将流量定向到在节点上运行的应用。 如果将应用更改为使用另一端口，必须在 Azure 负载均衡器中公开该端口（或路由另一端口）。

将 Service Fabric 群集部署到 Azure 后，系统会自动创建负载均衡器。 如果没有负载均衡器，请参阅[配置面向 Internet 的负载均衡器](..\load-balancer\load-balancer-get-started-internet-portal.md)。

## <a name="configure-service-fabric"></a>配置 Service Fabric

Service Fabric 应用程序 ServiceManifest.xml 配置文件定义应用程序应使用的终结点。 更新配置文件以定义终结点后，必须更新负载均衡器，公开该（或其他）端口。 有关如何创建 Service Fabric 终结点的详细信息，请参阅[设置终结点](service-fabric-service-manifest-resources.md)。

## <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则可打开面向 Internet 的端口，并将流量转发至应用程序所用的内部节点的端口。 如果没有负载均衡器，请参阅[配置面向 Internet 的负载均衡器](..\load-balancer\load-balancer-get-started-internet-portal.md)。

要创建负载均衡器规则，需要收集以下信息：

- 负载均衡器名称。
- 负载均衡器和 Service Fabric 群集的资源组。
- 外部端口。
- 内部端口。

## <a name="azure-cli"></a>Azure CLI
>[!NOTE]
>如果需要确定负载均衡器的名称，可使用此命令来快速获取所有负载均衡器和关联资源组的列表。
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>

使用单个命令即可使用 Azure CLI 创建负载均衡器规则。 你只需知道用于创建新规则的负载均衡器和资源组的名称。

```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Azure CLI 命令具有下表中所述的几个参数：

| 参数 | 说明 |
| --------- | ----------- |
| `--backend-port`  | Service Fabric 应用程序正在侦听的端口。 |
| `--frontend-port` | 负载均衡器针对外部连接公开的端口。 |
| `-lb-name` | 要更改的负载均衡器的名称。 |
| `-g`       | 同时包含负载均衡器与 Service Fabric 群集的资源组。 |
| `-n`       | 选定的规则名称。 |


>[!NOTE]
>有关如何使用 Azure CLI 创建负载均衡器的详细信息，请参阅[使用 Azure CLI 创建负载均衡器](..\load-balancer\load-balancer-get-started-internet-arm-cli.md)。

## <a name="powershell"></a>PowerShell

>[!NOTE]
>如果需要确定负载均衡器的名称，可使用此命令来快速获取所有负载均衡器和关联资源组的列表。
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

PowerShell 比 Azure CLI 稍显复杂。 从概念上讲，可执行以下步骤创建规则。

1. 从 Azure 中获取负载均衡器。
2. 创建规则。
3. 将规则添加到负载均衡器。
4. 更新负载均衡器。

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

对于 `New-AzureRmLoadBalancerRuleConfig` 命令，`-FrontendPort` 表示负载均衡器针对外部连接公开的端口，而 `-BackendPort` 表示 Service Fabric 应用正在侦听的端口。

>[!NOTE]
>有关如何使用 PowerShell 创建负载均衡器的详细信息，请参阅[使用 PowerShell 创建负载均衡器](..\load-balancer\load-balancer-get-started-internet-arm-ps.md)。


