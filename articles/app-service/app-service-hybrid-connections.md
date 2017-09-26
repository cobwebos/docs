---
title: "Azure 应用服务混合连接 | Microsoft Docs"
description: "如何创建混合连接并使用它来访问不同网络中的资源"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.contentlocale: zh-cn
ms.lasthandoff: 04/25/2017

---

# <a name="azure-app-service-hybrid-connections"></a>Azure 应用服务混合连接 #

## <a name="overview"></a>概述 ##

混合连接既是 Azure 中的一个服务，也是 Azure 应用服务中的一项功能。  作为服务，它的用途和功能超越了 Azure 应用服务中利用的服务。  若要详细了解混合连接及其在 Azure 应用服务外部的用途，可以从 [Azure 中继混合连接][HCService]着手

在 Azure 应用服务中，混合连接可用于访问其他网络中的应用程序资源。 使用混合连接可以从应用访问应用程序终结点。  它无法提供替代的功能用于访问应用程序。  在应用服务中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。  这意味着，混合连接终结点可以位于任何操作系统和任何应用程序上，前提是设置了 TCP 侦听端口。 混合连接不知道、也不关心应用程序协议或者要访问的内容是什么。  它只提供网络访问。  


## <a name="how-it-works"></a>工作原理 ##
混合连接功能由针对服务总线中继发出的两个出站调用组成。  从应用服务中运行应用的主机上的某个库建立了一个连接，同时，还建立了从混合连接管理器 (HCM) 到服务总线中继的连接。  HCM 是在网络主机中部署的中继服务 

通过这两个连接，应用可与 HCM 另一端的固定“主机:端口”组合建立 TCP 隧道。  连接使用 TLS 1.2 来确保安全，使用 SAS 密钥进行身份验证/授权。    

![][1]

如果应用发出了与配置的混合连接终结点匹配的 DNS 请求，则会通过混合连接重定向出站 TCP 流量。  

> [!NOTE]
> 这意味着，始终应该尽量为混合连接使用 DNS 名称。  如果终结点使用 IP 地址，某些客户端软件不会执行 DNS 查找。
>
>

有两种类型的混合连接：在 Azure 中继中以服务形式提供的新式混合连接，以及旧式 BizTalk 混合连接。  旧式 BizTalk 混合连接在门户中称为经典混合连接。  本文档稍后将提供相关的详细信息。

### <a name="app-service-hybrid-connection-benefits"></a>应用服务混合连接的优势 ###

混合连接功能提供许多优势，包括

- 应用可以安全访问本地系统和服务
- 该功能不需要可访问 Internet 的终结点
- 设置过程快速而轻松  
- 每个混合连接与单个“主机:端口”组合匹配，这非常有利于安全性
- 通常不需要在防火墙中开放端口，因为这些连接都是通过标准 Web 端口建立的出站连接
- 由于该功能在网络级别运行，因此也意味着它并不知道应用使用的语言以及终结点使用的技术
- 可以通过单个应用使用它在多个网络中提供访问 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>混合连接无法提供的功能 ###

混合连接无法提供某些功能，包括：

- 装载驱动器
- 使用 UDP
- 访问使用动态端口（例如 FTP 被动模式或扩展被动模式）的基于 TCP 的服务
- LDAP 支持，因为有时需要 UDP
- Active Directory 支持

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>在应用中添加和创建混合连接 ##

可以通过应用门户或混合连接服务门户创建混合连接。  强烈建议通过应用门户创建要在应用中使用的混合连接。  若要创建混合连接，请转到 [Azure 门户][portal]，然后进入应用的 UI。  选择“网络”>“配置混合连接终结点”。  从此处，可以看到为应用配置的混合连接  

![][2]

若要添加新的混合连接，请单击“添加混合连接”。  打开的 UI 列出了已创建的混合连接。  若要将其中的一个或多个混合连接添加到应用，请单击所需的混合连接，然后单击“添加选定的混合连接”。  

![][3]

如果想要创建新的混合连接，请单击“创建新的混合连接”。  在此处请指定： 

- 终结点名称
- 终结点主机名
- 终结点端口
- 要使用的服务总线命名空间

![][4]

每个混合连接已绑定到服务总线命名空间，每个服务总线命名空间在 Azure 区域中。  请尽量使用应用所在的同一区域中的服务总线命名空间，这一点非常重要，目的是避免网络造成的延迟。

如果想要从应用中删除混合连接，请右键单击该混合连接，然后选择“断开连接”。  

将混合连接添加到 Web 应用后，单击该混合连接即可查看其详细信息。  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>混合连接和应用服务计划 ##

目前，可以创建的混合连接仅限新式混合连接。  这些混合连接只能在“基本”、“标准”、“高级”和“隔离”定价 SKU 中使用。  定价计划没有相关的限制。  

| 定价计划 | 在计划中可以使用的混合连接数 |
|----|----|
| 基本 | 5 |
| 标准 | 25 |
| 高级 | 200 |
| 隔离 | 200 |

由于应用服务计划存在限制，应用服务计划中还提供了 UI 用于显示正在使用的混合连接数以及它们由哪些应用使用。  

![][6]

就像在应用视图中一样，你可以通过单击查看有关混合连接的详细信息。  在此处所示的属性中，不仅可以看到应用视图提供的所有信息，而且还能看到同一应用服务计划中的其他多少个应用正在使用该混合连接。

![][7]

尽管在应用服务计划中可以使用的混合连接终结点数有限制，但所用的每个混合连接可在该应用服务计划中任意数目的应用上使用。  也就是说，如果我在应用服务计划中的 5 个不同应用上使用了 1 个混合连接，仍然只会统计为 1 个混合连接。

除了只能在“基本”、“标准”、“高级”或“隔离”SKU 中使用的混合连接以外，使用其他混合连接会产生额外的成本。  有关混合连接定价的详细信息，请参阅[服务总线定价][sbpricing]。

## <a name="hybrid-connection-manager"></a>混合连接管理器 ##

若要使混合连接正常工作，需要在网络中安装一个中继代理用于托管混合连接终结点。  该中继代理称为混合连接管理器 (HCM)。  可以在 [Azure 门户][portal]中通过应用访问的“网络”>“配置混合连接终结点”UI 下载此工具。  

此工具可在 Windows server 2008 R2 和更高版本的 Windows 上运行。  安装后，HCM 将以服务的形式运行。  此服务基于配置的终结点连接到 Azure 服务总线中继。  从 HCM 建立的连接是与端口 80 和 443 建立的出站连接。    

HCM 提供一个 UI 用于配置连接。  安装 HCM 后，可以通过运行混合连接管理器安装目录中的 HybridConnectionManagerUi.exe 打开该 UI。  在 Windows 10 上，也可以轻松访问该 UI，只需在搜索框中键入“混合连接管理器 UI”即可。  

HCM UI 启动时，出现的第一个界面是一个表格，其中列出了为此 HCM 实例配置的所有混合连接。  如果想要进行任何更改，需要在 Azure 中完成身份验证。 

若要将一个或多个混合连接添加到 HCM，请执行以下操作：

1. 启动 HCM UI
1. 单击“配置另一个混合连接”![][8]

1. 使用 Azure 帐户登录
1. 选择订阅
1. 单击此 HCM 要中继的混合连接 ![][9]

1. 点击“保存”(Save)

此时将会显示已添加的混合连接。  还可以单击配置的混合连接查看其详细信息。

![][10]

要使 HCM 能够支持其上配置的混合连接，需要：

- 通过端口 80 和 443 对 Azure 进行 TCP 访问
- 对混合连接终结点进行 TCP 访问
- 能够在终结点主机和 Azure 服务总线命名空间中执行 DNS 查找

HCM 支持新式混合连接以及旧式 BizTalk 混合连接。

### <a name="redundancy"></a>冗余 ###

每个 HCM 可以支持多个混合连接。  此外，多个 HCM 可以支持任一给定的混合连接。  默认行为是在为任一给定终结点配置的 HCM 之间循环传送流量。  如果你希望从网络建立的混合连接具有高可用性，只需在单独的计算机上实例化多个 HCM。 

### <a name="manually-adding-a-hybrid-connection"></a>手动添加混合连接 ###

如果你想要让订阅外部的某人托管给定混合连接的 HCM 实例，可与他（她）共享该混合连接的网关连接字符串。  在 [Azure 门户][portal]上的混合连接属性中可以看到该字符串。 若要使用该字符串，请在 HCM 中单击“手动配置”按钮，然后粘贴网关连接字符串。


## <a name="troubleshooting"></a>故障排除 ##

如果与运行中的应用程序建立了混合连接并且至少为一个 HCM 配置了混合连接，门户中的状态将显示为“已连接”。  如果不是显示为“已连接”，则意味着你的应用已关闭，或者 HCM 无法通过端口 80 或 443 连接到 Azure。  

客户端无法连接到其终结点的主要原因是使用 IP 地址而不是 DNS 名称指定了终结点。  如果应用无法访问所需的终结点，而你使用了 IP 地址，请改为使用在运行 HCM 的主机上有效的 DNS 名称。  要检查的其他事项包括，DNS 名称是否能够在运行 HCM 的主机上正确解析，以及运行 HCM 的主机是否与混合连接终结点建立了连接。  

可以通过控制台调用应用服务中一个名为 tcpping 的工具。  此工具可以告知你是否能够访问 TCP 终结点，但不会告知你是否能够访问混合连接终结点。  在控制台中针对混合连接终结点使用此工具时，如果 ping 命令成功，结果只会告知你已经为应用配置了一个使用“主机:端口”组合的混合连接。  

## <a name="biztalk-hybrid-connections"></a>BizTalk 混合连接 ##

旧式 BizTalk 混合连接功能已不再能够用于创建 BizTalk 混合连接。  可以继续对应用使用现有的 BizTalk 混合连接，但应该迁移到新服务。  与 BizTalk 版本相比，新服务的优点包括：

- 不需要额外的 BizTalk 帐户
- TLS 版本为 1.2，而 BizTalk 混合连接中的 TCL 版本为 1.0
- 使用 DNS 名称通过端口 80 和 443 进行通信和访问 Azure，而不是使用 IP 地址和其他一系列附加端口。  

若要将 BizTalk 混合连接添加到应用，请在 [Azure 门户][portal]中转到你的应用，然后单击“网络”>“配置混合连接终结点”。  在“经典混合连接”表中，单击“添加经典混合连接”。  此处将会显示 BizTalk 混合连接的列表。  


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


