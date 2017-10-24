---
title: "Azure 中的高可用性端口概述 | Microsoft Docs"
description: "了解在内部负载均衡器上进行负载均衡的高可用性端口"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 3e54cb45cf002a183a5b0bd9b3082a235cd825f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-ports-overview-preview"></a>高可用性端口概述（预览）

Azure 负载均衡器的标准 SKU 引入了高可用性 (HA) 端口功能 - 能够为所有支持的协议分配来自所有端口的流量。 配置内部负载均衡器时，用户可以配置 HA 端口规则（可将前端和后端端口设置为 **0**，将协议设置为 **all**），从而允许所有流量流经内部负载均衡器。

>[!NOTE]
> 负载均衡器标准版中提供高可用性端口功能，当前处于预览状态。 在预览期，该功能的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 请务必注册负载均衡器标准版（预览版），以便在负载均衡器标准版资源中使用 HA 端口。 请按照说明注册负载均衡器[标准版（预览版）](https://aka.ms/lbpreview#preview-sign-up)。

负载均衡算法仍保持不变，并基于五元组 <源 IP 地址、源端口、目标 IP 地址、目标端口、协议> 选择目标。 但此配置允许单个 LB 规则处理所有可用流量，并减少配置复杂性以及由可以添加的最大负载均衡规则数施加的任何限制。

HA 端口实现的关键方案之一是在 Azure 虚拟网络中进行网络虚拟设备的高可用性部署。 此外，HA 端口实现的另一个常见方案是对某一范围的端口进行负载均衡。 

## <a name="why-use-high-ha-ports"></a>为何使用高 HA 端口

Azure 客户主要依赖于网络虚拟设备 (NVA) 来保护其工作负荷免遭多种类型的安全威胁。 此外，NVA 必须可靠且高度可用。  

HA 端口无需采用更复杂的解决方案（如 zookeeper）从而减少了 NVA HA 方案的复杂性，并通过提供更快的故障转移和横向扩展选项提高了可靠性。 现在可以通过在 Azure 内部负载均衡器的后端池中添加 NVA，然后配置 HA 端口负载均衡器规则来实现 NVA HA。

以下示例显示了一个中心辐射型虚拟网络部署，其中轮辐在离开受信任的空间前，强制通过 NVA 使用隧道技术将流量传送到中心虚拟网络。 NVA 位于具有 HA 端口配置的内部负载均衡器后面，因此可以处理所有流量，并相应地转发。 

![ha 端口示例](./media/load-balancer-ha-ports-overview/nvaha.png)

图 1 - 包含以 HA 模式部署的 NVA 的中心辐射型虚拟网络


## <a name="region-availability"></a>上市区域

HA 端口当前在以下区域提供：
- 美国东部 2
- 美国中部
- 欧洲北部
- 美国中西部
- 欧洲西部
- 东南亚 

## <a name="preview-sign-up"></a>预览版注册

若要体验负载均衡器标准 SKU 中 HA 端口功能的预览版，请使用 PowerShell 或 Azure CLI 2.0 注册订阅，获取访问权限。

- 使用 PowerShell 注册

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- 使用 Azure CLI 2.0 注册

    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network 
    ```  


>[!NOTE]
>要使用此功能，除了注册 HA 端口外，还需注册负载均衡器[标准版（预览版）](https://aka.ms/lbpreview#preview-sign-up)。 注册 HA 端口或负载均衡器标准版（预览版）最长可能需要一小时。

## <a name="limitations"></a>限制

以下是 HA 端口支持的配置或异常：

- 单个前端 IP 配置可以有包含 HA 端口（所有端口）的单条 DSR 负载均衡器规则，也可以有包含 HA 端口（所有端口）的单条非 DSR 负载均衡器规则。 它不能同时具有这两者。
- 单个网络接口 IP 配置只能有包含 HA 端口的一条非 DSR 负载均衡器规则。 不能为此 ipconfig 配置任何其他规则。
- 单个网络接口 IP 配置可以有一条或多条包含 HA 端口的 DSR 负载均衡器规则，前提是其所有相应的前端 ip 配置都是唯一的。
- 如果所有负载均衡规则都包含 HA 端口（仅 DSR），或所有规则都包含非 HA 端口（DSR 和非 DSR），则两条（或更多）指向同一后端池的负载均衡器规则可以共存。 如果存在 HA 端口和非 HA 端口规则的组合，这两种 LB 规则不能共存。
- 启用了 IPv6 的租户上的 HA 端口不可用。


## <a name="next-steps"></a>后续步骤

[配置内部负载均衡器上的 HA 端口](load-balancer-configure-ha-ports.md)

