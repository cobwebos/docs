---
title: Azure Analysis Services 的本地数据网关 |Microsoft Docs
description: 如果 Azure 中的 Analysis Services 服务器要连接到本地数据源，则本地网关是必需的。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0d8960ddd8f617c59d6ac025fafe413256bc5b94
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107600"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>使用本地数据网关连接到本地数据源

本地数据网关提供本地数据源与云中的 Azure Analysis Services 服务器之间的安全数据传输。 除了适用于同一区域中的多个 Azure Analysis Services 服务器以外，最新版本的网关还适用于 Azure 逻辑应用、Power BI、Power Apps 和 Power Automate。 虽然在所有这些服务中安装的网关是相同的，但 Azure Analysis Services 和逻辑应用有一些额外的步骤。

此处提供的信息特定于 Azure Analysis Services 如何与本地数据网关配合使用。 若要详细了解网关的一般信息以及它如何与其他服务一起使用，请参阅[什么是本地数据网关？](/data-integration/gateway/service-gateway-onprem)。

就 Azure Analysis Services 来说，首次安装网关的过程由四个部分组成：

-  下载并运行安装程序 - 这一步会在你组织的计算机上安装网关服务。 还在[租户的](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD 中使用帐户登录到 Azure。 不支持 Azure B2B（来宾）帐户。

- **注册网关** - 在这一步中，指定网关的名称和恢复密钥，然后选择区域，在网关云服务中注册你的网关。 网关资源可以在任何区域中注册，但是建议将它与 Analysis Services 服务器位于同一区域中。 

- **在 Azure 中创建网关资源** - 此步骤在 Azure 中创建网关资源。

- **将网关资源连接到服务器** - 拥有网关资源后，可以开始将服务器连接到该资源。 可以连接多个服务器和其他资源，前提是它们位于同一区域中。

## <a name="installing"></a>安装

针对 Azure Analysis Services 环境进行安装时，必须按[为 Azure Analysis Services 安装和配置本地数据网关](analysis-services-gateway-install.md)中介绍的步骤操作。 本文专门针对 Azure Analysis Services。 它包含在 Azure 中设置本地数据网关资源并将 Azure Analysis Services 服务器连接到该资源所需的其他步骤。

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>连接到不同订阅中的网关资源

建议在服务器所在的订阅中创建 Azure 网关资源。 但是，可以将服务器配置为连接到其他订阅中的网关资源。 在门户中配置现有服务器设置或创建新服务器时，不支持连接到其他订阅中的网关资源，但可以使用 PowerShell 进行配置。 若要了解详细信息，请参阅[将网关资源连接到服务器](analysis-services-gateway-install.md#connect-gateway-resource-to-server)。

## <a name="ports-and-communication-settings"></a>端口和通信设置

网关会创建与 Azure 服务总线之间的出站连接。 它在以下出站端口上进行通信：TCP 443（默认值）、5671、5672、9350 到 9354。  网关不需要入站端口。

可能需要在防火墙中包括数据区域的 IP 地址。 可以下载 [Microsoft Azure 数据中心 IP 列表](https://www.microsoft.com/download/details.aspx?id=56519)。 该列表每周都会进行更新。 Azure 数据中心 IP 列表中列出的 IP 地址使用的是 CIDR 表示法。 若要了解详细信息，请参阅 [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)（无类别域际路由）。

以下是该网关所用的完全限定域名。

| 域名 | 出站端口 | 说明 |
| --- | --- | --- |
| *.powerbi.com |80 |用于下载该安装程序的 HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net、login.live.com、aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |高级消息队列协议 (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |通过 TCP 的服务总线中继上的侦听器（需要 443 来获取访问控制令牌） |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |在 Power BI 服务无法访问网关时用于测试 Internet 连接。 |
| *.microsoftonline-p.com |443 |用于根据配置进行身份验证。 |
| dc.services.visualstudio.com    |443 |由 AppInsights 用来收集遥测数据。 |

## <a name="next-steps"></a>后续步骤 

以下文章包含在本地数据网关常规内容中，该内容适用于网关支持的所有服务：

* [本地数据网关常见问题解答](/data-integration/gateway/service-gateway-onprem-faq)   
* [使用本地数据网关应用](/data-integration/gateway/service-gateway-app)   
* [租户级管理](/data-integration/gateway/service-gateway-tenant-level-admin)
* [配置代理设置](/data-integration/gateway/service-gateway-proxy)   
* [调整通信设置](/data-integration/gateway/service-gateway-communication)   
* [配置日志文件](/data-integration/gateway/service-gateway-log-files)   
* [故障排除](/data-integration/gateway/service-gateway-tshoot)
* [监视和优化网关性能](/data-integration/gateway/service-gateway-performance)