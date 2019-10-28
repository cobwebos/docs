---
title: 混合连接 - Azure 应用服务 | Microsoft Docs
description: 如何创建混合连接并使用它来访问不同网络中的资源
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 72874e7b96e2ec8909a325b5ae598b900ebe8079
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791894"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure 应用服务混合连接 #

混合连接既是 Azure 中的一个服务，也是 Azure 应用服务中的一项功能。 作为服务，它的用途和功能超越了应用服务中使用的功能。 若要了解有关应用服务外混合连接及其使用情况的详细信息，请参阅[Azure 中继混合连接][HCService]。

在应用服务中，混合连接可用于访问其他网络中的应用程序资源。 使用混合连接可以从应用访问应用程序终结点。 它无法提供可用于访问应用程序的替代功能。 在应用服务中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这意味着，混合连接终结点可以位于任何操作系统和任何应用程序上，前提是能够访问 TCP 侦听端口。 混合连接功能不知道、也不关心应用程序协议或者要访问的内容是什么。 它只提供网络访问。  


## <a name="how-it-works"></a>如何运作 ##
混合连接功能由针对 Azure 服务总线中继发出的两个出站调用组成。 从应用服务中运行应用的主机上的某个库建立了一个连接。 另外，还建立了从混合连接管理器 (HCM) 到服务总线中继的连接。 HCM 是部署在网络中的一种中继服务，用于托管你正在尝试访问的资源。 

通过这两个连接，应用可与 HCM 另一端的固定“主机:端口”组合建立 TCP 隧道。 连接使用 TLS 1.2 来确保安全，使用共享访问签名 (SAS) 密钥进行身份验证和授权。    

![混合连接高级别流示意图][1]

如果应用发出了与配置的混合连接终结点匹配的 DNS 请求，则会通过混合连接重定向出站 TCP 流量。  

> [!NOTE]
> 这意味着，始终应该尽量为混合连接使用 DNS 名称。 如果终结点使用 IP 地址，某些客户端软件不会执行 DNS 查找。 
>

### <a name="app-service-hybrid-connection-benefits"></a>应用服务混合连接的优势 ###

混合连接功能提供许多优势，包括：

- 应用可以访问本地系统和服务。
- 该功能不需要可访问 Internet 的终结点。
- 设置过程快速而轻松。 
- 每个混合连接与单个“主机:端口”组合匹配，这非常有利于安全性。
- 通常不需要在防火墙中开放端口。 连接全部是通过标准 Web 端口建立的。
- 由于该功能在网络级别运行，它并不知道应用使用的语言以及终结点使用的技术。
- 可以通过单个应用使用它在多个网络中提供访问。 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>混合连接无法提供的功能 ###

混合连接无法实现以下操作：

- 装载驱动器。
- 使用 UDP。
- 访问使用动态端口（例如 FTP 被动模式或扩展被动模式）的基于 TCP 的服务。
- 支持 LDAP，因为它可能需要 UDP。
- 支持 Active Directory，因为你无法域加入应用服务辅助角色。

### <a name="prerequisites"></a>必备组件 ###
 - Windows 应用服务是必需的。 它仅在 Windows 中可用。  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>在应用中添加和创建混合连接 ##

若要创建混合连接，请跳到[Azure 门户][portal]并选择应用。 选择“网络” > “配置混合连接终结点”。 此处显示了为应用配置的混合连接。  

![混合连接列表的屏幕截图][2]

要添加新的混合连接，请选择“[+]添加混合连接”。  此时会显示已创建的混合连接的列表。 要将其中的一个或多个混合连接添加到应用，请选择所需的混合连接，然后选择“添加选定的混合连接”。  

![混合连接门户的屏幕截图][3]

如果想要创建新的混合连接，请选择“创建新的混合连接”。 指定： 

- 混合连接名称。
- 终结点主机名。
- 终结点端口。
- 要使用的服务总线命名空间。

![“创建新的混合连接”对话框屏幕截图][4]

每个混合连接已绑定到服务总线命名空间，每个服务总线命名空间在 Azure 区域中。 请尽量使用应用所在的同一区域中的服务总线命名空间，这一点非常重要，目的是避免网络造成的延迟。

如果想要从应用中删除混合连接，请右键单击该混合连接，并选择“断开连接”。  

将混合连接添加到应用后，选择该混合连接即可查看其详细信息。 

![“混合连接详细信息”屏幕截图][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>在 Azure 中继门户中创建混合连接 ###

除了使用应用内部的门户体验以外，还可以在 Azure 中继门户中创建混合连接。 要使混合连接可供应用服务使用，必须：

* 要求客户端授权。
* 提供一个名为 endpoint 的元数据项，其中包含“主机:端口”的组合作为值。

## <a name="hybrid-connections-and-app-service-plans"></a>混合连接和应用服务计划 ##

应用服务混合连接只能在“基本”、“标准”、“高级”和“隔离”定价 SKU 中使用。 定价计划没有相关的限制。  

| 定价计划 | 在计划中可以使用的混合连接数 |
|----|----|
| 基本 | 5 |
| 标准版 | 25 |
| 高级 | 200 |
| 独立 | 200 |

应用服务计划 UI 会显示混合连接的用量以及由哪些应用使用。  

![应用服务计划属性的屏幕截图][6]

选择该混合连接可查看详细信息。 可以看到应用视图中显示的所有信息。 还可以查看同一计划中还有其他多少个应用正在使用该混合连接。

可在一个应用服务计划中使用的混合连接终结点数目有限制。 但是，所用的每个混合连接可在该计划中任意数目的应用中使用。 例如，在一个应用服务计划下的 5 个单独应用中共同使用的单个混合连接，仅算作 1 个混合连接。

### <a name="pricing"></a>价格 ###

除了存在应用服务计划 SKU 要求，使用混合连接还会产生额外的成本。 混合连接使用的每个侦听器都要收费。 侦听器是混合连接管理器。 如果你有 5 个混合连接，它们由 2 个混合连接管理器支持，则总计是 10 个侦听器。 有关详细信息，请参阅[服务总线定价][sbpricing]。

## <a name="hybrid-connection-manager"></a>混合连接管理器 ##

混合连接功能要求在网络中安装一个中继代理用于托管混合连接终结点。 该中继代理称为混合连接管理器 (HCM)。 若要从[Azure 门户][portal]中的应用下载 HCM，请选择 "**网络**" > **配置混合连接终结点**。  

此工具可在 Windows Server 2012 和更高版本上运行。 HCM 作为服务运行，并在端口 443 上出站连接到 Azure 中继。  

安装 HCM 后，可以运行 HybridConnectionManagerUi.exe 来使用该工具的 UI。 此文件位于混合连接管理器的安装目录中。 在 Windows 10 上，也可以在搜索框中搜索“混合连接管理器 UI”即可。  

![混合连接管理器的屏幕截图][7]

启动 HCM UI 时，出现的第一个界面是一个表格，其中列出了为此 HCM 实例配置的所有混合连接。 如果想要进行任何更改，请先在 Azure 中完成身份验证。 

要将一个或多个混合连接添加到 HCM，请执行以下操作：

1. 启动 HCM UI。
2. 选择“配置另一个混合连接”。
![配置新混合连接的屏幕截图][8]

1. 使用 Azure 帐户登录，以使你的混合连接适用于你的订阅。 HCM 不会继续使用你的 Azure 帐户。 
1. 选择订阅。
1. 选择 HCM 要中继的混合连接。
![混合连接的屏幕截图][9]

1. 选择“保存”。

现在，可以看到已添加的混合连接。 还可以选择配置的混合连接查看详细信息。

![混合连接详细信息的屏幕截图][10]

若要支持配置的混合连接，HCM 需要：

- 通过端口 443 对 Azure 进行 TCP 访问。
- 对混合连接终结点进行 TCP 访问。
- 能够在终结点主机和服务总线命名空间中执行 DNS 查找。

> [!NOTE]
> Azure 中继的连接性依赖于 Web 套接字。 此功能仅适用于 Windows Server 2012 或更高版本。 因此，低于 Windows Server 2012 的版本将不支持 HCM。
>

### <a name="redundancy"></a>冗余性 ###

每个 HCM 可以支持多个混合连接。 此外，多个 HCM 可以支持任一给定的混合连接。 默认行为是在为任一给定终结点配置的 HCM 之间路由流量。 如果希望从网络建立的混合连接具有高可用性，可在单独的计算机上运行多个 HCM。 中继服务用于将流量分配到 HCM 的负载分配算法是随机分配。 

### <a name="manually-add-a-hybrid-connection"></a>手动添加混合连接 ###

若要让订阅外部的某人托管给定混合连接的 HCM 实例，可与他（她）共享该混合连接的网关连接字符串。 可以在[Azure 门户][portal]的混合连接属性中查看网关连接字符串。 要使用该字符串，请在 HCM 中选择“手动输入”，并粘贴网关连接字符串。

![手动添加混合连接][11]

### <a name="upgrade"></a>升级 ###

混合连接管理器会定期更新，以解决问题或提供改进。 发布升级时，HCM UI 中将显示一个弹出窗口。 应用升级时将一并应用更改并重启 HCM。 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>以编程方式向应用添加混合连接 ##

下面提到的 API 可直接用于管理连接到应用的混合连接。 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

与混合连接关联的 JSON 对象如下所示：

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

使用此信息的一种方法是使用 armclient，可从[armclient][armclient] GitHub 项目获取。 下面是将预先存在的混合连接附加到应用的示例。 按照上述架构创建一个 JSON 文件，如：

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

要使用此 API，需要发送密钥和中继资源 ID。 如果使用文件名 hctest.json 保存了信息，请发出此命令以将混合连接附加到你的应用： 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>故障排除 ##

“已连接”状态表示，至少有一个 HCM 配置了该混合连接，且可以访问 Azure。 如果混合连接状态未显示“已连接”，则表示未在任何可访问 Azure 的 HCM 上配置该混合连接。

客户端无法连接到其终结点的主要原因是使用 IP 地址而不是 DNS 名称指定了终结点。 如果应用无法访问所需的终结点，而你使用了 IP 地址，请改为使用在运行 HCM 的主机上有效的 DNS 名称。 另请检查 DNS 名称是否能够在运行 HCM 的主机上正确解析。 确认运行 HCM 的主机是否与混合连接终结点建立了连接。  

在应用服务中，可以从 "高级工具" （Kudu）控制台调用**tcpping**命令行工具。 此工具可以告知你是否能够访问 TCP 终结点，但不会告知你是否能够访问混合连接终结点。 在控制台中针对混合连接终结点使用此工具时，只能确认混合连接是否使用了“主机:端口”组合。  

如果你有终结点的命令行客户端，则可以从应用控制台测试连接。 例如，你可以使用卷来测试对 web 服务器终结点的访问。

## <a name="biztalk-hybrid-connections"></a>BizTalk 混合连接 ##

此功能的早期形式被称为 BizTalk 混合连接。 此功能于 2018 年 5 月 31 日结束并停止操作。 BizTalk 混合连接已从所有应用中删除，无法通过门户或 API 访问。 如果仍在混合连接管理器中配置了这些旧连接，则会看到“已停用”状态且底部显示“生命周期结束”。

![HCM 中的 BizTalk 混合连接][12]


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
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
