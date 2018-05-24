---
title: Azure 应用服务环境的网络注意事项
description: 介绍 ASE 网络流量，以及如何在 ASE 中设置 NSG 和 UDR
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: ccompy
ms.openlocfilehash: d099163cdc34624afd8f01b8f1978c5ee902d1ff
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="networking-considerations-for-an-app-service-environment"></a>应用服务环境的网络注意事项 #

## <a name="overview"></a>概述 ##

 Azure [应用服务环境][Intro]是指将 Azure App Service 部署到 Azure 虚拟网络 (VNet) 的子网中。 应用服务环境 (ASE) 具有两种部署类型：

- 外部 ASE：在 Internet 可访问的 IP 地址上公开 ASE 托管的应用。 有关详细信息，请参阅[创建外部 ASE][MakeExternalASE]。
- ILB ASE：在 VNet 中的 IP 地址上公开 ASE 托管的应用。 内部终结点是一个内部负载均衡器 (ILB)，因此该类部署被称为 ILB ASE。 有关详细信息，请参阅[创建和使用 ILB ASE][MakeILBASE]。

目前，有两个版本的应用服务环境：ASEv1 和 ASEv2。 有关 ASEv1 的信息，请参阅[应用服务环境 v1 简介][ASEv1Intro]。 可在经典 VNet 或资源管理器 VNet 中部署 ASEv1。 而 ASEv2 只能部署到资源管理器 VNet 中。

从 ASE 发出的、转到 Internet 的所有调用将通过分配给 ASE 的 VIP 离开 VNet。 然后，此 VIP 的公共 IP 将成为从 ASE 发出的、转到 Internet 的所有调用的源 IP。 如果 ASE 中的应用调用了 VNet 中的资源或通过 VNet 发出调用，则源 IP 是 ASE 使用的子网中的某个 IP。 由于 ASE 在 VNet 中，因此可以访问 VNet 中的资源，而不需要进行其他任何配置。 如果 VNet 连接到本地网络，则 ASE 中的应用也可访问此处的资源。 无需再进一步配置 ASE 或自己的应用。

![外部 ASE][1] 

如果拥有外部 ASE，则公共 VIP 还是 ASE 应用针对下述项解析到的终结点：

* HTTP/S。 
* FTP/S。 
* Web 部署。
* 远程调试。

![ILB ASE][2]

如果拥有 ILB ASE，则 ILB 的 IP 地址是用于 HTTP/S、FTP/S、Web 部署和远程调试的终结点。

普通的应用访问端口为：

| 用途 | 源 | 目标 |
|----------|---------|-------------|
|  HTTP/HTTPS  | 用户可配置 |  80、443 |
|  FTP/FTPS    | 用户可配置 |  21, 990, 10001-10020 |
|  Visual Studio 远程调试  |  用户可配置 |  4016, 4018, 4020, 4022 |

如果在外部 ASE 或 ILB ASE 上操作，就会使用这些端口。 如果在外部 ASE 上操作，将在公共 VIP 上命中这些端口。 如果在 ILB ASE 上操作，则将在 ILB 上命中这些端口。 如果锁定端口 443，则在门户中公开的某些功能可能会收到影响。 有关详细信息，请参阅[门户依赖项](#portaldep)。

## <a name="ase-subnet-size"></a>ASE 子网大小 ##

用于托管 ASE 的子网的大小在部署 ASE 后不能更改。  ASE 使用每个基础结构角色以及每个独立应用服务计划实例的地址。  此外，还有创建的每个子网的 Azure 网络使用的 5 个地址。  在创建应用前，根本不带应用服务计划的 ASE 将使用 12 个地址。  如果它是 ILB ASE，则在该 ASE 中创建应用前将使用 13 个地址。 扩大应用服务计划时，添加的每个前端将需要额外的地址。  默认情况下，为每 15 个总应用服务计划实例添加前端服务器。 

   > [!NOTE]
   > 子网中仅可具有 ASE。 请务必选择一个容许将来扩展的地址空间。 以后无法更改此设置。 建议大小是地址长度为 128 位的 `/25`。

## <a name="ase-dependencies"></a>ASE 依赖项 ##

ASE 入站访问依赖项如下：

| 用途 | 源 | 目标 |
|-----|------|----|
| 管理 | 应用服务管理地址 | ASE 子网：454、455 |
|  ASE 内部通信 | ASE 子网：所有端口 | ASE 子网：所有端口
|  允许 Azure 负载均衡器入站流量 | Azure 负载均衡器 | ASE 子网：所有端口
|  应用分配的 IP 地址 | 应用分配的地址 | ASE 子网：所有端口

除系统监视以外，入站流量还提供对 ASE 的指挥与控制。 [ASE 管理地址][ASEManagement]文档中列出了此流量的源 IP。 网络安全配置需要允许通过端口 454 和 455 上的所有 IP 进行访问。

在 ASE 子网内，有多个用于内部组件通信的端口，并且可以更改。  这要求 ASE 子网中的所有端口均可从 ASE 子网访问。 

对于 Azure 负载均衡器和 ASE 子网间的通信，需要开放的最小端口是 454、455 和 16001。 端口 16001 用于使负载均衡器和 ASE 之间的流量保持活动状态。 如果使用 ILB ASE，则可将流量锁定为仅 454、455 和 16001 端口。  如果使用外部 ASE，则需要考虑常规的应用访问端口。  如果使用应用分配的地址，则需要向所有端口开放。  如果地址分配给特定应用，则负载均衡器将使用未知的端口提前向 ASE 发送 HTTP 和 HTTPS 流量。

如果使用应用分配的 IP 地址，则需要允许从分配给应用的 IP 到 ASE 子网的流量。

对于出站访问，ASE 依赖于多个外部系统。 这些系统依赖项是使用 DNS 名称定义的，不会映射到一组固定的 IP 地址。 因此，ASE 需要从 ASE 子网跨各种端口对所有外部 IP 进行出站访问。 ASE 具有以下出站依赖项：

| 用途 | 源 | 目标 |
|-----|------|----|
| Azure 存储 | ASE 子网 | table.core.windows.net、blob.core.windows.net、queue.core.windows.net、file.core.windows.net：80、443、445（只有 ASEv1 需要 445。） |
| Azure SQL 数据库 | ASE 子网 | database.windows.net：1433、11000-11999、14000-14999（有关详细信息，请参阅 [SQL 数据库 V12 端口用法](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)。）|
| Azure 管理 | ASE 子网 | management.core.windows.net、management.azure.com：443 
| SSL 证书验证 |  ASE 子网            |  ocsp.msocsp.com、mscrl.microsoft.com、crl.microsoft.com：443
| Azure Active Directory        | ASE 子网            |  Internet：443
| 应用服务管理        | ASE 子网            |  Internet：443
| Azure DNS                     | ASE 子网            |  Internet：53
| ASE 内部通信    | ASE 子网：所有端口 |  ASE 子网：所有端口

如果 ASE 失去这些依赖项的访问权限，将会停止工作。 如果长时间发生此情况，将暂停 ASE。

### <a name="customer-dns"></a>客户 DNS ###

如果在 VNet 中配置了客户定义的 DNS 服务器，则租户工作负荷将使用该服务器。 ASE 仍需出于管理目的与 Azure DNS 通信。 

如果在 VNet 中配置了 VPN 另一端上的客户 DNS，则必须可从包含 ASE 的子网访问 DNS 服务器。

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>门户依赖项 ##

除了 ASE 功能依赖项，还有其他几项与门户体验相关。 Azure 门户中的某些功能依赖于对 _SCM 站点_的直接访问。 Azure 应用服务中的每个应用都有两个 URL。 第一个 URL 用于访问你的应用。 第二个 URL 用于访问 SCM 站点（也称为 _Kudu 控制台_）。 使用 SCM 站点的功能包括：

-   Web 作业
-   函数
-   日志流式处理
-   Kudu
-   扩展
-   进程资源管理器
-   控制台

使用 ILB ASE 时，无法从 VNet 外部通过 Internet 访问 SCM 站点。 在 ILB ASE 上托管应用时，无法从门户使用某些功能。  

其中许多依赖于 SCM 站点的功能也可以直接在 Kudu 控制台中使用。 可直接连接到该站点，无需使用门户。 如果将应用托管在 ILB ASE 中，需使用发布凭据登录。 用于访问 ILB ASE 中托管的应用的 SCM 站点的 URL 采用以下格式： 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

如果 ILB ASE 的域名是 contoso.net，应用名称是 testapp，则将在 testapp.contoso.net 中访问此应用。 在 testapp.scm.contoso.net 中访问其随附的 SCM 站点。

## <a name="functions-and-web-jobs"></a>函数和 Web 作业 ##

函数和 Web 作业依赖于 SCM 站点，但支持在门户中使用，即使应用位于 ILB ASE 中，但前提是浏览器可以访问 SCM 站点。  如果在 ILB ASE 上使用自签名证书，需要让浏览器信任该证书。  对于 IE 和 Edge 而言，这意味着该证书必须在计算机信任存储中。  如果使用的是 Chrome，则意味着已提前接受浏览器中的证书，因为假设直接访问了 SCM 站点。  最佳解决方法是使用浏览器信任链中的商业证书。  

## <a name="ase-ip-addresses"></a>ASE IP 地址 ##

ASE 具有一些需要注意的 IP 地址。 它们是：

- 公共入站 IP 地址：用于外部 ASE 中的应用流量，以及外部 ASE 和 ILB ASE 中的管理流量。
- 出站公共 IP：用作 ASE 发出、离开 VNet 且不经过 VPN 的出站连接的“来源”IP。
- ILB IP 地址：如果使用 ILB ASE。
- 应用分配的基于 IP 的 SSL 地址：仅当配置了基于 IP 的 SSL 时在外部 ASE 上使用。

在 ASE UI 上的 Azure 门户中的 ASEv2 内可以轻松查看所有这些 IP 地址。 若使用 ILB ASE，将列出 ILB 的 IP。

![IP 地址][3]

### <a name="app-assigned-ip-addresses"></a>应用分配的 IP 地址 ###

使用外部 ASE 时，可将 IP 地址分配到各个应用。 无法使用 ILB ASE 实现这一点。 若要深入了解如何将应用配置为自备 IP 地址，请参阅[将现有的自定义 SSL 证书绑定到 Azure Web 应用](../app-service-web-tutorial-custom-ssl.md)。

当应用使用其自身的基于 IP 的 SSL 地址时，ASE 将保留两个映射到该 IP 地址的端口。 它们分别用于 HTTP 流量和 HTTPS 流量。 这些端口列在 ASE UI 上的“ IP 地址”部分中。 流量必须能够从 VIP 抵达这些端口，否则无法访问应用。 配置网络安全组 (NSG) 时，请务必牢记此要求。

## <a name="network-security-groups"></a>网络安全组 ##

[网络安全组][NSGs]可用于控制 VNet 中的网络访问。 使用门户时，有一个最低优先级的隐式拒绝规则可拒绝任何流量。 因此，只需生成允许规则。

在 ASE 中，你无权访问用于托管 ASE 本身的 VM。 它们位于 Microsoft 管理订阅中。 若要限制对 ASE 上的应用的访问，请在 ASE 子网中设置 NSG。 操作时，需特别注意 ASE 依赖项。 如果阻止任何依赖项，ASE 将停止工作。

可通过 Azure 门户或 PowerShell 配置 NSG。 此处仅介绍了 Azure 门户中的操作。 在门户中的“网络”下面创建和管理 NSG 顶级资源。

若要考虑到入站和出站要求，NSG 应与本例中所示的 NSG 相似。 VNet 地址范围为 192.168.250.0/23，ASE 所在的子网为 192.168.251.128/25。

本例列表顶部显示了要使 ASE 正常工作需满足的前两个入站要求。 满足这些要求可以实现 ASE 管理，并使 ASE 能与自身通信。 其他项均可在租户中配置，且可控制对 ASE 托管的应用程序的网络访问。 

![入站安全规则][4]

默认规则允许 VNet 中的 IP 与 ASE 子网对话。 另一条默认规则允许负载均衡器（亦称为公共 VIP）与 ASE 通信。 选择“添加”图标旁边的“默认规则”即可查看此规则。 如果所示 NSG 规则的后面放置一条拒绝其他任何流量的规则，则将阻止 VIP 与 ASE 之间的流量。 要阻止来自 Vnet 内部的流量，请自行添加规则以允许入站。 使用等效于 AzureLoadBalancer 的源，其目标为“任何”，端口范围为 \*。 由于 ASE 子网将应用 NSG 规则，因此无需指定具体的目标。

若向应用分配了 IP 地址，请确保端口保持打开。 可在“应用服务环境” > “IP 地址”中查看端口。  

下列出站规则中显示的所有项均是必需项，最后一项除外。 使用这些端口可以通过网络访问本文前面所述的 ASE 依赖项。 阻止其中的任意一个，ASE 都将停止工作。 列表中的最后一项可让 ASE 与 VNet 中的其他资源通信。

![入站安全规则][5]

定义 NSG 之后，请将其分配到 ASE 所在的子网。 如果你不记得 ASE VNet 或子网，可以通过 ASE 门户页查看。 若要将 NSG 分配到子网，请转到子网 UI 并选择该 NSG。

## <a name="routes"></a>路由 ##

强制隧道是指，在 VNet 中设置路由时，使出站流量不直接前往 Internet，而是前往诸如 ExpressRoute 网关或虚拟设备的其他位置。  如果需要以这样的方式配置 ASE，请阅读有关[为应用服务环境配置强制隧道][forcedtunnel]的文档。  该文档将介绍可用于 ExpressRoute 和强制隧道的选项。

在门户中创建 ASE 时，我们还在随 ASE 创建的子网上创建一组路由表。  这些路由只是指示将出站流量直接发送到 Internet。  
若要手动创建同样的路由，请执行以下步骤：

1. 转到 Azure 门户。 选择“网络” > “路由表”。

2. 在 Vnet 所在的位置新建一个路由表。

3. 在路由表 UI 中选择“路由” > “添加”。

4. 将“下一跃点类型”设置为 Internet，将“地址前缀”设置为 0.0.0.0/0。 选择“保存”。

    然后将看到如下内容：

    ![功能路由][6]

5. 创建新路由表后，请转到包含 ASE 的子网。 从门户中的列表处选择路由表。 保存更改后，会看到子网旁显示有 NSG 和路由。

    ![NSG 和路由][7]

## <a name="service-endpoints"></a>服务终结点 ##

可以通过服务终结点将多租户服务的访问权限限制给一组 Azure 虚拟网络和子网。 若要详细了解服务终结点，可参阅[虚拟网络服务终结点][serviceendpoints]文档。 

在资源上启用服务终结点时，有些已创建路由的优先级高于所有其他路由。 如果将服务终结点与强制隧道 ASE 配合使用，则 Azure SQL 和 Azure 存储管理流量不会通过强制隧道进行重定向。 

在包含 Azure SQL 实例的子网上启用服务终结点时，所有与该子网有连接的 Azure SQL 实例必定会启用服务终结点。 如果需要从同一子网访问多个 Azure SQL 实例，则不能在一个 Azure SQL 实例上启用服务终结点，而在另一个实例上不启用。 Azure 存储的表现与 Azure SQL 不同。 对 Azure 存储启用服务终结点时，可以锁定从子网对该资源进行的访问，但仍可访问其他 Azure 存储帐户，即使这些帐户未启用服务终结点。  

![服务终结点][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
