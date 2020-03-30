---
title: 在虚拟网络中对 Azure 数据资源管理器群集的访问、引入和操作进行故障排除
description: 在虚拟网络中对 Azure 数据资源管理器群集的连接、引入、群集创建和操作进行故障排除
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241655"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>在虚拟网络中对 Azure 数据资源管理器群集的访问、引入和操作进行故障排除

在本节中，您将了解如何解决部署到[虚拟网络](/azure/virtual-network/virtual-networks-overview)中的群集的连接、操作和群集创建问题。

## <a name="access-issues"></a>访问问题

如果在使用公共 （cluster.region.kusto.windows.net） 或专用 （private-cluster.region.kusto.windows.net） 终结点访问群集时出现问题，并且怀疑该终结点与虚拟网络设置相关，则执行以下步骤，排除故障。

### <a name="check-tcp-connectivity"></a>检查 TCP 连接

第一步包括使用 Windows 或 Linux 操作系统检查 TCP 连接。

# <a name="windows"></a>[Windows](#tab/windows)

   1. 将[TCping](https://www.elifulkerson.com/projects/tcping.php)下载到连接到群集的计算机。
   2. 使用以下命令从源计算机 ping 目标：

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. 在连接到群集的机器中安装*网猫*

    ```bash
    $ apt-get install netcat
     ```

   2. 使用以下命令从源计算机 ping 目标：

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

如果测试不成功，请执行以下步骤。 如果测试成功，则问题不是由于 TCP 连接问题。 转到[操作问题](#cluster-creation-and-operations-issues)以进一步排除故障。

### <a name="check-the-network-security-group-nsg"></a>检查网络安全组 （NSG）

   检查连接到群集子网的[网络安全组](/azure/virtual-network/security-overview)（NSG） 具有允许从客户端计算机的 IP 访问端口 443 的入站规则。

### <a name="check-route-table"></a>检查路由表

   如果群集的子网具有强制隧道设置到防火墙（包含默认路由"0.0.0.0/0"[的路由表](/azure/virtual-network/virtual-networks-udr-overview)的子网），请确保计算机 IP 地址具有[下一个跃点类型](/azure/virtual-network/virtual-networks-udr-overview)到 VirtualNetwork/Internet 的路由。 此路由是防止非对称路由问题所必需的。

## <a name="ingestion-issues"></a>摄入问题

如果您遇到引入问题，并且怀疑它与虚拟网络设置有关，则执行以下步骤。

### <a name="check-ingestion-health"></a>检查摄入健康

检查[群集引入指标](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics)指示正常状态。

### <a name="check-security-rules-on-data-source-resources"></a>检查数据源资源的安全规则

如果指标指示未从数据源处理任何事件（事件/IoT 中心*的事件处理*指标），请确保数据源资源（事件中心或存储）允许在防火墙规则或服务终结点中访问群集的子网。

### <a name="check-security-rules-configured-on-clusters-subnet"></a>检查在群集子网上配置的安全规则

确保群集的子网已正确配置了 NSG、UDR 和防火墙规则。 此外，测试所有从属终结点的网络连接。 

## <a name="cluster-creation-and-operations-issues"></a>群集创建和操作问题

如果您遇到群集创建或操作问题，并且怀疑它与虚拟网络设置有关，请按照以下步骤解决问题。

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>使用 REST API 诊断虚拟网络

[ARMClient](https://chocolatey.org/packages/ARMClient)用于使用 PowerShell 调用 REST API。 

1. 使用 ARMClient 登录

   ```powerShell
   armclient login
   ```

1. 调用诊断操作

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. 检查响应

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. 等待操作完成

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   等待*状态*属性显示 *"已完成"，* 然后*属性*字段应显示：

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

如果 *"结果"* 属性显示空结果，则表示所有网络测试都通过，并且没有中断任何连接。 如果显示以下错误，*则出站依赖项"[依赖项名称]：[端口]"可能未满足（出站），* 群集无法访问从属服务终结点。 继续以下步骤。

### <a name="check-network-security-group-nsg"></a>检查网络安全组 （NSG）

确保网络安全[组](/azure/virtual-network/security-overview)根据[VNet 部署依赖项](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)中的说明正确配置

### <a name="check-route-table"></a>检查路由表

如果群集的子网具有强制隧道设置为防火墙（包含默认路由"0.0.0.0/0"[路由表](/azure/virtual-network/virtual-networks-udr-overview)的子网）请确保[管理 IP 地址](vnet-deployment.md#azure-data-explorer-management-ip-addresses)）和[运行状况监视 IP 地址](vnet-deployment.md#health-monitoring-addresses)具有[具有下一跃点类型的](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*的路由，并且[源地址前缀](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route)为 *"管理-ip/32"* 和 *"运行状况监视-ip/32"。* 此路由需要防止非对称路由问题。

### <a name="check-firewall-rules"></a>检查防火墙规则

如果强制隧道子网出站流量到防火墙，请确保所有依赖项 FQDN（例如 *.blob.core.windows.net*）都允许在防火墙配置中，如[使用防火墙保护出站流量](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)时所述。
