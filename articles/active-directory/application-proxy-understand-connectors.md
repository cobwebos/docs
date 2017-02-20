---
title: "了解 Azure AD 应用程序代理连接器 | Microsoft 文档"
description: "介绍有关 Azure AD 应用程序代理连接器的基础知识。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 36e737ebc3451a190e99dc2bc91ef4242d2f573e
ms.openlocfilehash: e9dfe8ad62dfa0eec810ecdeeddadbecc25b9163


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>了解 Azure AD 应用程序代理连接器

本文介绍连接器，它是 Azure AD 应用程序代理中的一个秘密武器。 连接器的形式简单、易于部署和维护，且功能超强。

> [!NOTE]
> 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。
> 
> 

## <a name="what-are-azure-ad-application-proxy-connectors"></a>Azure AD 应用程序代理连接器是什么？
在网络中安装一个名为连接器的 Windows Server 服务后，应用程序代理便开始工作。 可以根据高可用性和可伸缩性的需要安装连接器。 一开始可以安装一个，以后可根据需要添加。 每次安装连接器后，它会添加到为租户提供服务的连接器池中。

建议不要在应用程序服务器本身上安装连接器；不过这种做法是可行的，尤其是在小型部署中。

无需删除不再使用的连接器。 当连接器运行时，它会在连接到服务时保持活动状态。 未使用的连接器标记为_非活动_状态，保持这种状态 10 天后将被删除。 

有关解决 Azure AD 连接问题的信息，请参阅 [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems)（如何排查 Azure AD 应用程序代理连接问题）。 

## <a name="what-are-the-cloud-rules-for-connectors"></a>连接器的云规则是什么？
连接器和服务负责处理所有的高可用性任务。 可以动态添加或删除这些连接器和服务。 每当有新请求抵达时，该请求将路由到当前可用的连接器之一。 如果某个连接器暂时不可用，它不会对此流量做出响应。

连接器是无状态的，计算机上不包含连接器的配置数据，而只保留连接器与服务设置之间的关联，以及连接器与用来对它进行身份验证的证书之间的关联。 连接器在连接到服务时，将提取全部所需的配置数据，并每隔几分钟刷新这些数据。
此外，它们还会轮询服务器，确定是否有更高版本的连接器。 如果找到了更高的版本，连接器将更新自身。

可以在运行监视器的计算机中使用事件日志和性能计数器或者在云中使用“连接器状态”页来监视连接器，如下所示。

 ![AzureAD 应用程序代理连接器](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

## <a name="all-networking-is-outbound"></a>所有网络流量都是出站流量
连接器只发送出站请求，因此连接始终由连接器发起。 无需打开入站端口，因为一旦建立会话，流量就会双向流动。

出站流量将依次发送到应用程序代理服务和发布的应用程序。 发往服务的流量将依次发送到 Azure 数据中心和多个不同的端口号。 有关详细信息，请参阅[在 Azure 门户中启用应用程序代理](active-directory-application-proxy-enable.md)。

由于只会发生出站流量，因此不需要在连接器之间设置负载均衡，或者通过防火墙配置入站访问。

有关配置出站防火墙规则的信息，请参阅 [Work with existing on-premise Proxy servers](application-proxy-working-with-proxy-servers.md)（使用现有的本地代理服务器）。

## <a name="network-security"></a>网络安全

可将连接器安装在网络中允许连接器向服务和后端应用程序发送请求的任意位置。 如果将连接器安装在企业网络中、外围网络（外围安全区域）中，甚至是云中运行的可访问应用的虚拟机上，它们都可正常工作。

外围网络部署通常更复杂。 但是，将连接器部署在外围网络中的原因之一是可以利用外围网络中运行的组件可用的其他基础结构，例如后端应用程序负载均衡器和/或入侵检测系统等安全控制机制。

## <a name="domain-joining"></a>域加入

连接器可在未加入域的计算机上运行。 但是，如果选择对使用 Windows 集成身份验证 (IWA) 的应用程序使用单一登录 (SSO)，则需要在已加入域的计算机上运行。 

在这种情况下，必须将连接器计算机加入到可代表已发布应用程序的相关用户执行 [Kerberos](https://web.mit.edu/kerberos) 约束委派的域。

还可以将连接器加入到具有部分信任的域或林，或者加入到只读的域控制器 (RODC)。

## <a name="connectors-on-hardened-environments"></a>强化的环境中的连接器

在大多数情况下，连接器部署相当直截了当，无需经过特殊的配置。 但是，应该考虑到一些独特的条件：

* 限制出站流量的组织必须遵循本文中的说明打开所需的端口。
* 符合 FIPS 规范的计算机可能需要更改其配置才能允许连接器服务、连接器更新程序服务及其安装程序在该计算机上生成和存储证书。
* 根据网络请求发出过程锁定其环境的组织必须确保启用这两个连接器服务，以访问全部所需的端口和 IP。
* 在某些情况下，出站正向代理可能会中断双向证书身份验证，导致通信失败。

## <a name="all-connectors-are-created-almost-equal"></a>创建的所有连接器几乎都是相同的

系统假设所有连接器彼此相同，每个传入请求可以抵达每个连接器。 这意味着，所有这些连接器应该使用相同的网络连接和 [Kerberos](https://web.mit.edu/kerberos) 设置。

连接器与服务之间的所有通信受客户端证书的保护，该证书在颁发后安装在连接器计算机上。 有关续订连接器证书的信息，请参阅[在 Azure 门户中启用应用程序代理](active-directory-application-proxy-enable.md)。

## <a name="connector-authentication"></a>连接器身份验证

为了提供安全服务，连接器必须向服务执行身份验证，而服务必须向连接器执行身份验证。 当连接器发起连接时，将使用客户端和服务器证书完成这种身份验证。 这样，管理员的用户名和密码就不会存储在连接器计算机上。

使用的证书特定于应用程序代理服务。 这些证书是在初始注册期间创建的，每隔几个月由连接器自动续订。 

如果连接器有几个月未连接到服务，其证书可能会过时。 在这种情况下，需要进行注册，为此必须卸载然后重新安装连接器来触发注册。 可运行以下 PowerShell 命令：

```
* Import-module AppProxyPSModule
* Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>性能和可伸缩性

尽管联机服务的规模是透明的，但对于连接器而言，规模是一种可变因素。 需要提供足够的连接器才能处理高峰流量。 由于连接器是无状态的，它们不依赖于用户或会话数目， 而是依赖于请求的数目及其有效负载大小。 对于标准 Web 流量，我们发现，一台普通的计算机每秒就能处理几千个请求。 具体能够处理多少，取决于确切的计算机特征。

连接器性能受 CPU 和网络的约束。 SSL 加密和解密依赖于 CPU 性能，而网络性能对于快速连接到应用程序和 Azure 中的联机服务非常重要。 相比之下，内存对于连接器来说不是一个很大的问题。

对于连接器服务，我们会尽最大努力来减轻连接器的负载。 联机服务会处理大部分处理负载，以及所有未经身份验证的流量。 可在云中完成的所有任务将在云中完成。

影响性能的另一个因素是连接器之间的网络质量，包括：

* _联机服务：_如果连接速度缓慢或者延迟较高， 则会给服务级别造成波动。 组织最好是通过 Express Route 连接到 Azure。 否则，网络团队应确保以有效的方式来处理 Azure 的连接。

* _后端应用程序：_在某些情况下，连接器与后端应用程序之间存在其他代理。 在连接器计算机中打开浏览器并访问这些应用程序，就能轻松排查相关问题。 如果连接器在 Azure 中运行，而这些应用程序位于本地，则用户的体验可能与期望不符。
* _域控制器：_如果连接器使用 Kerberos 约束委派 (KCD) 执行 SSO，在向后端发送请求之前，它们会联系域控制器。 连接器提供 Kerberos 票证缓存，但在繁忙的环境中，域控制器的响应能力下降可能会影响体验。 如果连接器在 Azure 中运行，而域控制器位于本地，则更容易出现这种情况。

##<a name="automatic-updates-to-the-connector"></a>连接器的自动更新

我们通过连接器更新程序服务使连接器自动保持最新状态。 这样，你便可以持续获得所有新增功能，以及安全与性能增强功能，保持自己的优势。

Azure AD 支持自动更新部署的所有连接器。 只要应用程序代理连接器更新程序服务保持运行，连接器就会自动更新，不会出现停机，也不需要执行手动步骤。 如果在服务器上未看到连接器更新程序服务，需要重新安装连接器才能获得所有更新。 如需了解有关安装连接器的详细信息，请参阅[安装程序文档](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-enable.md)。

### <a name="updater-impact"></a>更新程序的影响

_包含一个连接器的租户：_如果只有一个连接器，该连接器将连同最新的组一起更新。 由于没有其他连接器可用来重新路由流量，因此在更新期间，服务将不可用。 为了避免这种停机并更轻松地确保高可用性，我们建议安装另一个连接器并创建连接器组。 有关具体操作的详细信息，请参阅[有关连接器组的文档](https://azure.microsoft.com/en-us/documentation/articlesactive-directory-application-proxy-connectors.md)。

_其他租户：_在更新连接器期间，流量将重新路由到其他连接器，以尽量减少中断。 但是，在开始更新时正在进行的所有事务可能被丢弃。 浏览器应自动重试操作，使你能够意识到这种潜在的丢弃行为。 否则，可能需要刷新页面才能解决此问题。

我们知道，哪怕是一分钟的停机也让人感到非常不便，但是，这些更新可以通过大量的改进来优化连接器，因此，我们认为这是值得的。

如需有关最新连接器更新所发生的更改的详细信息，请参阅[最新更新](https://azure.microsoft.com/en-us/updates/app-proxy-connector-12sept2016)。 每次有更新发布时，我们都会修改该页面。

## <a name="under-the-hood"></a>揭秘

我们在 Azure 中提供了众多有用的工具。 尤其是连接器，它们提供大量有用的功能。 连接器基于 Windows Server Web 应用程序代理，因此包含大多数相同的管理工具，包括丰富的 Windows 事件日志和 Windows 性能计数器集，如下面的“事件查看器”中所示：

 ![AzureAD 事件查看器](./media/application-proxy-understand-connectors/event-view-window.png)

性能监视器：

 ![AzureAD 性能监视器](./media/application-proxy-understand-connectors/performance-monitor.png)

连接器提供管理日志和会话日志。 管理日志包括关键事件及其错误。 会话日志包括所有事务及其处理详细信息。 

若要查看这些日志，必须在事件查看器的“视图”菜单中启用“显示分析和调试日志”。 然后，必须启用这些日志才能开始收集事件。 这些日志不会显示在 Windows Server 2012 R2 上的 Web 应用程序代理中，因为连接器基于更新的版本。

 ![AzureAD 事件查看器会话](./media/application-proxy-understand-connectors/event-view-window-session.png)

可在“服务”窗口中检查服务的状态。 连接器由两个 Windows 服务组成：一个是实际的连接器，另一个负责处理更新。 这两个服务需要一直运行。

 ![AzureAD 本地服务](./media/application-proxy-understand-connectors/aad-connector-services.png)

有关解决应用程序代理连接器错误的信息，请参阅[应用程序代理故障排除](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot)。

##<a name="next-steps"></a>后续步骤
[使用现有的本地代理服务器](application-proxy-working-with-proxy-servers.md)<br>
[如何以无提示方式安装 Azure AD 应用程序代理连接器](active-directory-application-proxy-silent-installation.md)




<!--HONumber=Feb17_HO1-->


