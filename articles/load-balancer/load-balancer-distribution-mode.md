---
title: 配置 Azure 负载均衡器分发模式
titleSuffix: Azure Load Balancer
description: In this article, get started configuring the distribution mode for Azure Load Balancer to support source IP affinity.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: ddccd02e7157792d942309ae4f74933322f246f9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225375"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>配置 Azure 负载均衡器的分配模式

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>基于哈希的分发模式

The default distribution mode for Azure Load Balancer is a five-tuple hash. 

The tuple is composed of the:
* **Source IP**
* **Source port**
* **Destination IP**
* **Destination port**
* **Protocol type**

The hash is used to map traffic to the available servers. The algorithm provides stickiness only within a transport session. Packets that are in the same session are directed to the same datacenter IP behind the load-balanced endpoint. When the client starts a new session from the same source IP, the source port changes and causes the traffic to go to a different datacenter endpoint.

![Five-tuple hash-based distribution mode](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>源 IP 关联模式

The load balancer can also be configured by using the source IP affinity distribution mode. 此分配模式也称为为会话关联或客户端 IP 关联。 The mode uses a two-tuple (source IP and destination IP) or three-tuple (source IP, destination IP, and protocol type) hash to map traffic to the available servers. By using source IP affinity, connections that are started from the same client computer go to the same datacenter endpoint.

The following figure illustrates a two-tuple configuration. Notice how the two-tuple runs through the load balancer to virtual machine 1 (VM1). VM1 随后由 VM2 和 VM3 备份。

![Two-tuple session affinity distribution mode](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

源 IP 关联模式解决了 Azure 负载均衡器与远程桌面网关（RD 网关）之间的不兼容问题。 使用此模式可在单个云服务中生成 RD 网关场。

另一个用例方案是媒体上传。 数据上传通过 UDP 进行，但控制平面通过 TCP 实现：

* A client starts a TCP session to the load-balanced public address and is directed to a specific DIP. 通道将保持活动状态以监视连接运行状况。
* A new UDP session from the same client computer is started to the same load-balanced public endpoint. 连接像前面的 TCP 连接一样定向到同一个 DIP 终结点。 能够以较高的吞吐量执行媒体上传，同时通过 TCP 维护控制通道。

> [!NOTE]
> 如果通过删除或添加虚拟机来更改负载均衡集，则会重新计算客户端请求的分配。 无法确保现有客户端的新连接最终都会抵达同一台服务器。 此外，使用源 IP 关联分配模式可能导致流量的不均衡分配。 在代理后面运行的客户端可被视为唯一的客户端应用程序。

## <a name="configure-source-ip-affinity-settings"></a>配置源 IP 关联设置

### <a name="azure-portal"></a>Azure 门户

You can change the configuration of the distribution mode by modifying the load-balancing rule in the portal.

1. Sign in to the Azure portal and locate the Resource Group containing the load balancer you wish to change by clicking on **Resource Groups**.
2. In the load balancer overview screen, click on **Load-balancing rules** under **Settings**.
3. In the load-balancing rules screen, click on the load-balancing rule that you wish to change the distribution mode.
4. Under the rule, the distribution mode is changed by changing the **Session persistence** drop down box.  提供了以下选项：
    
    * **None (hash-based)** - Specifies that successive requests from the same client may be handled by any virtual machine.
    * **Client IP (source IP affinity 2-tuple)** - Specifies that successive requests from the same client IP address will be handled by the same virtual machine.
    * **Client IP and protocol (source IP affinity 3-tuple)** - Specifies that successive requests from the same client IP address and protocol combination will be handled by the same virtual machine.

5. Choose the distribution mode and then click **Save**.

### <a name="azure-powershell"></a>Azure PowerShell

For virtual machines deployed with Resource Manager, use PowerShell to change the load-balancer distribution settings on an existing load-balancing rule. The following command updates the distribution mode: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

对于经典虚拟机，请使用 Azure PowerShell 更改分发设置。 将 Azure 终结点添加到虚拟机并配置负载均衡器分配模式：

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Set the value of the `LoadBalancerDistribution` element for the amount of load balancing required. Specify sourceIP for two-tuple (source IP and destination IP) load balancing. Specify sourceIPProtocol for three-tuple (source IP, destination IP, and protocol type) load balancing. Specify none for the default behavior of five-tuple load balancing.

使用以下设置检索终结点负载均衡器分配模式配置：

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

When the `LoadBalancerDistribution` element isn't present, Azure Load Balancer uses the default five-tuple algorithm.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>在负载均衡终结点集上配置分配模式

如果终结点是负载均衡终结点集的一部分，则必须在负载均衡终结点集上配置分配模式：

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>配置云服务终结点的分配模式

使用用于 .NET 的 Azure SDK 2.5 更新云服务。 在 .csdef 中指定云服务的终结点设置。 若要更新云服务部署的负载均衡器分配模式，需要进行部署升级。

下面是终结点设置的 .csdef 更改的示例：

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API 示例

以下示例演示如何针对部署中的指定负载均衡集重新配置负载均衡器分配模式。 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>更改已部署的负载均衡集的分配模式

使用 Azure 经典部署模型更改现有的部署配置。 添加 `x-ms-version` 标头，并将值设置为版本 2014-09-01 或更高。

#### <a name="request"></a>请求

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

As previously described, set the `LoadBalancerDistribution` element to sourceIP for two-tuple affinity, sourceIPProtocol for three-tuple affinity, or none for no affinity (five-tuple affinity).

#### <a name="response"></a>响应

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>后续步骤

* [Azure 内部负载均衡器概述](load-balancer-internal-overview.md)
* [配置面向 Internet 的负载均衡器入门](load-balancer-get-started-internet-arm-ps.md)
* [配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)
