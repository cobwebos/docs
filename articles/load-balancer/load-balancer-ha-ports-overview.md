---
title: "Azure 中的高可用性端口概述 | Microsoft Docs"
description: "了解在内部负载均衡器上进行负载均衡的高可用性端口。"
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
ms.openlocfilehash: 7a77e6ecbf59944c62aa4ae014bf5b8a5a7f7f1f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="high-availability-ports-overview"></a>高可用性端口概述

使用内部负载均衡器时，Azure 负载均衡器标准版可帮助同时对所有端口上的 TCP 和 UDP 流进行负载均衡。 

>[!NOTE]
> 负载均衡器标准版提供高可用性 (HA) 端口功能，目前该功能处于预览状态。 在预览期间，该功能的可用性和可靠性级别可能与正式发布版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 注册负载均衡器标准版（预览版），以便在负载均衡器标准版资源中使用 HA 端口。 请按照说明注册负载均衡器[标准版（预览版）](https://aka.ms/lbpreview#preview-sign-up)。

HA 端口规则是在内部负载均衡器标准版中配置的负载均衡规则的变体。 可以通过提供单个规则对到达内部负载均衡器标准版的所有端口的所有 TCP 和 UDP 流进行负载均衡，来简化负载均衡器的使用。 按流进行负载均衡决策。 这基于以下五元组连接：“源 IP 地址”、“源端口”、“目标 IP 地址”、“目标端口”和“协议”。

HA 端口功能可帮助实现关键方案，如虚拟网络内部网络虚拟设备 (NVA) 的高可用性和缩放。 当大量端口必须进行负载均衡时，它也可以帮助完成。 

当将前端和后端端口设为 **0** 并将协议设为 **All** 时，即配置了 HA 端口功能。 然后，不管端口号是什么，内部负载均衡器资源都会均衡所有 TCP 和 UDP 流。

## <a name="why-use-ha-ports"></a>为何使用 HA 端口？

### <a name="nva"></a>网络虚拟设备

可以使用 NVA 来保护 Azure 工作负荷免受多种类型的安全威胁。 如果在这些方案中使用 NVA，这些设备必须可靠、高度可用且可根据需要横向扩展。

只需将 NVA 实例添加到 Azure 内部负载均衡器后端池，并配置 HA 端口负载均衡器规则，即可实现这些目标。

HA 端口为 NVA HA 方案提供几项优势：
- 根据实例运行状况探测快速故障转移到正常的实例
- 通过横向扩展到 *n* 个活动实例来提高性能
- *N*-主动和主动-被动方案
- 无需使用复杂解决方案，例如，使用 Apache ZooKeeper 节点来监视设备

下图显示了中心辐射型虚拟网络部署。 在离开受信任空间之前，辐射使用强制隧道将其流量发送到中心虚拟网络并通过 NVA。 NVA 在采用 HA 端口配置的内部负载均衡器标准版后面。 可以处理并相应地转发所有流量。

![包含以 HA 模式部署的 NVA 的中心辐射型虚拟网络的关系图](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> 如果使用 NVA，请咨询相应的提供商来了解如何最好地使用 HA 端口，以及支持哪些方案。

### <a name="load-balancing-large-numbers-of-ports"></a>对大量端口进行负载均衡

对于需要负载均衡大量端口的应用程序，也可以使用 HA 端口。 可以通过将内部[负载均衡器标准版](https://aka.ms/lbpreview)与 HA 端口配合使用来简化这些方案。 单个负载均衡规则可替换多个单独的负载均衡规则（每个端口一个）。

## <a name="region-availability"></a>上市区域

可在[负载均衡器标准版所在的同一区域](https://aka.ms/lbpreview#region-availability)中使用 HA 端口功能。  

## <a name="preview-sign-up"></a>预览版注册

若要体验负载均衡器标准版中 HA 端口功能的预览版，请注册订阅以获取访问权限。 可以使用 Azure CLI 2.0 或 PowerShell。

>[!NOTE]
>若要使用此功能，除了注册 HA 端口功能外，还需注册负载均衡器[标准版（预览版）](https://aka.ms/lbpreview#preview-sign-up)。 注册最多可能需要一小时。

### <a name="sign-up-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 注册

1. 在提供程序中注册此功能：
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. 上述操作最长可能需要 10 分钟才能完成。 可使用以下命令检查操作状态：

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    当功能注册状态返回“已注册”时，操作成功，如下所示：
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. 在资源提供程序中重新注册订阅，完成预览版注册：

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-by-using-powershell"></a>使用 PowerShell 注册

1. 在提供程序中注册此功能：
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. 上述操作最长可能需要 10 分钟才能完成。 可使用以下命令检查操作状态：

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    当功能注册状态返回“已注册”时，操作成功，如下所示：
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. 在资源提供程序中重新注册订阅，完成预览版注册：

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>限制

以下是 HA 端口功能支持的配置或异常：

- 单个前端 IP 配置可以有包含 HA 端口的单条 DSR 负载均衡器规则，也可以有包含 HA 端口的单条非 DSR 负载均衡器规则。 它不能同时具有这两者。
- 单个网络接口 IP 配置只能有一条包含 HA 端口的非 DSR 负载均衡器规则。 无法为此 ipconfig 配置任何其他规则。
- 单个网络接口 IP 配置可以有一条或多条包含 HA 端口的 DSR 负载均衡器规则，前提是其所有相应的前端 IP 配置都是唯一的。
- 如果所有负载均衡规则都包含 HA 端口（仅 DSR），则两条（或更多）指向同一后端池的负载均衡器规则可以共存。 这同样适用于所有规则都包含非 HA 端口（DSR 和非 DSR）的情况。 如果存在 HA 端口和非 HA 端口规则的组合，这两种负载均衡规则不能共存。
- HA 端口功能不适用于 IPv6。
- 仅单个 NIC 支持 NVA 方案的流对称性。 请参阅[网络虚拟设备](#nva)的说明和示意图。 



## <a name="next-steps"></a>后续步骤

- [在内部负载均衡器标准版中配置 HA 端口](load-balancer-configure-ha-ports.md)
- [了解负载均衡器标准预览版](https://aka.ms/lbpreview)

