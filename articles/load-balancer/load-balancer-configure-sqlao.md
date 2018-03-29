---
title: 为 SQL Server AlwaysOn 配置负载均衡器 | Microsoft Docs
description: 介绍如何将负载均衡器配置为与 SQL Server AlwaysOn 配合工作，以及如何利用 PowerShell 为 SQL Server 实现创建负载均衡器
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: a0c2345b47b9103ac6a7ae998f13a12332e3907e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>为 SQL Server AlwaysOn 配置负载均衡器



SQL Server AlwaysOn 可用性组现在可与内部负载均衡器配合运行。 可用性组是 SQL Server 用于实现高可用性和灾难恢复的旗舰解决方案。 无论配置中的副本数目是多少，可用性组侦听程序都可让客户端应用程序无缝连接到主要副本。

侦听程序 (DNS) 名称会映射到负载均衡 IP 地址。 Azure 负载均衡器仅将传入流量定向到副本集中的主服务器。

可以使用 SQL Server AlwaysOn（侦听程序）终结点的内部负载均衡器支持。 现在，可以控制侦听程序的可访问性。 可以从虚拟网络的特定子网中选择负载均衡 IP 地址。

如果在侦听程序上使用内部负载均衡器，只有以下项可以访问 SQL Server 终结点（例如 Server=tcp:ListenerName,1433;Database=DatabaseName）：

* 同一虚拟网络中的服务和 VM。
* 来自连接的本地网络的服务和 VM。
* 来自互连的虚拟网络的服务和 VM。

![SQL Server AlwaysOn 的内部负载均衡器](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>将内部负载均衡器添加到服务

1. 在以下示例中，将配置包含名为“Subnet-1”的子网的虚拟网络：

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. 为每个 VM 上的内部负载均衡器添加负载均衡终结点。

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    在上述示例中，有两个名为“sqlsvc1”和“sqlsvc2”的 VM 在云服务“Sqlsvc”中运行。 使用 `DirectServerReturn` 开关创建内部负载均衡器后，可将负载均衡终结点添加到内部负载均衡器。 负载均衡终结点允许 SQL Server 为可用性组配置侦听程序。

有关 SQL Server AlwaysOn 的详细信息，请参阅[在 Azure 中为 AlwaysOn 可用性组配置内部负载均衡器](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)。

## <a name="see-also"></a>另请参阅
* [开始配置公共负载均衡器](load-balancer-get-started-internet-arm-ps.md)
* [开始配置内部负载均衡器](load-balancer-get-started-ilb-arm-ps.md)
* [配置负载均衡器分发模式](load-balancer-distribution-mode.md)
* [配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)
