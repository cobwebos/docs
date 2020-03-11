---
title: 什么是 Azure Private Link service？
description: 了解 Azure 专用链接服务。
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082959"
---
# <a name="what-is-azure-private-link-service"></a>什么是 Azure Private Link service？

Azure 专用链接服务是对你自己的、由 Azure 专用链接提供支持的服务的引用。 可对在[Azure 标准负载均衡器](../load-balancer/load-balancer-standard-overview.md)后面运行的服务启用专用链接访问，以便服务的使用者可以从其自己的 vnet 中私下访问该服务。 客户可在其 VNet 中创建专用终结点，并将其映射到此服务。 本文介绍与服务提供商端相关的概念。 

## <a name="workflow"></a>工作流

![专用链接服务工作流](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>创建专用链接服务

- 将应用程序配置为在虚拟网络中的标准负载均衡器后运行。 如果已将应用程序配置到标准负载均衡器之后，则可以跳过此步骤。   
- 创建引用上面的负载均衡器的专用链接服务。 在 "负载平衡器选择" 进程中，选择要接收流量的前端 IP 配置。 选择专用链接服务的 NAT IP 地址的子网。 建议至少在子网中提供8个 NAT IP 地址。 所有使用者流量都将从该专用 IP 地址池发送到服务提供商。 为专用链接服务选择适当的属性/设置。    

    > [!NOTE]
    > 仅标准负载均衡器支持 Azure 专用链接服务。 
    
### <a name="share-your-service"></a>共享服务

创建专用链接服务之后，Azure 将根据你为服务提供的名称，生成名为 "alias" 的全局唯一命名名字对象。 你可以与你的客户脱机共享你的服务的别名或资源 URI。 使用者可以使用别名或资源 URI 启动专用链接连接。
 
### <a name="manage-your-connection-requests"></a>管理连接请求

使用者启动连接后，服务提供商可以接受或拒绝连接请求。 所有连接请求都将列在专用链接服务上的**privateendpointconnections**属性下。
 
### <a name="delete-your-service"></a>删除服务

如果专用链接服务不再使用，则可以将其删除。 但是，在删除服务之前，请确保没有与之关联的专用终结点连接。 可以拒绝所有连接并删除服务。

## <a name="properties"></a>属性

专用链接服务指定以下属性： 

|properties |说明  |
|---------|---------|
|预配状态（provisioningState）  |一个只读属性，其中列出了私有链接服务的当前设置状态。 适用的预配状态为： "删除;因成功正在更新 "。 如果预配状态为 "成功"，则已成功设置专用链接服务。        |
|别名（别名）     | 别名是服务的全局唯一的只读字符串。 它可帮助你屏蔽服务的客户数据，同时为你的服务创建一个易于共享的名称。 创建专用链接服务时，Azure 将为你的服务生成可与客户共享的别名。 你的客户可以使用此别名请求连接到你的服务。          |
|可见性（可见性）     | 可见性是控制专用链接服务的公开设置的属性。 服务提供商可以选择使用基于角色的访问控制（RBAC）权限、一组受限的订阅或所有 Azure 订阅，将其服务的暴露程度限制为订阅。          |
|自动批准（自动批准）    |   自动批准控制对专用链接服务的自动访问。 当从这些订阅中的专用终结点请求连接时，自动批准列表中指定的订阅将自动获得批准。          |
|负载均衡器前端 IP 配置（loadBalancerFrontendIpConfigurations）    |    专用链接服务绑定到标准负载均衡器的前端 IP 地址。 目标为该服务的所有流量都将到达 SLB 的前端。 你可以配置 SLB 规则，以将此流量定向到你的应用程序在其中运行的适当后端池。 负载均衡器前端 IP 配置不同于 NAT IP 配置。      |
|NAT IP 配置（Ipconfiguration）    |    此属性是指专用链接服务的 NAT （网络地址转换） IP 配置。 可以从服务提供商的虚拟网络中的任何子网中选择 NAT IP。 专用链接服务对专用链路流量执行目标端 NAT 传输。 这可确保源（使用者端）和目标（服务提供商）地址空间之间没有 IP 冲突。 在目标端（服务提供商端），NAT IP 地址将显示为服务接收的所有数据包的源 IP，并显示服务发送的所有数据包的目标 IP。       |
|专用终结点连接（privateEndpointConnections）     |  此属性列出连接到专用链接服务的专用终结点。 多个专用终结点可以连接到同一专用链接服务，服务提供商可以控制单个专用终结点的状态。        |
|TCP 代理 V2 （EnableProxyProtocol）     |  此属性使服务提供程序可以使用 tcp 代理 v2 检索有关服务使用者的连接信息。 服务提供程序负责设置接收方配置，以便能够分析代理协议 v2 标头。        |
|||


### <a name="details"></a>详细信息

- 可以从同一区域中已批准的专用终结点访问专用链接服务。 可以使用专用 VPN 或 ExpressRoute 连接从同一虚拟网络、突破对等互连 Vnet、全球对等互连 Vnet 和本地访问专用终结点。 
 
- 创建专用链接服务时，会为资源的生命周期创建一个网络接口。 此接口不能由客户管理。
 
- 专用链接服务必须与虚拟网络和标准负载均衡器部署在同一区域中。  
 
- 可以从属于不同 Vnet、订阅和/或 Active Directory 租户的多个专用终结点访问单个专用链接服务。 通过连接工作流建立连接。 
 
- 可以使用不同的前端 IP 配置在同一标准负载均衡器上创建多个专用链接服务。 对于每个标准负载均衡器和每个订阅可以创建的专用链接服务的数量有限制。 有关详细信息，请参阅 [Azure 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。
 
- 专用链接服务可以有多个链接到的 NAT IP 配置。 选择多个 NAT IP 配置可帮助服务提供程序进行缩放。 目前，服务提供商可以为每个专用链接服务分配多达8个 NAT IP 地址。 对于每个 NAT IP 地址，可以为 TCP 连接分配更多端口，从而扩大规模。将多个 NAT IP 地址添加到专用链接服务后，将无法删除 NAT IP 地址。 这样做是为了确保在删除 NAT IP 地址时活动连接不会受到影响。


## <a name="alias"></a>Alias

**别名**是服务的全局唯一名称。 它可帮助你屏蔽服务的客户数据，同时为你的服务创建一个易于共享的名称。 创建专用链接服务时，Azure 将为你的服务生成可与客户共享的别名。 你的客户可以使用此别名请求连接到你的服务。

别名由三部分组成：*前缀*。*GUID*。*后缀*

- 前缀为服务名称。 你可以选择自己的前缀。 创建 "Alias" 后，无法对其进行更改，因此请相应地选择前缀。  
- GUID 将由平台提供。 这有助于使名称全局唯一。 
- 后缀追加到*azure： privatelinkservice* 

完整的别名： *Prefix*。 {GUID}。privatelinkservice  

## <a name="control-service-exposure"></a>控制服务公开

专用链接服务提供通过 "可见性" 设置控制服务公开的选项。 你可以使服务专用于你所拥有的不同 Vnet （仅 RBAC 权限）的使用，将泄露限制为你信任的有限订阅集，或将其设为公开，以便所有 Azure 订阅都可以请求专用链接上的连接服务. 可见性设置确定使用者是否可以连接到你的服务。 

## <a name="control-service-access"></a>控制服务访问

向专用链接服务公开（由可见性设置控制）的使用者可以在其 Vnet 中创建专用终结点，并请求连接到专用链接服务。 专用终结点连接将在私有链接服务对象上以 "挂起" 状态创建。 服务提供商负责处理连接请求。 你可以批准连接，拒绝连接，或删除连接。 只有已批准的连接才能将流量发送到专用链接服务。

通过使用专用链接服务上的自动批准属性，可以自动执行批准连接的操作。 自动批准是服务提供商 preapprove 一组订阅以自动访问其服务的能力。 若要将服务提供商添加到自动批准列表，客户需要脱机共享其订阅。 自动批准是可见性数组的子集。 可见性控制曝光设置，而自动批准控制服务的批准设置。 如果客户从自动批准列表中的订阅请求连接，则会自动批准连接并建立连接。 服务提供商无需再手动批准请求。 另一方面，如果客户从可见性数组而不是自动批准数组中的订阅请求连接，则该请求将到达服务提供商，但服务提供商必须手动批准连接。

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>使用 TCP 代理 v2 获取连接信息

使用专用链接服务时，来自专用终结点的数据包的源 IP 地址是服务提供商端的网络地址转换（NAT），使用从提供程序的虚拟网络分配的 NAT IP。 因此，应用程序接收分配的 NAT IP 地址，而不是服务使用者的实际源 IP 地址。 如果应用程序需要来自使用方的实际源 IP 地址，则可以在服务上启用代理协议，并从代理协议标头中检索信息。 除了源 IP 地址外，代理协议标头还携带专用终结点的 LinkID。 源 IP 地址和 LinkID 的组合可帮助服务提供商唯一标识其使用者。 有关代理协议的详细信息，请访问[此处](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)。 

此信息使用自定义的类型-长度-值（TLV）向量进行编码，如下所示：

自定义 TLV 详细信息：

|字段 |长度（八进制）  |说明  |
|---------|---------|----------|
|类型  |1        |PP2_TYPE_AZURE （0xEE）|
|长度  |2      |值的长度|
|值  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID （0x01）|
|  |4        |UINT32 （4个字节），表示专用终结点的 LINKID。 编码为 little endian 格式。|

 > [!NOTE]
 > 服务提供商负责确保标准负载均衡器后面的服务配置为在专用链接服务上启用代理协议时根据[规范](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)分析代理协议标头。 如果在专用链接服务上启用了代理协议设置，但未将服务提供商的服务配置为分析该标头，则请求将失败。 同样，如果服务提供商的服务需要代理协议标头，而专用链接服务上未启用该设置，则该请求将失败。 一旦启用了代理协议设置，代理协议标头也将包含在从主机到后端虚拟机的 HTTP/TCP 运行状况探测中，即使标头中没有客户端信息也是如此。 

## <a name="limitations"></a>限制

以下是使用专用链接服务时的已知限制：
- 仅标准负载均衡器上支持 
- 仅支持 IPv4 流量
- 仅支持 TCP 流量

## <a name="next-steps"></a>后续步骤
- [使用 Azure PowerShell 创建专用链接服务](create-private-link-service-powershell.md)
- [使用 Azure CLI 创建专用链接服务](create-private-link-service-cli.md)
