---
title: "混合连接概述 | Microsoft Docs"
description: "了解混合连接、安全、TCP 端口和受支持的配置。 MABS，WABS。"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 397a922bf3bf4c39c89f5f69015de4942bda0af9


---
# <a name="hybrid-connections-overview"></a>混合连接概述
混合连接的简介，其中列出了受支持的配置和所需的 TCP 端口。

## <a name="what-is-a-hybrid-connection"></a>什么是混合连接
混合连接是 Azure BizTalk 服务的一项功能。 混合连接提供了将 Azure 应用服务中的 Web 应用（之前称为网站）和移动应用功能（之前称为移动服务）连接到防火墙后的本地资源的一种简便方式。

![混合连接][HCImage]

混合连接的优势包括：

* Web 应用和移动应用可安全地访问现有本地数据和服务。
* 多个 Web 应用或移动应用可通过共享一个混合连接以访问本地资源。
* 只需最小的 TCP 端口即可访问网络。
* 使用混合连接的应用程序仅能访问通过混合连接发布的特定本地资源。
* 可以连接到任何使用静态 TCP 端口的本地资源，例如 SQL Server、MySQL、HTTP Web API 和大多数自定义 Web 服务。
  
  > [!NOTE]
  > 当前不支持使用动态端口（例如 FTP 被动模式或扩展被动模式）的基于 TCP 的服务。 也不支持 LDAP。 虽然 LDAP 使用的是静态 TCP 端口，也它可以使用 UDP。 因此，它不受支持。
  > 
  > 
* 可以与 Web 应用支持的所有框架（.NET、PHP、Java、Python 和 Node.js）和移动应用支持的所有框架（Node.js、.NET）配合使用。
* Web 应用和移动应用可轻松地访问本地资源，如 Web 应用或移动应用就位于本地网络上一样。 例如，在本地使用的同一个连接字符串也可以在 Azure 中使用。

混合连接还为企业管理员提供了对通过混合连接访问的企业资源的控制和可见性，包括：

* 通过使用“组策略”设置，管理员可允许在网络上进行混合连接，还可以指定混合应用程序可访问的资源。
* 企业网络上的事件和审核日志为通过混合连接访问的资源提供了可见性。

## <a name="example-scenarios"></a>示例方案
混合连接支持以下框架和应用程序组合：

* SQL Server 的 .NET framework 访问
* 使用 WebClient 的 HTTP/HTTPS 服务的 .NET framework 访问
* SQL Server、MySQL 的 PHP 访问
* SQL Server、MySQL 和 Oracle 的 Java 访问
* HTTP/HTTPS 服务的 Java 访问

使用混合连接访问本地 SQL Server 时，请注意以下几点：

* 必须将 SQL Express 命名实例配置为使用静态端口。 默认情况下，SQL Express 命名实例使用的是动态端口。
* 尽管 SQL Express 默认实例使用的是静态端口，但必须启用 TCP。 默认情况下，未启用 TCP。
* 在使用群集或可用性组时，目前不支持 `MultiSubnetFailover=true` 模式。
* 目前不支持 `ApplicationIntent=ReadOnly` 。
* 可能需要 SQL 身份验证作为 Azure 应用程序和本地 SQL Server 支持的端到端授权方法。

## <a name="security-and-ports"></a>安全性和端口
混合连接使用共享访问签名 (SAS) 授权，确保从 Azure 应用程序和本地混合连接管理器到混合连接的连接安全。 为应用程序和本地混合连接管理器创建了单独的连接密钥。 可独立滚动和撤销这些连接密钥。

混合连接为应用程序和本地混合连接管理器提供了无缝和安全的密钥分发。

请参阅 [创建和管理混合连接](integration-hybrid-connection-create-manage.md)。

*应用程序授权独立于混合连接*。 可以使用任何适当的授权方法。 授权方法取决于 Azure 云和本地组件之间支持的端到端授权方法。 例如，Azure 应用程序访问本地 SQL Server。 在此方案中，SQL 授权可以是支持端到端的授权方法。

#### <a name="tcp-ports"></a>TCP 端口
混合连接仅需来自专用网络的出站 TCP 或 HTTP 连接。 不需要打开任何防火墙端口或更改网络外围配置以允许任何入站连接连入网络。

混合连接使用下列 TCP 端口：

| 端口 | 为何需要 |
| --- | --- |
| 9350 - 9354 |这些端口用于数据传输。 服务总线中继管理器探测端口 9350 以确定 TCP 连接是否可用。 如果可用，则假定端口 9352 也可用。 数据流量会通过端口 9352。 <br/><br/>允许到这些端口的出站连接。 |
| 5671 |端口 9352 用于数据通信时，端口 5671 用作控制通道。 <br/><br/>允许到此端口的出站连接。 |
| 80、443 |这些端口用于对 Azure 进行一些数据请求。 此外，如果端口 9352 和 5671 不可用，*则*将端口 80 和 443 作为用于数据传输和通道控制的回退端口。<br/><br/>允许到这些端口的出站连接。 <br/><br/>**注意**：不建议使用这些回退端口代替其他的 TCP 端口。 HTTP/WebSocket 用作数据通道的协议，而不是本机 TCP。 它可能会导致性能降低。 |

## <a name="next-steps"></a>后续步骤
[Create and manage Hybrid Connections](integration-hybrid-connection-create-manage.md)<br/>
[将 Azure Web 应用连接到本地资源](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[从 Azure Web 应用连接到本地 SQL Server](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>

## <a name="see-also"></a>另请参阅
[REST API for Managing BizTalk Services on Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)（用于在 Microsoft Azure 上管理 BizTalk 服务的 REST API）
[BizTalk 服务：版本图表](biztalk-editions-feature-chart.md)<br/>
[使用 Azure 门户创建 BizTalk 服务](biztalk-provision-services.md)<br/>
[BizTalk 服务：“仪表板”、“监视”和“缩放”选项卡](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png



<!--HONumber=Nov16_HO2-->


