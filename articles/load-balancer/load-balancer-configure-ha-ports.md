---
title: 为 Azure 负载均衡器配置高可用性端口 | Microsoft Docs
description: 了解如何使用高可用性端口对所有端口上的内部流量进行负载均衡
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/20178
ms.author: kumud
ms.openlocfilehash: 7f7f8e254e0ed0556446e7b08eaf46ec59977f62
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>为内部负载均衡器配置高可用性端口

本文提供了在内部负载均衡器上部署高可用性端口的示例。 若要深入了解有关特定于网络虚拟设备 (NVA) 的配置信息，请参阅相应的提供程序网站。

>[!NOTE]
>Azure 负载均衡器支持两种不同的类型：“基本”和“标准”。 本文介绍标准负载均衡器。 有关基本负载均衡器的详细信息，请参阅[负载均衡器概述](load-balancer-overview.md)。

该图演示了本文中所述部署示例的以下配置：

- NVA 部署在内部负载均衡器（高可用性端口配置后）的后端池中。 
- 应用于 DMZ 子网的用户定义路由 (UDR) 将下一跃点设为内部负载均衡器虚拟 IP，从而将所有流量路由到 NVA。 
- 内部负载均衡器根据负载均衡器算法将流量分配到某一活动 NVA。
- NVA 处理流量并将其转发到后端子网中的原始目标。
- 如果在后端子网中配置了相应 UDR，则返回路径可采用相同的路由。 

![高可用性端口示例部署](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>预览版注册

若要预览标准负载均衡器中高可用性端口功能，请使用 Azure CLI 2.0 或 PowerShell 注册订阅，获取访问权限。 注册[标准负载均衡器预览版](https://aka.ms/lbpreview#preview-sign-up)的订阅。

>[!NOTE]
>注册标准负载均衡器预览版最长可能需要一小时。

## <a name="configure-high-availability-ports"></a>配置高可用性端口

若要配置高可用性端口，请在后端池中使用 NVA 设置内部负载均衡器。 设置相应的负载均衡器运行状况探测配置，以便使用高可用性端口检测 NVA 运行状况和负载均衡器规则。 [入门](load-balancer-get-started-ilb-arm-portal.md)中介绍了常规的负载均衡器相关配置。 本文重点介绍高可用性端口配置。

该配置实质上包括将前端端口和后端端口值设置为“0”。 将协议值设置为“All”。 本文介绍如何使用 Azure 门户、PowerShell 和 Azure CLI 2.0 配置高可用性端口。

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>使用 Azure 门户配置高可用性端口负载均衡器规则

若要使用 Azure 门户配置高可用性端口，请选中“HA 端口”复选框。 选中时，会自动填充相关端口和协议配置。 

![通过 Azure 门户配置高可用性端口](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>通过资源管理器模板配置高可用性端口负载均衡规则

可以使用 2017-08-01 API 版本为负载均衡器资源中的 Microsoft.Network/loadBalancers 配置高可用性端口。 以下 JSON 代码片段演示通过 REST API 配置的高可用性端口的负载均衡器配置中的更改：

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>使用 PowerShell 配置高可用性端口负载均衡器规则

使用以下命令，在使用 PowerShell 创建内部负载均衡器时，创建高可用性端口负载均衡器规则：

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>使用 Azure CLI 2.0 配置高可用性端口负载均衡器规则

在[创建内部负载均衡器集](load-balancer-get-started-ilb-arm-cli.md)的步骤 4 中，使用以下命令创建高可用性端口负载均衡器规则：

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>后续步骤

深入了解[高可用性端口](load-balancer-ha-ports-overview.md)。
