---
title: 混合连接
description: 了解如何在 Azure 应用服务中创建混合连接来访问不同网络中的资源。
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/08/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 8c8b71e4be11a886da3e0889add0a3cdc19a56c3
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962411"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure 应用服务混合连接

混合连接既是 Azure 中的一个服务，也是 Azure 应用服务中的一项功能。 作为服务，它的用途和功能超越了应用服务中使用的功能。 若要详细了解混合连接及其在应用服务外部的用途，请参阅 [Azure 中继混合连接][HCService]。

在应用服务中，混合连接可用于访问任何网络中的应用程序资源，这些网络可以通过端口 443 对 Azure 进行出站调用。 混合连接提供从应用到 TCP 终结点的访问权限，而不会启用新的访问应用的方式。 在应用服务中使用时，每个混合连接与单个 TCP 主机和端口组合相关联。 这样，应用就可以访问任何 OS 上的资源，前提是它是 TCP 终结点。 混合连接功能不知道、也不关心应用程序协议或者要访问的内容是什么。 它只提供网络访问。  

## <a name="how-it-works"></a>工作原理 ##
混合连接要求将中继代理部署到可以同时到达所需终结点和 Azure 的位置。 中继代理混合连接管理器 (HCM) 通过端口 443 调用 Azure 中继。 在 Web 应用站点上，应用服务基础结构还代表应用程序连接到 Azure 中继。 通过已联接的连接，应用可以访问所需的终结点。 连接使用 TLS 1.2 来确保安全，使用共享访问签名 (SAS) 密钥进行身份验证和授权。    

![混合连接高级别流示意图][1]

如果应用发出了与配置的混合连接终结点匹配的 DNS 请求，则会通过混合连接重定向出站 TCP 流量。  

> [!NOTE]
> 这意味着，始终应该尽量为混合连接使用 DNS 名称。 如果终结点使用 IP 地址，某些客户端软件不会执行 DNS 查找。 
>

### <a name="app-service-hybrid-connection-benefits"></a>应用服务混合连接的优势 ###

混合连接功能提供许多优势，包括：

- 应用可以访问本地系统和服务。
- 该功能不需要可访问 Internet 的终结点。
- 设置过程快速而轻松。 无需网关
- 每个混合连接与单个“主机:端口”组合匹配，这非常有利于安全性。
- 通常不需要在防火墙中开放端口。 连接全部是通过标准 Web 端口建立的。
- 由于该功能在网络级别运行，它并不知道应用使用的语言以及终结点使用的技术。
- 可以通过单个应用使用它在多个网络中提供访问。 
- 它在 Windows 应用正式版和 Linux 应用预览版中受支持。

### <a name="things-you-cannot-do-with-hybrid-connections"></a>混合连接无法提供的功能 ###

无法通过混合连接执行的操作包括：

- 装载驱动器。
- 使用 UDP。
- 访问使用动态端口（例如 FTP 被动模式或扩展被动模式）的基于 TCP 的服务。
- 支持 LDAP，因为它可能需要 UDP。
- 支持 Active Directory，因为无法将应用服务辅助角色加入域。 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>在应用中添加和创建混合连接 ##

若要创建混合连接，请转到 [Azure 门户][portal]，并选择应用。 选择“网络” > “配置混合连接终结点”。  在此处，可以看到为应用配置的混合连接。  

![混合连接列表的屏幕截图][2]

若要添加新的混合连接，请选择“[+] 添加混合连接”。  此时会显示已创建的混合连接的列表。 要将其中的一个或多个混合连接添加到应用，请选择所需的混合连接，然后选择“添加选定的混合连接”。  

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
| 基本 | 每个计划 5 个 |
| 标准 | 每个计划 25 个 |
| PremiumV2 | 每个应用 200 个 |
| 隔离 | 每个应用 200 个 |

应用服务计划 UI 会显示使用了多少个混合连接以及是由哪些应用使用的。  

![应用服务计划属性的屏幕截图][6]

选择该混合连接可查看详细信息。 可以看到应用视图中显示的所有信息。 还可以查看同一计划中还有其他多少个应用正在使用该混合连接。

可在一个应用服务计划中使用的混合连接终结点数目有限制。 但是，所用的每个混合连接可在该计划中任意数目的应用中使用。 例如，在一个应用服务计划下的 5 个单独应用中共同使用的单个混合连接，仅算作 1 个混合连接。

### <a name="pricing"></a>定价 ###

除了要求使用应用服务计划 SKU 外，使用混合连接还需要额外付费。 需要为混合连接使用的每个侦听器付费。 侦听器是混合连接管理器。 如果你有由两个混合连接管理器支持的五个混合连接，则将有 10 个侦听器。 有关详细信息，请参阅[服务总线定价][sbpricing]。

## <a name="hybrid-connection-manager"></a>混合连接管理器 ##

混合连接功能要求在网络中安装一个中继代理用于托管混合连接终结点。 该中继代理称为混合连接管理器 (HCM)。 若要下载 HCM，请在 [Azure 门户][portal]上的应用中，选择“网络” > “配置混合连接终结点”。   

此工具可在 Windows Server 2012 和更高版本上运行。 HCM 作为服务运行，并且在端口 443 上出站连接到 Azure 中继。  

安装 HCM 后，可以运行 HybridConnectionManagerUi.exe 来使用该工具的 UI。 此文件位于混合连接管理器的安装目录中。 在 Windows 10 上，也可以在搜索框中搜索“混合连接管理器 UI”即可。  

![混合连接管理器的屏幕截图][7]

启动 HCM UI 时，出现的第一个界面是一个表格，其中列出了为此 HCM 实例配置的所有混合连接。 如果想要进行任何更改，请先在 Azure 中完成身份验证。 

要将一个或多个混合连接添加到 HCM，请执行以下操作：

1. 启动 HCM UI。
2. 选择“配置另一个混合连接”。
![配置新混合连接的屏幕截图][8]

1. 使用 Azure 帐户登录，以使你的订阅可以使用混合连接。 除此之外，HCM 不会继续使用你的 Azure 帐户。 
1. 选择订阅。
1. 选择 HCM 要中继的混合连接。
![混合连接的屏幕截图][9]

1. 选择“保存” 。

现在，可以看到已添加的混合连接。 还可以选择配置的混合连接查看详细信息。

![混合连接详细信息的屏幕截图][10]

若要支持配置的混合连接，HCM 需要：

- 通过端口 443 对 Azure 进行 TCP 访问。
- 对混合连接终结点进行 TCP 访问。
- 能够在终结点主机和服务总线命名空间中执行 DNS 查找。

> [!NOTE]
> Azure 中继的连接性依赖于 Web 套接字。 此功能仅适用于 Windows Server 2012 或更高版本。 因此，低于 Windows Server 2012 的版本将不支持 HCM。
>

### <a name="redundancy"></a>冗余 ###

每个 HCM 可以支持多个混合连接。 此外，多个 HCM 可以支持任一给定的混合连接。 默认行为是在为任一给定终结点配置的 HCM 之间路由流量。 如果希望从网络建立的混合连接具有高可用性，可在单独的计算机上运行多个 HCM。 中继服务用来将流量分配给各个 HCM 的负载分配算法是随机分配。 

### <a name="manually-add-a-hybrid-connection"></a>手动添加混合连接 ###

若要让订阅外部的某人托管给定混合连接的 HCM 实例，可与他（她）共享该混合连接的网关连接字符串。 可以在 [Azure 门户][portal]的“混合连接”属性中看到网关连接字符串。 要使用该字符串，请在 HCM 中选择“手动输入”，并粘贴网关连接字符串。

![手动添加混合连接][11]

### <a name="upgrade"></a>升级 ###

我们会定期发布混合连接管理器更新来修复问题或提供改进。 当发布升级时，一个弹出窗口将显示在 HCM UI 中。 应用升级将应用所做的更改并重启 HCM。 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>以编程方式将混合连接添加到你的应用 ##

Azure CLI 支持混合连接。 提供的命令可在应用和应用服务计划级别上运行。  应用级命令为：

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

利用应用服务计划命令，你可以设置给定混合连接将使用的密钥。 每个混合连接上都设置了两个密钥，一个主密钥和一个辅助密钥。 可以选择通过以下命令使用主密钥或辅助密钥。 这样，你就可以在需要定期再生成密钥时切换密钥。 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>保护混合连接 ##

任何在基础 Azure 服务总线中继上有足够权限的用户都可以将现有的混合连接添加到应用服务 Web 应用。 这意味着，如果必须阻止他人重复使用这个相同的混合连接（例如，如果目标资源是一项没有设置任何其他的安全措施来防止未经授权的访问的服务，则必须这样做），则必须锁定对 Azure 服务总线中继的访问。

可以通过 `Reader` 访问权限来访问中继的任何人都将能够看到混合连接（在尝试通过 Azure 门户将它添加到 Web 应用时），但却无法添加它，因为缺少检索用于建立中继连接的连接字符串的权限。 若要成功添加混合连接，他们必须具有 `listKeys` 权限 (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`)。 `Contributor` 角色或者任何其他包含此权限（在中继上）的角色都会允许用户使用混合连接并将其添加到自己的 Web 应用。

## <a name="troubleshooting"></a>故障排除 ##

“已连接”状态表示，至少有一个 HCM 配置了该混合连接，且可以访问 Azure。 如果混合连接状态未显示“已连接”，则表示未在任何可访问 Azure 的 HCM 上配置该混合连接。

客户端无法连接到其终结点的主要原因是使用 IP 地址而不是 DNS 名称指定了终结点。 如果应用无法访问所需的终结点，而你使用了 IP 地址，请改为使用在运行 HCM 的主机上有效的 DNS 名称。 另请检查 DNS 名称是否能够在运行 HCM 的主机上正确解析。 确认运行 HCM 的主机是否与混合连接终结点建立了连接。  

在应用服务中，可以通过高级工具 (Kudu) 控制台调用 **tcpping** 命令行工具。 此工具可以告知你是否能够访问 TCP 终结点，但不会告知你是否能够访问混合连接终结点。 在控制台中针对混合连接终结点使用此工具时，只能确认混合连接是否使用了“主机:端口”组合。  

如果你的终结点有命令行客户端，则可以从应用控制台测试连接。 例如，可以使用 curl 测试对 Web 服务器终结点的访问。


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
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/