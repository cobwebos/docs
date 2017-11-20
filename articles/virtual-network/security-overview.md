---
title: "Azure 网络安全概述 | Microsoft Docs"
description: "了解用于控制 Azure 资源之间的网络流量流的安全选项。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: d4a216b612274ff1de499bd4892ff7422c66b4d0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="network-security"></a>网络安全

可以使用网络安全组限制发往虚拟网络中的资源的网络流量。 网络安全组包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。 

## <a name="network-security-groups"></a>网络安全组

每个网络接口有零个或一个关联的网络安全组。 每个网络接口位于[虚拟网络](virtual-networks-overview.md)子网中。 一个子网也可以有零个或一个关联的网络安全组。 

应用到某个子网时，安全规则会应用到该子网中的所有资源。 除了部署网络接口以外，还可以在子网中部署其他 Azure 服务的实例，例如 HDInsight、虚拟机规模集和应用程序服务环境。

下面描述了当网络安全组同时与网络接口和该网络接口所在的子网相关联时，如何应用网络安全组：

- **入站流量**：首先评估与网络接口所在子网关联的网络安全组。 然后，与网络接口关联的网络安全组会评估允许通过与子网相关联的网络安全组的所有流量。 例如，我们可能要求通过 Internet 在端口 80 上对某个虚拟机进行入站访问。 如果将网络安全组同时关联到网络接口以及该网络接口所在的子网，则与子网和网络接口关联的网络安全组必须允许端口 80。 如果只允许端口 80 上的流量通过与子网或该子网所在的网络接口相关联的网络安全组，则会由于默认安全规则方面的原因而导致通信失败。 有关详细信息，请参阅[默认安全规则](#default-security-rules)。 如果只将网络安全组应用到子网或网络接口，并且该网络安全组包含允许入站端口 80 流量的规则，则通信会成功。 
- **出站流量**：首先评估与网络接口关联的网络安全组。 然后，与子网关联的网络安全组会评估允许通过与网络接口相关联的网络安全组的所有流量。

将网络安全组同时应用到网络接口和子网时，你不一定总能察觉得到。 可以通过查看网络接口的[有效安全规则](virtual-network-manage-nsg-arm-portal.md)，轻松查看已应用到网络接口的聚合规则。 还可以使用 Azure 网络观察程序中的 [IP 流验证](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能来确定是否允许发往或发自网络接口的通信。 该工具会告知通信是否受允许，以及哪个网络安全规则允许或拒绝流量。
 
> [!NOTE]
> 网络安全组关联到子网或关联到部署经典部署模型的虚拟机和云服务，而不是关联到资源管理器部署模型中的网络接口。 若要详细了解 Azure 部署模型，请参阅[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

可将同一网络安全组应用到所选的任意数量的各个网络接口和子网。

## <a name="security-rules"></a>安全规则

一个网络安全组包含零个或者不超过 Azure 订阅[限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)的任意数量的规则。 每个规则指定以下属性：

|属性  |说明  |
|---------|---------|
|名称|网络安全组中的唯一名称。|
|Priority | 介于 100 和 4096 之间的数字。 规则按优先顺序进行处理。先处理编号较小的规则，因为编号越小，优先级越高。 一旦流量与某个规则匹配，处理即会停止。 因此，不会处理优先级较低（编号较大）的、其属性与高优先级规则相同的所有规则。|
|源或目标| 值为“任何”，或者单个 IP 地址、CIDR 块（例如 10.0.0.0/24）、服务标记或应用程序安全组。 详细了解[服务标记](#service-tags)和[应用程序安全组](#application-security-groups)。 指定范围、服务标记或应用程序安全组可以减少创建的安全规则数。 在一个规则中指定多个单独的 IP 地址和范围（不能指定多个服务标记或应用程序组）的功能以预览版提供，称为扩充式安全规则。 只能在通过资源管理器部署模型创建的网络安全组中创建扩充式安全规则。 在通过经典部署模型创建的网络安全组中，不能指定多个 IP 地址和 IP 地址范围。|
|协议     | TCP、UDP 或“任何”，包括 TCP、UDP 和 ICMP。 不能仅指定 ICMP，因此，如果需要 ICMP，则必须使用“任何”。 |
|方向| 该规则是应用到入站还是出站流量。|
|端口范围     |可以指定单个端口或端口范围。 例如，可以指定 80 或 10000-10005。 指定范围可以减少创建的安全规则数。 在一个规则中指定多个单独的端口和端口范围的功能以预览版提供，称为扩充式安全规则。 在使用扩充式安全规则之前，请阅读[预览功能](#preview-features)了解重要信息。 只能在通过资源管理器部署模型创建的网络安全组中创建扩充式安全规则。 在通过经典部署模型创建的网络安全组中，不能在同一个安全规则中指定多个端口或端口范围。   |
|操作     | 允许或拒绝        |

安全规则是有状态的。 例如，如果针对通过端口 80 访问的任何地址指定了出站安全规则，则不需要指定入站安全规则来响应出站流量。 如果通信是从外部发起的，则只需指定入站安全规则。 反之亦然。 如果允许通过某个端口发送入站流量，则不需要指定出站安全规则来响应通过该端口发送的流量。 若要了解创建安全规则时的限制，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="augmented-security-rules"></a>扩充式安全规则

扩充式规则简化了虚拟网络的安全定义，可让我们以更少的规则定义更大、更复杂的网络安全策略。 可将多个端口、多个显式 IP 地址、服务标记和应用程序安全组合并成一个易于理解的安全规则。 可在规则的源、目标和端口字段中使用扩充式规则。 创建规则时，可以指定多个显式 IP 地址、CIDR 范围和端口。 若要简化安全规则定义的维护，可将扩充式安全规则与服务标记或应用程序安全组合并。 

扩充式安全规则以预览版提供。 若要了解创建扩充式安全规则时的限制，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 预览版功能的可用性和可靠性级别与正式版不同。 预览版功能仅在以下区域提供：美国东部、美国西部、美国西部 2 区、美国中西部、澳大利亚东部、澳大利亚东南部和英国南部。

## <a name="default-security-rules"></a>默认安全规则

如果网络安全组不与某个子网或网络接口相关联，则允许发往该子网或网络接口的所有入站流量，或发自该子网或网络接口的所有出站流量。 创建网络安全组后，Azure 会立即在该网络安全组中创建以下默认规则：

### <a name="inbound"></a>入站

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priority|源|源端口|目标|目标端口|协议|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|全部|ALLOW|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priority|源|源端口|目标|目标端口|协议|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|全部|ALLOW|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priority|源|源端口|目标|目标端口|协议|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|全部|DENY|

### <a name="outbound"></a>出站

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priority|源|源端口| 目标 | 目标端口 | 协议 | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 全部 | ALLOW |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priority|源|源端口| 目标 | 目标端口 | 协议 | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | 全部 | ALLOW |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priority|源|源端口| 目标 | 目标端口 | 协议 | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 全部 | DENY |

在“源”和“目标”列表中，“VirtualNetwork”、“AzureLoadBalancer”和“Internet”是[服务标记](#tags)，而不是 IP 地址。 在“协议”列中，“所有”包含 TCP、UDP 和 ICMP。 创建规则时，可以指定 TCP、UDP 或“所有”，但不能仅指定 ICMP。 因此，如果规则需要 ICMP，则必须为协议选择“所有”。 “源”和“目标”列中的“0.0.0.0/0”表示所有地址。
 
不能删除默认规则，但可以通过创建更高优先级的规则来替代默认规则。

## <a name="service-tags"></a>服务标记

 服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。 无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 可在安全规则定义中使用以下服务标记。 服务标记的名称根据 [Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的不同而略有不同。

* **VirtualNetwork**（资源管理器）（如果是经典部署模型，则为 *VIRTUAL_NETWORK**）：此标记包括虚拟网络地址空间（为虚拟网络定义的所有 CIDR 范围）、所有连接的本地地址空间，以及[对等互连](virtual-network-peering-overview.md)的虚拟网络，或已连接到[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的虚拟网络。
* **AzureLoadBalancer** (Resource Manager)（如果是经典部署模型，则为 **AZURE_LOADBALANCER**）：此标记表示 Azure 的基础结构负载均衡器。 此标记将转换为 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653)，Azure 的运行状况探测源于该 IP。 如果不使用 Azure 负载均衡器，则可替代此规则。
* **Internet**（资源管理器）（如果是经典部署模型，则为 **INTERNET**）：此标记表示虚拟网络外部的 IP 地址空间，可以通过公共 Internet 进行访问。 地址范围包括 [Azure 拥有的公共 IP 地址空间](https://www.microsoft.com/download/details.aspx?id=41653)。
* **AzureTrafficManager**（仅限资源管理器）：此标记表示 Azure 流量管理器服务的 IP 地址空间。
* **Storage**（仅限资源管理器）：此标记表示 Azure 存储服务的 IP 地址空间。 如果指定 *Storage* 作为值，则会允许或拒绝发往存储的流量。 如果只想允许对某个特定[区域](https://azure.microsoft.com/regions)中的存储进行访问，可以指定该区域。 例如，如果希望只允许访问美国东部区域中的 Azure 存储，可以指定 *Storage.EastUS* 作为服务标记。 其他可用的区域服务标记：Storage.AustraliaEast、Storage.AustraliaSoutheast、Storage.EastUS、Storage.UKSouth、Storage.WestCentralUS、Storage.WestUS 和 Storage.WestUS2。 标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure 存储服务，但不能表示特定的 Azure 存储帐户。
* **Sql**（仅限资源管理器）：此标记表示 Azure SQL 数据库和 Azure SQL 数据仓库服务的地址前缀。 只能为此服务标记指定特定的区域。 例如，如果希望只允许访问美国东部区域中的 Azure SQL 数据库，可以指定 *Sql.EastUS* 作为服务标记。 不能针对所有 Azure 区域单独指定 Sql，而必须单独指定区域。 其他可用的区域服务标记：Sql.AustraliaEast、Sql.AustraliaSoutheast、Sql.EastUS、Sql.UKSouth、Sql.WestCentralUS、Sql.WestUS 和 Sql.WestUS2。 标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 Azure SQL 数据库。

> [!WARNING]
> AzureTrafficManager、Storage 和 Sql 服务标记以预览版提供。 预览版功能的可用性和可靠性级别与正式版不同。 这些服务标记仅在以下区域提供：美国东部、美国西部、美国西部 2 区、美国中西部、澳大利亚东部、澳大利亚东南部和英国南部。

> [!NOTE]
> 如果为某个服务（例如 Azure 存储或 Azure SQL 数据库）实现虚拟网络服务终结点，Azure 会将路由添加到该服务的虚拟网络子网。 路由的地址前缀与相应服务标记的地址前缀或 CIDR 范围相同。

## <a name="application-security-groups"></a>应用程序安全组

使用应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，从而可以基于这些组将虚拟机分组以及定义网络安全策略。 借助此功能，可以大量重复使用安全策略，而无需手动维护显式 IP 地址。 平台会处理显式 IP 地址和多个规则集存在的复杂性，让你专注于业务逻辑。

可将应用程序安全组指定为安全规则中的源和目标。 定义安全策略后，可以创建虚拟机，并将虚拟机中的网络接口分配到应用程序安全组。 该策略会根据虚拟机中每个网络接口的应用程序安全组成员身份应用。 以下示例演示如何针对订阅中的所有 Web 服务器使用应用程序安全组：

1. 创建名为 *WebServers* 的应用程序安全组。
2. 创建名为 *MyNSG* 的网络安全组。
3. 在该网络安全组中创建一个入站安全规则，为源地址指定 *Internet* 服务标记，将 *WebServers* 应用程序安全组指定为目标地址，并允许端口 80 和 443。
4. 部署运行 Web 服务器应用程序的虚拟机。 指定虚拟机中的网络接口是 *WebServers* 应用程序安全组的成员。 然后，允许端口 80 和 443 访问虚拟机。 此外，还允许这些端口访问所创建的并已指定为 *WebServers* 应用程序安全组成员的所有后续 Web 服务器。 

如果在上述示例中创建其他规则并将其他应用程序安全组指定为目标，则这些规则不会应用到 Web 服务器。 指定应用程序安全组的规则只会应用到属于应用程序安全组的成员的网络接口。 将应用程序安全组与扩充式安全规则和服务标记相结合，可以创建少量的网络安全组来管理订阅中的网络安全性。
 
若要了解创建应用程序安全组以及在安全规则中指定应用程序安全组时的相关限制，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

应用程序安全组以预览版提供。 在使用应用程序安全组之前，必须完成[创建包含应用程序安全组的网络安全组](create-network-security-group-preview.md#powershell)中的步骤 1-5 来注册使用这些功能，并阅读[预览功能](#preview-features)，了解重要信息。 应用程序安全组具有以下约束：

-   一个应用程序安全组中的所有网络接口必须存在于同一虚拟网络中。 不能向同一应用程序安全组添加来自不同虚拟网络的网络接口。 第一个分配给应用程序安全组的网络接口所在的虚拟网络定义所有后续分配的网络接口必须存在于其中的虚拟网络。
- 如果在安全规则中将应用程序安全组指定为源和目标，则两个应用程序安全组中的网络接口必须存在于同一虚拟网络中。 例如，如果 ASG1 包含来自 VNet1 的网络接口，ASG2 包含来自 VNet2 的网络接口，则不能在一项规则中将 ASG1 分配为源，将 ASG2 分配为目标，所有网络接口需存在于 VNet1 中。 

预览版功能的可用性和可靠性级别与正式版不同。 在使用应用程序安全组之前，必须先注册使用这些功能。 这些功能仅在以下区域提供：美国中西部。


## <a name="azure-platform-considerations"></a>Azure 平台注意事项

- **主机节点的虚拟 IP**：基本的基础结构服务（例如 DHCP、DNS 和运行状况监视）是通过虚拟化主机 IP 地址 168.63.129.16 和 169.254.169.254 提供的。 这些公共 IP 地址属于 Microsoft，是仅有的用于所有区域的虚拟化 IP 地址，没有其他用途。 此地址映射到托管虚拟机的服务器计算机（主机节点）的物理 IP 地址。 主机节点充当 DHCP 中继、DNS 递归解析器，以及进行负载均衡器运行状况探测和计算机运行状况探测的探测源。 与这些 IP 地址的通信不是攻击。 如果阻止发往或发自这些 IP 地址的流量，虚拟机可能无法正常工作。
- **许可（密钥管理服务）**：在虚拟机中运行的 Windows 映像必须获得许可。 为了确保许可，会向处理此类查询的密钥管理服务主机服务器发送请求。 该请求是通过端口 1688 以出站方式提出的。
- **负载均衡池中的虚拟机**：应用的源端口和地址范围来自源计算机，而不是来自负载均衡器。 目标端口和地址范围是目标计算机的，而不是负载均衡器的。
- **Azure 服务实例**：在虚拟网络子网中部署了多个 Azure 服务的实例，例如 HDInsight、应用程序服务环境和虚拟机规模集。 有关可部署到虚拟网络的服务的完整列表，请参阅 [Azure 服务的虚拟网络](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 在将网络安全组应用到部署了资源的子网之前，请确保熟悉每个服务的端口要求。 如果拒绝服务所需的端口，服务将无法正常工作。
- **发送出站电子邮件**：Microsoft 建议你利用经过身份验证的 SMTP 中继服务（通常通过 TCP 端口 587 进行连接，但也经常使用其他端口）从 Azure 虚拟机发送电子邮件。 SMTP 中继服务特别重视发件人信誉，尽量降低第三方电子邮件提供商拒绝邮件的可能性。 此类 SMTP 中继服务包括但不限于：Exchange Online Protection 和 SendGrid。 在 Azure 中使用 SMTP 中继服务绝不会受限制，不管订阅类型如何。 

  如果是在 2017 年 11 月 15 日之前创建的 Azure 订阅，则除了能够使用 SMTP 中继服务，还可以直接通过 TCP 端口 25 发送电子邮件。 如果是在 2017 年 11 月 15 日之后创建的订阅，则可能无法直接通过端口 25 发送电子邮件。 经端口 25 的出站通信行为取决于订阅类型，如下所示：

     - **企业协议**：允许端口 25 的出站通信。 可以将出站电子邮件直接从虚拟机发送到外部电子邮件提供商，不受 Azure 平台的限制。 
     - **即用即付：**阻止所有资源通过端口 25 进行出站通信。 如需将电子邮件从虚拟机直接发送到外部电子邮件提供商（不使用经身份验证的 SMTP 中继），可以请求去除该限制。 Microsoft 会自行审核和批准此类请求，并且只在进行防欺诈检查后授予相关权限。 若要提交请求，请建立一个问题类型为“技术”、“虚拟网络连接”、“无法发送电子邮件（SMTP/端口 25）”的支持案例。 在支持案例中，请详细说明为何你的订阅需要将电子邮件直接发送到邮件提供商，而不经过经身份验证的 SMTP 中继。 如果订阅得到豁免，则只有在豁免日期之后创建的虚拟机能够经端口 25 进行出站通信。
     - **MSDN、Azure Pass、Azure 开放许可、教育、BizSpark 和免费试用版**：阻止所有资源经端口 25 进行出站通信。 不能请求去除该限制，因为不会针对请求授予相关权限。 如果必须从虚拟机发送电子邮件，则必须使用 SMTP 中继服务。

  即使 Azure 允许经端口 25 发送电子邮件，Microsoft 也不能保证电子邮件提供商会接受来自你的虚拟机的入站电子邮件。 如果特定的提供商拒绝了来自你的虚拟机的邮件，你必须直接与该提供商协商解决邮件传送问题或垃圾邮件过滤问题，否则只能使用经身份验证的 SMTP 中继服务。 


## <a name="next-steps"></a>后续步骤

* 完成[创建网络安全组](virtual-networks-create-nsg-arm-pportal.md)教程
* 完成[创建包含应用程序安全组的网络安全组](create-network-security-group-preview.md)教程
