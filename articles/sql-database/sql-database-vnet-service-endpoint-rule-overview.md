---
title: "适用于 Azure SQL 数据库的虚拟网络服务终结点和规则 | Microsoft Docs"
description: "将子网标记为虚拟网络服务终结点。 然后将终结点标记为适用于 Azure SQL 数据库 ACL 的虚拟网络规则。 然后，SQL 数据库就会接受来自子网上所有虚拟机和其他节点的通信。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/30/2017
ms.author: genemi
ms.openlocfilehash: 69059b6169e1fac4d0abea2770b0de502f816113
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>使用适用于 Azure SQL 数据库的虚拟网络服务终结点和规则

虚拟网络规则是一种防火墙安全功能，用于控制是否允许 Azure SQL 数据库服务器接受从虚拟网络中的特定子网发送的通信。 本文说明了为何有时候最好选择虚拟网络规则功能来安全地启用到 Azure SQL 数据库的通信。

若要创建虚拟网络规则，首先必须具有可供规则引用的[虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]。


> [!NOTE]
> 对于 Azure SQL 数据库，此功能以预览版形式针对以下 Azure 区域提供：
>
> - WestCentralUS、WestUS2 和 EastUS。


#### <a name="how-to-create-a-virtual-network-rule"></a>如何创建虚拟网络规则

如果只创建虚拟网络规则，则可跳到[本文后面](#anchor-how-to-by-using-firewall-portal-59j)的步骤和说明。






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>术语和说明

虚拟网络：可以让虚拟网络与 Azure 订阅相关联。

子网：虚拟网络包含子网。 你所拥有的任何 Azure 虚拟机 (VM) 都会分配到子网。 一个子网可能包含多个 VM 或其他计算节点。 虚拟网络之外的计算节点不能访问虚拟网络，除非已将安全性配置为允许这样的访问。

虚拟网络服务终结点：[虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 本文介绍 **Microsoft.Sql** 的类型名称，即名为“SQL 数据库”的 Azure 服务。

虚拟网络规则：适用于 SQL 数据库服务器的虚拟网络规则是一个子网，列在 SQL 数据库服务器的访问控制列表 (ACL) 中。 子网必须包含“Microsoft.Sql”类型名称才会将其列在 SQL 数据库的 ACL 中。

虚拟网络规则要求 SQL 数据库服务器接受来自子网上所有节点的通信。







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>虚拟网络规则的优势

在你采取措施之前，子网上的 VM 不能与 SQL 数据库通信。 若要弄清楚为何需要选择虚拟网络规则方法来启用通信，则必须进行一个比较和对比式的讨论，其中涉及到防火墙提供的竞争性安全选项。

#### <a name="a-allow-access-to-azure-services"></a>A. 允许访问 Azure 服务

防火墙窗格有一个标记为“允许访问 Azure 服务”的“启用/禁用”按钮。 “启用”设置允许来自所有 Azure IP 地址和所有 Azure 子网的通信。 这些 Azure IP 或子网可能不是你所拥有的。 此“启用”设置可能超出你在开放方面对 SQL 数据库的需要。 虚拟网络规则功能提供精细得多的控制。

#### <a name="b-ip-rules"></a>B. IP 规则

可以通过 SQL 数据库防火墙规则指定 IP 地址范围，处于该范围内的通信允许进入 SQL 数据库。 此方法适用于 Azure 专用网络外部的稳定 IP 地址。 但是，Azure 专用网络内部的许多节点都配置了动态 IP 地址。 某些情况下（例如重启 VM 时），动态 IP 地址可能会变化。 处于生产环境中时，在防火墙规则中指定一个动态 IP 地址并不明智。

可以通过获取 VM 的静态 IP 地址，对 IP 选项进行“补救”。 有关详细信息，请参阅[使用 Azure 门户为虚拟机配置专用 IP 地址][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]。

但是，静态 IP 方法可能会变得难以管理，在规模大时操作成本高。 虚拟网络规则更易于制定和管理。

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. 目前，子网上不允许有 SQL 数据库

如果 Azure SQL 数据库服务器是虚拟网络子网上的一个节点，则虚拟网络中的所有节点都可以与 SQL 数据库通信。 在这种情况下，VM 可以与 SQL 数据库通信，不需任何虚拟网络规则或 IP 规则。

但截至 2017 年 9 月，Azure SQL 数据库服务仍然无法分配给子网。






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>虚拟网络规则详细信息

此部分介绍虚拟网络规则的多项详细信息。

#### <a name="only-one-geographic-region"></a>只有一个地理区域

一个虚拟网络服务终结点只能应用于一个 Azure 区域。 终结点不允许其他区域接受来自该子网的通信。

任何虚拟网络规则都只能应用于基础终结点应用到的区域。

#### <a name="server-level-not-database-level"></a>服务器级而非数据库级

每个虚拟网络规则都适用于整个 Azure SQL 数据库服务器，而不仅仅是该服务器上某个特定的数据库。 换句话说，虚拟网络规则适用于服务器级而非数据库级。

- 与之不同的是，IP 规则适用于这其中的任一级别。

#### <a name="security-administration-roles"></a>安全管理角色

在管理虚拟网络服务终结点时，安全角色是分开的。 下述每个角色都需要进行操作：

- 网络管理员：&nbsp;启用终结点。
- 数据库管理员：&nbsp;更新访问控制列表 (ACL)，将给定的子网添加到 SQL 数据库服务器。

RBAC 备用： 

网络管理员和数据库管理员角色的权限超出虚拟网络规则的管理需要， 只有部分权限是必需的。

可以选择在 Azure 中使用[基于角色的访问控制 (RBAC)][rbac-what-is-813s]，创建一个只有部分必需权限的自定义角色。 在涉及到网络管理员或数据库管理员时，可以使用自定义角色来代替。与向两个主要的管理员角色添加用户相比，向自定义角色添加用户的安全风险较低。






## <a name="limitations"></a>限制

对于 Azure SQL 数据库，虚拟网络规则功能具有以下限制：

- 目前，已打开“服务终结点”的子网中的 Azure Web 应用尚未按预期方式运行。 我们正在努力实现此功能。
    - 在完全实现此功能之前，我们建议将 Web 应用移到其他没有为 SQL 打开服务终结点的子网。

- 在 SQL 数据库的防火墙中，每个虚拟网络规则都引用一个子网。 引用的所有这些子网都必须托管在同一个托管 SQL 数据库的地理区域内。

- 对于任何给定的虚拟网络，每个 Azure SQL 数据库服务器最多可拥有 128 个 ACL 条目。

- 虚拟网络规则仅适用于 Azure 资源管理器虚拟网络，不适用于[经典部署模型][arm-deployment-model-568f]网络。

- 在防火墙上，IP 地址范围适用于以下网络项，但虚拟网络规则并不适用：
    - [站点到站点 (S2S) 虚拟专用网络 (VPN)][vpn-gateway-indexmd-608y]
    - 通过 [Expressroute][expressroute-indexmd-744v] 进行的本地连接

#### <a name="expressroute"></a>ExpressRoute

如果网络通过使用 [ ExpressRoute ][expressroute-indexmd-744v] 连接到 Azure 网络，则每个线路在 Microsoft Edge 配置有两个公共 IP 地址。 这两个 IP 地址用于通过使用 Azure 公共对等互连连接到 Azure 存储等 Microsoft 服务。

若要允许从线路到 Azure SQL 数据库的通信，则必须为线路的公共 IP 地址创建 IP 网络规则。 为查找 ExpressRoute 线路的公共 IP 地址，请使用 Azure 门户开具 ExpressRoute 支持票证。


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->





## <a name="errors-40914-and-40615"></a>错误 40914 和 40615

连接错误 40914 与虚拟网络规则（如 Azure 门户中的“防火墙”窗格所指定）相关。 错误 40615 基本相似，不同之处在于与“防火墙”上的“IP 地址规则”相关。

#### <a name="error-40914"></a>错误 40914

消息正文：无法打开登录时请求的服务器 [server-name]。 不允许客户端访问服务器。

错误描述：客户端位于包含虚拟网络服务器终结点的子网中。 不过，Azure SQL 数据库服务器没有虚拟网络规则授权子网与 SQL 数据库进行通信。

错误解决方法：在 Azure 门户的“防火墙”窗格中，使用虚拟网络规则控件为子网[添加虚拟网络规则](#anchor-how-to-by-using-firewall-portal-59j)。

#### <a name="error-40615"></a>错误 40615

消息正文：无法打开登录时请求的服务器“{0}”。 不允许具有 IP 地址“{1}”的客户端访问服务器。

错误描述：客户端尝试从未经授权连接到 Azure SQL 数据库服务器的 IP 地址进行连接。 服务器防火墙没有 IP 地址规则允许客户端从给定 IP 地址与 SQL 数据库进行通信。

错误解决方法：输入客户端 IP 地址作为 IP 规则。 为此，可以使用 Azure 门户中的“防火墙”窗格。


[此处][sql-database-develop-error-messages-419g]收录了多个 SQL 数据库错误消息的列表。





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>门户可以创建虚拟网络规则

本部分介绍如何使用 [Azure 门户][http-azure-portal-link-ref-477t]在 Azure SQL 数据库中创建虚拟网络规则。 此规则要求 SQL 数据库接受来自特定子网的通信，该子网已被标记为“虚拟网络服务终结点”。

#### <a name="powershell-alternative"></a>PowerShell 备用

PowerShell 脚本也可创建虚拟网络规则。 重要的 cmdlet New-AzureRmSqlServerVirtualNetworkRule。 如果有兴趣，可以参阅[使用 PowerShell 创建 Azure SQL 数据库的虚拟网络服务终结点和规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

#### <a name="prerequisites"></a>先决条件

必须有一个子网已经使用特定的虚拟网络服务终结点类型名称进行标记，且该名称必须与 Azure SQL 数据库相关。

- 相关的终结点类型名称为 Microsoft.Sql。
- 如果子网没有使用类型名称进行标记，请参阅[验证子网是否是终结点][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]。

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Azure 门户步骤

1. 登录到 [Azure 门户][http-azure-portal-link-ref-477t]。

2. 然后在门户中导航到“SQL 服务”&gt;“防火墙/虚拟网络”。

3. 将“允许访问 Azure 服务”控件设置为“禁用”。

    > [!IMPORTANT]
    > 如果将该控件设置为“启用”，则 Azure SQL 数据库服务器会接受来自任何子网的通信，从安全角度来说，这样的访问权限设置过高。 针对 SQL 数据库结合使用 Microsoft Azure 虚拟网络服务终结点功能和虚拟网络规则功能，可以降低安全风险。

4. 在“虚拟网络”部分单击“+ 添加现有项”控件。

    ![单击“添加现有项(子网终结点，充当 SQL 规则)”。][image-portal-firewall-vnet-add-existing-10-png]

5. 在新的“创建/更新”窗格的控件中填充 Azure 资源的名称。
 
    > [!TIP]
    > 必须包括子网的正确地址前缀。 可以在门户中找到该值。 导航到“所有资源”&gt;“所有类型”&gt;“虚拟网络”。 筛选器会显示虚拟网络。 单击虚拟网络，然后单击“子网”。 “地址范围”列包含所需的地址前缀。

    ![填充新规则的字段。][image-portal-firewall-create-update-vnet-rule-20-png]

6. 单击窗格底部的“确定”按钮。

7.  查看防火墙窗格中生成的虚拟网络规则。

    ![查看防火墙窗格中的新规则。][image-portal-firewall-vnet-result-rule-30-png]






<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>相关文章

- [使用 PowerShell 创建 Azure SQL 数据库的虚拟网络服务终结点，然后创建虚拟网络规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Azure 虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL 数据库服务器级和数据库级防火墙规则][sql-db-firewall-rules-config-715d]

适用于 Azure SQL 数据库的 Microsoft Azure 虚拟网络服务终结点功能和虚拟网络规则功能已在 2017 年 9 月下旬发布。





<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md#classic-deployment-characteristics

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/




<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->

