---
title: "使用 Azure AD 应用程序代理时的安全注意事项 | Microsoft Docs"
description: "介绍使用 Azure AD 应用程序代理时的安全注意事项"
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5a2ab5c68345deee3a34173d38d529a911c7ff3e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理远程访问应用时的安全注意事项

本文介绍了在使用 Azure Active Directory 应用程序代理时，能够保证用户和应用程序安全的组件。

下图显示了 Azure AD 如何实现对本地应用程序的安全远程访问。

 ![通过 Azure AD 应用程序代理进行安全远程访问的示意图](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="security-benefits"></a>安全优势

Azure AD 应用程序代理提供以下安全优势：

### <a name="authenticated-access"></a>经过身份验证的访问 

如果选择使用 Azure Active Directory 预身份验证，则只有经过身份验证的连接才能访问网络。

Azure AD 应用程序代理依赖于使用 Azure AD 安全令牌服务 (STS) 执行所有身份验证。  预身份验证的独特性质会阻止大量的匿名攻击，因为只有经过身份验证的标识才能访问后端应用程序。

如果选择“直通”作为预身份验证方法，则无法获得此优势。 

### <a name="conditional-access"></a>条件性访问

在与网络建立连接之前应用更丰富的策略控制。

使用[条件性访问](active-directory-conditional-access-azure-portal-get-started.md)可以针对允许哪些流量访问后端应用程序来定义限制。 可以基于位置、身份验证强度和用户风险配置文件，创建限制登录的策略。

还可以使用条件性访问配置多重身份验证策略，为用户身份验证再添一层安全保障。 

### <a name="traffic-termination"></a>流量终止

所有流量在云中终止。

Azure AD 应用程序代理是一个反向代理，因此，发往后端应用程序的所有流量会在服务上终止。 只能与后端服务器重建会话，这意味着，不会公开后端服务器来定向 HTTP 流量。 这种配置意味着可以更好地抵御有针对性的攻击。

### <a name="all-access-is-outbound"></a>所有访问都是出站的 

无需向企业网络打开入站连接。

应用程序代理连接器仅与 Azure AD 应用程序代理服务建立出站连接，这意味着无需为传入连接打开防火墙端口。 传统代理要求部署外围网络（也称为 *DMZ*、*外围安全区域*或*屏蔽子网*），并在网络边缘允许未经身份验证的连接进行访问。 此方案需要投资开发 Web 应用程序防火墙产品，用于分析流量并保护环境。 使用应用程序代理，不需要部署外围网络，因为所有连接都是出站的，并且是通过安全通道建立的。

有关连接器的信息，请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)。

### <a name="cloud-scale-analytics-and-machine-learning"></a>云级分析和机器学习 

获得一流的安全保护。

由于属于 Azure Active Directory，因此应用程序代理可以利用 [Azure AD Identity Protection](active-directory-identityprotection.md)（数据由 Microsoft 安全响应中心和反数字犯罪部门提供）。 我们会共同主动发现遭到入侵的帐户，并提供保护，以免出现高风险登录威胁。我们会考虑许多因素，以确定哪些登录尝试有高风险。 这些因素包括标记为受感染设备、对网络进行匿名化处理，以及非典型或不太可能的位置。

其中的许多报告与事件已通过某个 API 提供，便于与安全信息与事件管理 (SIEM) 系统集成。

### <a name="remote-access-as-a-service"></a>以服务的形式进行远程访问

无需担心要维护和修补本地服务器。

未修补的软件仍会遭受大量攻击。 Azure AD 应用程序代理是 Microsoft 拥有的一项 Internet 级服务，因此，用户始终可以获得最新的安全修补程序和升级。

为了提高 Azure AD 应用程序代理发布的应用程序的安全性，我们会阻止 Web 爬网程序机器人存档应用程序或编制其索引。 每当 Web 爬网程序机器人尝试检索已发布的应用的机器人设置时，应用程序代理都会回复一个 robots.txt 文件，其中包含 `User-agent: * Disallow: /`。

### <a name="ddos-prevention"></a>DDOS 预防

通过应用程序代理发布的应用程序受到保护，以免遭分布式拒绝服务 (DDOS) 攻击。

应用程序代理服务监视试图访问应用程序和网络的流量。 如果请求远程访问应用程序的设备数量激增，Microsoft 就会限制对网络的访问。 

Microsoft 统一监视各个应用程序和订阅的流量模式。 如果某应用程序收到的请求数高于正常水平，那么请求访问应用程序将会在短时间内遭到拒绝。 如果跨整个订阅收到的请求数高于正常水平，那么请求访问任何应用都会遭到拒绝。 这一预防措施可以防止应用程序服务器因远程访问请求而重载，以便本地用户能够一直访问应用程序。 

## <a name="under-the-hood"></a>揭秘

Azure AD 应用程序代理由两个部分组成：

* 基于云的服务：此服务在 Azure 中运行，是建立外部客户端/用户连接的位置。
* [本地连接器](application-proxy-understand-connectors.md)：该连接器是一个本地组件，侦听来自 Azure AD 应用程序代理服务的请求并处理与内部应用程序的连接。 

发生以下情况时，会在连接器与应用程序代理服务之间建立流量：

* 首次设置连接器时。
* 连接器从应用程序代理服务中拉取配置信息。
* 用户访问发布的应用程序时。

>[!NOTE]
>所有通信通过 SSL 发生，始终从连接器发起，目标为应用程序代理服务。 该服务只会建立出站连接。

连接器在执行几乎所有的调用时，都会使用客户端证书向应用程序代理服务进行身份验证。 只有在执行初始设置步骤时，此过程才有所不同，因为客户端证书是在此步骤中建立的。

### <a name="installing-the-connector"></a>安装连接器

首次设置连接器时会发生以下流量事件：

1. 在安装连接器的过程中，将连接器注册到服务。 系统会提示用户输入其 Azure AD 管理员凭据。 从此身份验证获取令牌，并将其提供给 Azure AD 应用程序代理服务。
2. 应用程序代理服务评估该令牌。 它检查用户是否为租户中的公司管理员。 如果用户不是管理员，则终止此过程。
3. 连接器生成客户端证书请求，并将此请求连同令牌一起传递给应用程序代理服务。 该服务转而验证令牌并为客户端证书请求签名。
4. 以后，连接器将使用此客户端证书来与应用程序代理服务通信。
5. 连接器使用其客户端证书从服务执行初始的系统配置数据提取，并准备好接收请求。

### <a name="updating-the-configuration-settings"></a>更新配置设置

每当应用程序代理服务更新配置设置时，都会发生以下流量事件：

1. 连接器使用其客户端证书连接到应用程序代理服务中的配置终结点。
2. 验证客户端证书后，应用程序代理服务向连接器返回配置数据（例如，连接器应属于的连接器组）。
3. 如果当前证书超过 180 天，连接器会生成新的证书请求，即，每隔 180 天有效地更新客户端证书。

### <a name="accessing-published-applications"></a>访问发布的应用程序

当用户访问发布的应用程序时，应用程序代理服务与应用程序代理连接器之间发生以下事件：

1. [服务对应用用户进行身份验证](#the-service-checks-the-configuration-settings-for-the-app)
2. [服务在连接器队列中放入请求](#The-service-places-a-request-in-the-connector-queue)
3. [连接器处理来自队列的请求](#the-connector-receives-the-request-from-the-queue)
4. [连接器等待响应](#the-connector-waits-for-a-response)
5. [服务将数据流式传输给用户](#the-service-streams-data-to-the-user)

若要深入了解其中每个步骤的具体内容，请继续阅读下文。


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1.服务对应用用户进行身份验证

如果已将应用配置为使用“直通”作为其预身份验证方法，则跳过此部分中的步骤。

如果已将应用配置为使用 Azure AD 进行预身份验证，则会将用户重定向到 Azure AD STS 进行身份验证，并且发生以下步骤：

1. 应用程序代理会检查特定应用程序的任何条件访问策略要求。 此步骤可确保将用户分配到该应用程序。 如果需要双重验证，身份验证序列会提示用户执行第二个身份验证方法。

2. 所有检查都通过后，Azure AD STS 将为应用程序颁发已签名的令牌，并将用户重定向回应用程序代理服务。

3. 应用程序代理会验证是否已颁发用于更正应用程序的令牌。 应用程序代理还会执行其他检查，例如，确保令牌已由 Azure AD 签名并且仍在有效期内。

4. 应用程序代理设置加密的身份验证 Cookie，以指示已向应用程序进行身份验证。 该 Cookie 包含基于 Azure AD 颁发的令牌和其他数据（例如，用于身份验证的用户名）的过期时间戳。 该 Cookie 已使用只有应用程序代理服务知道的私钥加密。

5. 应用程序代理将用户重定向回到最初请求的 URL。

如果预身份验证步骤的任何一个环节失败，用户的请求会被拒绝，并向用户显示一条消息指出问题的起源。


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2.服务在连接器队列中放入请求

连接器向应用程序代理服务保持打开出站连接。 传入某个请求时，服务会在一个打开的连接上将该请求排入队列，等待连接器提取。

该请求包含应用程序中的项，例如，已加密 Cookie 中的请求标头和数据、发出请求的用户，以及请求 ID。 虽然已加密 Cookie 中的数据会随请求一起发送，但不会发送身份验证 Cookie 本身。

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3.连接器处理来自队列的请求。 

应用程序代理基于请求执行以下操作之一：

* 如果请求是一个简单的操作（例如，像 RESTful *GET* 请求一样在正文中不包含任何数据），则连接器将与目标内部资源建立连接并等待响应。

* 如果请求的正文中包含关联的数据（例如 RESTful *POST* 操作），连接器将使用客户端证书来与应用程序代理实例建立出站连接。 建立此连接是为了请求数据，并打开与内部资源的连接。 收到来自连接器的请求后，应用程序代理服务开始接受用户的内容并将数据转发到连接器。 连接器随之将数据转发到内部资源。

#### <a name="4-the-connector-waits-for-a-response"></a>4.连接器等待响应。

完成向后端发送请求并传输所有内容后，连接器将等待响应。

收到响应后，连接器将与应用程序代理服务建立出站连接，以返回标头详细信息并开始流式传输返回的数据。

#### <a name="5-the-service-streams-data-to-the-user"></a>5.服务将数据流式传输给用户。 

此时可能会发生某些应用程序处理活动。 如果已将应用程序代理配置为转换应用程序中的标头或 URL，则会在此步骤中根据需要进行该项处理。


## <a name="next-steps"></a>后续步骤

[使用 Azure AD 应用程序代理时的网络拓扑注意事项](application-proxy-network-topology-considerations.md)

[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)
