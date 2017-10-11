---
title: "Azure App Service 混合连接 |Microsoft 文档"
description: "如何创建和访问不同网络中的资源使用混合连接"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service 混合连接 #

## <a name="overview"></a>概述 ##

混合连接是 Azure 中的服务以及 Azure App Service 中的功能。  作为一种服务，它具有使用和功能之外，在 Azure App Service 中利用。  若要了解有关混合连接，你可以从这里，开始在 Azure 应用程序服务的外部其使用情况的详细信息[Azure 中继混合连接][HCService]

在 Azure App Service，混合连接可以用于访问其他网络中的应用程序资源。 它提供从你的应用到应用程序终结点的访问。  它不会启用一种备用的能力，以访问你的应用程序。  由于使用 App Service 中，每个混合连接与关联到单个 TCP 主机和端口组合。  这意味着，混合连接终结点可能会在任何操作系统上，而且任何应用程序提供命中 TCP 侦听端口。 混合连接不知道，也不关心应用程序协议的是你访问。  它只需提供网络访问权限。  


## <a name="how-it-works"></a>工作原理 ##
混合连接功能由两个到服务总线中继的出站调用组成。  没有从主机应用程序服务中运行你的应用程序，其中，则没有从混合连接 Manager(HCM) 到服务总线中继的连接上的库的连接。  HCM 是一种中继服务中承载的网络部署 

通过两个联接连接你的应用程序的固定的主机： 端口组合 TCP 隧道 HCM 的另一端。  连接安全性和身份验证/授权的 SAS 密钥使用 TLS 1.2。    

![][1]

当你的应用程序发出 DNS 请求与配置混合连接终结点匹配时，出站 TCP 流量将重定向的混合连接关闭。  

> [!NOTE]
> 这意味着你应尝试以始终使用混合连接的 DNS 名称。  某些客户端软件不执行 DNS 查找，而是在终结点使用的 IP 地址。
>
>

有两种类型的混合连接，Azure 中继下作为服务提供的新混合连接和较早的 BizTalk 混合连接。  较旧的 BizTalk 混合连接称为经典混合连接在门户中。  没有在本文档后面有关它们的详细信息。

### <a name="app-service-hybrid-connection-benefits"></a>App Service 混合连接的优势 ###

有大量的为混合连接功能包括带来的好处

- 应用程序安全地可以安全地访问在本地系统和服务上
- 该功能不需要 internet 访问的终结点
- 你可以快速轻松地设置  
- 这是一个很好的安全方面的单个主机： 端口组合到匹配的每个混合连接
- 它通常不需要防火墙漏洞如通过标准 web 端口的连接是所有出站
- 因为该功能是也意味着，网络级别不可知的您的应用程序使用的语言和终结点使用的技术
- 它可以用于在从一个单应用的多个网络进行访问 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>与混合连接不能执行的操作 ###

有的一些不能与混合连接，并且它们包括：

- 安装驱动器
- 使用 UDP
- 访问 TCP 基于使用动态端口，例如 FTP 被动模式或扩展被动模式的服务
- LDAP 的支持，因为它有时，需要 UDP
- Active Directory 支持

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>添加并在你的应用程序中创建混合连接 ##

通过应用门户或通过混合连接的服务门户，则可以创建混合连接。  强烈建议你使用应用门户来创建混合连接你想要使用与你的应用。  若要创建混合连接，请转到[Azure 门户][ portal]并转到你的应用程序的 UI。  选择**网络 > 配置混合连接终结点**。  从此处你可以看到与你的应用配置混合连接  

![][2]

若要添加新的混合连接，请单击添加混合连接。  打开用户界面列出你已创建的混合连接。  若要将一个或多个它们添加到你的应用程序中，单击的所需和命中**添加所选的混合连接**。  

![][3]

如果你想要创建新的混合连接，请单击**创建新的混合连接**。  从此处指定: 

- 终结点名称
- 终结点主机名
- 终结点端口
- 你想要使用的服务总线命名空间

![][4]

每个混合连接与 service bus 命名空间，并且每个服务总线命名空间是在 Azure 区域。  务必请尝试并在你以避免引起的网络延迟的应用程序所在的同一区域中使用 service bus 命名空间。

如果你想要从应用删除混合连接，右键单击它并选择**断开连接**。  

一旦混合连接添加到你的 web 应用，你可以在其上查看详细信息，只需单击在其上。  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>混合连接和 App Service 计划 ##

你现在可以进行的仅混合连接是新的混合连接。  它们只是在基本、 标准、 高级和独立的定价 Sku 中可用。  没有绑定到定价计划的限制。  

| 定价计划 | 在计划中可用的混合连接数 |
|----|----|
| 基本 | 5 |
| Standard | 25 |
| 高级 | 200 |
| 隔离 | 200 |

由于 App Service 计划的限制，此外还有 UI 中显示多少混合连接都在使用 App Service 计划以及哪些应用程序。  

![][6]

就像应用程序视图后，你可以查看详细信息上混合连接通过单击它。  此处显示的属性中可以看到你必须在应用程序视图的所有信息，但还可以都查看在相同的 App Service 计划中的多少其他应用程序正在使用该混合连接。

![][7]

虽然可以在 App Service 计划中使用的混合连接终结点的数量限制，使用每个混合连接可在任意数量的 App Service 计划中的应用。  这是说，如果我遇到了 1 我在我的 App Service 计划中使用 5 个单独的应用中的混合连接，这是仍然只是 1 个混合连接。

没有到除了仅在基本、 标准、 高级或独立 SKU 中可用的混合连接需要额外的成本。  为混合连接定价的详细信息请转到此处：[服务总线定价][sbpricing]。

## <a name="hybrid-connection-manager"></a>混合连接管理器 ##

按顺序为混合连接起作用，您需要承载你的混合连接终结点的网络中的中继代理。  该中继代理称为混合连接管理器 (HCM)。  此工具可以从下载**网络 > 配置混合连接终结点**可从你的应用程序的 UI [Azure 门户][portal]。  

在 Windows server 2008 R2 和更高版本的 Windows 上运行此工具。  一次作为服务安装 HCM 运行。  此服务连接到基于配置的终结点的 Azure 服务总线中继。  从 HCM 的连接是出站到端口 80 和 443。    

HCM 具有 UI 对其进行配置。  HCM 安装后你可以通过混合连接管理器安装目录中运行位于 HybridConnectionManagerUi.exe 显示 UI。  也很容易达到 Windows 10 上通过键入*混合连接管理器 UI*您的搜索框中。  

HCM UI 启动时，你会看到第一件事是列出的所有与此实例 HCM 配置混合连接的表。  如果你想要进行任何更改，需要向 Azure 进行身份验证。 

若要将一个或多个混合连接添加到你 HCM:

1. 启动 HCM UI
1. 单击配置另一个混合连接![][8]

1. 使用你的 Azure 帐户登录
1. 选择订阅
1. 单击所需此 HCM 到中继的混合连接![][9]

1. 单击保存

此时，你将看到你添加的混合连接。  你还可以单击配置的混合连接上，查看有关连接详细信息。

![][10]

若要能够支持混合连接配置了你 HCM，它需要：

- 通过端口 80 和 443 TCP 对 Azure 的访问权限
- 对混合连接终结点的 TCP 访问
- 能够执行 DNS 查找 ups 终结点宿主和 azure servicebus 命名空间

HCM 支持新的混合连接以及较旧的 BizTalk 混合连接。

### <a name="redundancy"></a>冗余 ###

每个 HCM 可以支持多个混合连接。  此外，任何给定的混合连接可以支持多个 HCMs。  在为任何给定的终结点配置 HCMs 情况下，默认行为是轮循机制流量状态。  如果你想针对您的混合连接从网络高可用性，只需实例化在单独的计算机的多个 HCMs。 

### <a name="manually-adding-a-hybrid-connection"></a>手动添加混合连接 ###

如果你想有人在你的订阅以承载给定的混合连接的 HCM 实例之外，你可以与他们共享的混合连接的网关连接字符串。  中的混合连接的属性中，可以看到此[Azure 门户][portal]。 若要使用该字符串，请单击**手动配置**HCM 按钮，然后粘贴网关连接字符串中。


## <a name="troubleshooting"></a>疑难解答 ##

如果运行的应用程序设置为混合连接，没有至少一个已配置，该混合连接的 HCM 则状态将为**已连接**在门户中。  如果未说明是**已连接**则这意味着，你的应用程序已关闭或者是你 HCM 无法出连接到端口 80 或 443 上的 Azure。  

客户端无法连接到其终结点的主要原因是因为已指定的终结点，而不 DNS 名称中使用的 IP 地址。  如果你的应用程序无法访问所需终结点，并且你使用的 IP 地址，切换到使用主机有效的 DNS 名称 HCM 正在其中运行。  要检查的其他事项是正确的 DNS 名称解析 HCM 正在其中运行的主机上，并且没有从主机 HCM 正在其中运行到混合连接终结点连接。  

在可以从调用 tcpping 控制台调用 App Service 中没有一种工具。  如果你有权访问的 TCP 终结点，但不会告诉你如果你有权访问的混合连接终结点，此工具可以告诉你。  针对混合连接终结点的控制台中使用时，成功 ping 将仅告诉你具有配置为应用程序使用该主机： 端口组合的混合连接。  

## <a name="biztalk-hybrid-connections"></a>BizTalk 混合连接 ##

关闭已关闭的较旧的 BizTalk 混合连接功能进一步创建提供 BizTalk 混合连接。  你可以继续使用你的应用程序使用预先存在的 BizTalk 混合连接，但应将迁移到新的服务。  在相对于 BizTalk 版本新的服务的优点包括：

- 没有其他 BizTalk 帐户是必需的
- TLS 是一种而不是如下所示 BizTalk 混合连接的 1.0 1.2
- 通信是通过端口 80 和 443 使用的 DNS 名称来访问 Azure，而不是 IP 地址和范围的附加其他端口。  

若要向应用添加 BizTalk 混合连接，请转到你的应用程序[Azure 门户][ portal]单击**网络 > 配置混合连接终结点**。  经典混合连接表中单击**添加经典混合连接**。  从此处你将看到你的 BizTalk 混合连接的列表。  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/

