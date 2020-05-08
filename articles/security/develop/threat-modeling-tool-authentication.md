---
title: 身份验证 - Microsoft 威胁建模工具 - Azure | Microsoft 文档
description: 针对威胁建模工具中暴露的威胁采取的缓解措施
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref
ms.openlocfilehash: 569e8d769d56acbb4c7fb4258952ec19e44b58e4
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607820"
---
# <a name="security-frame-authentication--mitigations"></a>安全框架：身份验证 | 缓解措施

| 产品/服务 | 项目 |
| --------------- | ------- |
| **Web 应用程序**    | <ul><li>[考虑使用标准身份验证机制向 Web 应用程序进行身份验证](#standard-authn-web-app)</li><li>[应用程序必须安全处理失败的身份验证方案](#handle-failed-authn)</li><li>[启用单步执行或自适应身份验证](#step-up-adaptive-authn)</li><li>[确保适当锁定管理界面](#admin-interface-lockdown)</li><li>[安全地实施忘记密码功能](#forgot-pword-fxn)</li><li>[确保已实施密码和帐户策略](#pword-account-policy)</li><li>[实现控制来防止用户名枚举](#controls-username-enum)</li></ul> |
| **Database** | <ul><li>[如果可能，请使用 Windows 身份验证连接到 SQL Server](#win-authn-sql)</li><li>[如果可能，请使用 Azure Active Directory 身份验证连接到 SQL 数据库](#aad-authn-sql)</li><li>[使用 SQL 身份验证模式时，确保对 SQL Server 实施帐户和密码策略](#authn-account-pword)</li><li>[不要在包含的数据库中使用 SQL 身份验证](#autn-contained-db)</li></ul> |
| **Azure 事件中心** | <ul><li>[结合 SaS 令牌使用每个设备的身份验证凭据](#authn-sas-tokens)</li></ul> |
| **Azure 信任边界** | <ul><li>[启用面向 Azure 管理员的多重身份验证](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric 信任边界** | <ul><li>[限制对 Service Fabric 群集的匿名访问](#anon-access-cluster)</li><li>[确保 Service Fabric 的客户端到节点证书不同于节点到节点证书](#fabric-cn-nn)</li><li>[使用 AAD 对 service fabric 群集的客户端进行身份验证](#aad-client-fabric)</li><li>[确保从批准的证书颁发机构 (CA) 获取 Service Fabric 证书](#fabric-cert-ca)</li></ul> |
| **标识服务器** | <ul><li>[使用标识服务器支持的标准身份验证方案](#standard-authn-id)</li><li>[使用可缩放的替代方法覆盖默认的标识服务器令牌缓存](#override-token)</li></ul> |
| **计算机信任边界** | <ul><li>[确保部署的应用程序的二进制文件经过数字签名](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[在 WCF 中连接到 MSMQ 队列时启用身份验证](#msmq-queues)</li><li>[WCF - 不要将消息 clientCredentialType 设置为 none](#message-none)</li><li>[WCF-不要将传输 clientCredentialType 设置为 none](#transport-none)</li></ul> |
| **Web API** | <ul><li>[确保使用标准身份验证技术保护 Web API](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[使用 Azure Active Directory 支持的标准身份验证方案](#authn-aad)</li><li>[使用可缩放的替代项覆盖默认的 ADAL 令牌缓存](#adal-scalable)</li><li>[确保使用 TokenReplayCache 防止 ADAL 身份验证令牌重放](#tokenreplaycache-adal)</li><li>[使用 ADAL 库来管理从 OAuth2 客户端到 AAD （或本地 AD）的令牌请求](#adal-oauth2)</li></ul> |
| **IoT 现场网关** | <ul><li>[对连接到现场网关的设备进行身份验证](#authn-devices-field)</li></ul> |
| **IoT 云网关** | <ul><li>[确保对连接到云网关的设备进行身份验证](#authn-devices-cloud)</li><li>[使用每设备身份验证凭据](#authn-cred)</li></ul> |
| **Azure 存储** | <ul><li>[确保只对所需的容器和 Blob 授予匿名读取访问权限](#req-containers-anon)</li><li>[使用 SAS 或 SAP 对 Azure 存储中的对象授予受限的访问权限](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>考虑使用标准身份验证机制向 Web 应用程序进行身份验证

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Web 应用程序 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| 详细信息 | <p>身份验证是某个实体证明其身份的过程，这通常是通过用户名和密码等凭据完成的。 可以考虑使用多种身份验证协议。 下面列出了其中一些协议：</p><ul><li>客户端证书</li><li>基于 Windows</li><li>基于窗体</li><li>联合身份验证 - ADFS</li><li>联合身份验证 - Azure AD</li><li>联合身份验证 - 标识服务器</li></ul><p>考虑使用标准身份验证机制来识别源进程</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>应用程序必须安全处理失败的身份验证方案

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Web 应用程序 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| 详细信息 | <p>显式执行用户身份验证的应用程序必须安全处理失败的身份验证方案。身份验证机制必须：</p><ul><li>在身份验证失败时拒绝访问特权资源</li><li>身份验证失败并且发生访问被拒绝后，显示常规错误消息</li></ul><p>测试：</p><ul><li>登录失败后保护特权资源</li><li>身份验证失败并且发生访问被拒绝事件后，显示常规错误消息</li><li>尝试失败的次数过多后禁用帐户</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>启用升级或自适应身份验证

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Web 应用程序 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| 详细信息 | <p>验证应用程序是否有附加的授权（例如，通过多重身份验证（在短信中发送 OTP，等等）执行升级或自适应的身份验证，或者提示重新身份验证），以便在向用户授予对敏感信息的访问权限之前向其提出质询。 对帐户或操作进行重大更改时，也可以应用此规则</p><p>这也意味着，必须以适当的方式实施身份验证的调适，以便应用程序能够正确实施区分上下文的授权，阻止通过参数篡改等方式执行未经授权的操作</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>确保适当锁定管理界面

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Web 应用程序 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| 详细信息 | 第一种解决方案是仅授予从某个源 IP 范围到管理界面的访问权限。 如果该解决方案不可行，我们始终建议针对管理界面的登录实施升级或自适应的身份验证 |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>安全实施忘记密码功能

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Web 应用程序 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| 详细信息 | <p>首先，验证“忘记密码”和其他恢复路径是否发送包含限时激活令牌而不是密码本身的链接。 在发送链接之前，还可能需要实施基于软令牌（例如 SMS 令牌、本机移动应用程序等）的其他身份验证。 第二，在获取新密码的过程正在进行时，不应锁定用户帐户。</p><p>否则，每当攻击者决定使用自动攻击来有意锁定用户时，可能会导致拒绝服务攻击。 第三，在新密码请求正在设置时，显示的消息应该普通化，防止用户名枚举。 第四，始终禁止使用旧密码并实施强密码策略。</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>确保实施密码和帐户策略

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Web 应用程序 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| 详细信息 | <p>应该实施与组织策略和最佳做法相符的密码与帐户策略。</p><p>为了防范暴力破解和基于字典的猜测：必须实现强密码策略，确保用户创建复杂密码（例如，最小长度为 12 个字符，必须包含字母数字和特殊字符）。</p><p>可按以下方式实施帐户锁定策略：</p><ul><li>**软锁定：** 这可能是防止用户遭受暴力破解攻击的不错选项。 例如，当用户输入错误密码三次时，应用程序可能会将该帐户锁定一分钟，以便减缓强行破解密码的过程，从而使攻击者无法继续进行盈利。 如果要在此示例中实现硬锁定对策，可以通过永久锁定帐户来实现 "DoS"。 或者，应用程序可以生成 OTP（一次性密码），并将其以带外方式（通过电子邮件、短信等）发送给用户。 另一种做法是在达到失败尝试次数的阈值后实施 CAPTCHA。</li><li>**硬锁定：** 每当检测到你的应用程序的攻击者，并通过永久锁定其帐户，直到响应团队有时间完成其辩论之前，就应应用这种类型的锁定。 完成此过程后，你可以决定向用户返回其帐户或对其采取进一步的法律操作。 这种方式可以防止攻击者进一步侵入应用程序和基础结构。</li></ul><p>为了防范针对默认与可预测帐户的攻击，请验证所有密钥和密码是否可替换，并且是否是在安装后生成或替换的。</p><p>如果应用程序必须自动生成密码，请确保生成的密码是随机的并具有高熵。</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>实施控制来防止用户名枚举

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Web 应用程序 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| **步骤** | 所有错误消息应该普通化，以防止用户名枚举。 此外，在注册页等功能中，有时无法避免信息泄露。 在这种情况下，需要使用 CAPTCHA 等频率限制方法来防止攻击者的自动攻击。 |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>尽可能使用 Windows 身份验证连接到 SQL Server

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | 数据库 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | OnPrem |
| **特性**              | SQL 版本 - 所有 |
| **参考**              | [SQL Server - 选择身份验证模式](https://msdn.microsoft.com/library/ms144284.aspx) |
| **步骤** | Windows 身份验证使用 Kerberos 安全协议，提供有关强密码复杂性验证的密码策略强制，还提供帐户锁定支持，并且支持密码过期。|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>尽可能使用 Azure Active Directory 身份验证连接到 SQL 数据库

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | 数据库 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | SQL Azure |
| **特性**              | SQL 版本 - V12 |
| **参考**              | [使用 Azure Active Directory 身份验证连接到 SQL 数据库](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **步骤** | **最低版本：** 需要安装 Azure SQL 数据库 V12 才能允许 Azure SQL 数据库针对 Microsoft 目录使用 AAD 身份验证 |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>使用 SQL 身份验证模式时，确保对 SQL Server 实施帐户和密码策略

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | 数据库 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [SQL Server 密码策略](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **步骤** | 使用 SQL Server 身份验证时，登录名是在不基于 Windows 用户帐户的 SQL Server 中创建的。 用户名和密码是使用 SQL Server 创建的，存储在 SQL Server 中。 SQL Server 可以使用 Windows 密码策略机制。 对于在 SQL Server 中使用的密码，它可以应用 Windows 中所用的相同复杂性与过期策略。 |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>不要在包含的数据库中使用 SQL 身份验证

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | 数据库 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | OnPrem、SQL Azure |
| **特性**              | SQL 版本 - MSSQL2012，SQL 版本 - V12 |
| **参考**              | [针对包含数据库的安全性最佳方法](https://msdn.microsoft.com/library/ff929055.aspx) |
| **步骤** | 缺少强制密码策略可能会增大在包含的数据库中建立弱凭据的可能性。 利用 Windows 身份验证 |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>结合 SaS 令牌使用每个设备的身份验证凭据

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Azure 事件中心 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [事件中心身份验证和安全模型概述](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **步骤** | <p>事件中心安全模型基于共享访问签名 (SAS) 令牌与事件发布者的组合。 发布者名称表示接收令牌的 DeviceID。 它可以帮助将生成的令牌与相应的设备相关联。</p><p>所有消息在服务端标记为发起方，用于检测有效负载中原点欺骗的企图。 对设备进行身份验证时，将生成一个对应于唯一发布者的基于设备的 SaS 令牌。</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>启用面向 Azure 管理员的多重身份验证

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Azure 信任边界 |
| **SDL 阶段**               | 部署 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [什么是 Azure 多重身份验证？](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **步骤** | <p>多重身份验证 (MFA) 是要求使用多种验证方法的身份验证方法，为用户登录和事务额外提供一层重要的安全保障。 它需要以下验证方法中的两种或更多种来进行工作：</p><ul><li>您知道的信息（通常为密码）</li><li>您拥有的东西（不易被复制的受信任设备，如电话）</li><li>自身的特征（生物辨识系统）</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>限制对 Service Fabric 群集的匿名访问

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Service Fabric 信任边界 |
| **SDL 阶段**               | 部署 |
| **适用的技术** | 泛型 |
| **特性**              | 环境 - Azure  |
| **参考**              | [Service Fabric 群集安全方案](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **步骤** | <p>始终都应该保护群集，防止未经授权的用户连接到群集，特别是群集上正在运行生产工作负荷时。</p><p>创建 Service Fabric 群集时，请确保安全模式设置为“安全”，并配置所需的 X.509 服务器证书。 如果创建“不安全”的群集，当这种群集在公共 Internet 上公开管理终结点时，任何匿名用户都可与它建立连接。</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>确保 Service Fabric 的客户端到节点证书不同于节点到节点证书

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Service Fabric 信任边界 |
| **SDL 阶段**               | 部署 |
| **适用的技术** | 泛型 |
| **特性**              | 环境 - Azure，环境 - 独立 |
| **参考**              | [Service Fabric 客户端到节点的证书安全性](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security)、[使用客户端证书连接到安全群集](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **步骤** | <p>客户端到节点证书安全性是在使用 Azure 门户、Resource Manager 模板或独立的 JSON 模板创建群集时，通过指定管理员客户端证书和/或用户客户端证书来配置的。</p><p>指定的管理员客户端证书和用户客户端证书应该不同于为节点到节点安全性指定的主证书和辅助证书。</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>使用 AAD 向 Service Fabric 群集进行客户端身份验证

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Service Fabric 信任边界 |
| **SDL 阶段**               | 部署 |
| **适用的技术** | 泛型 |
| **特性**              | 环境 - Azure |
| **参考**              | [群集安全方案 - 安全建议](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **步骤** | 在 Azure 上运行的群集除了使用客户端证书以外，还可使用 Azure Active Directory (AAD) 来保护对管理终结点的访问。 对于 Azure 群集，建议针对节点到节点安全性使用 AAD 安全性来验证客户端和证书。|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>确保从批准的证书颁发机构 (CA) 获取 Service Fabric 证书

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Service Fabric 信任边界 |
| **SDL 阶段**               | 部署 |
| **适用的技术** | 泛型 |
| **特性**              | 环境 - Azure |
| **参考**              | [X.509 证书和 Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **步骤** | <p>Service Fabric 使用 X.509 服务器证书对节点和客户端进行身份验证。</p><p>在 Service Fabric 中使用证书时，需考虑一些重要事项：</p><ul><li>运行生产工作负荷的群集中使用的证书应使用正确配置的 Windows Server 证书服务进行创建，或者从已批准的证书颁发机构 (CA) 获取。 CA 可以是批准的外部 CA，也可以是适当管理的内部公钥基础结构 (PKI)</li><li>切勿在生产环境中使用通过 MakeCert.exe 等工具创建的临时或测试证书</li><li>可以使用自签名证书，但只应使用它来测试群集，而不应在生产环境中使用</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>使用标识服务器支持的标准身份验证方案

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | 标识服务器 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [IdentityServer3 - 大图](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **步骤** | <p>下面是标识服务器支持的典型交互：</p><ul><li>浏览器与 Web 应用程序通信</li><li>Web 应用程序与 Web API 通信（有时是代表自身，有时代表用户）</li><li>基于浏览器的应用程序与 Web API 通信</li><li>本机应用程序与 Web API 通信</li><li>基于服务器的应用程序与 Web API 通信</li><li>Web API 与 Web API 通信（有时是代表自身，有时代表用户）</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>使用可缩放的替代方案覆盖默认的标识服务器令牌缓存

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | 标识服务器 |
| **SDL 阶段**               | 部署 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [标识服务器部署 - 缓存](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **步骤** | <p>IdentityServer 具有简单的内置内存中缓存。 尽管对于小规模本机应用而言这很合适，但是，出于以下原因，它无法根据中间层和后端应用程序缩放：</p><ul><li>这些应用程序同时由许多用户访问。 在同一个存储中保存所有访问令牌会产生隔离问题，并且在大规模运行时会带来难题：如果用户数目众多，并且每个用户的令牌数与应用代表他们访问的资源数相当，则可能意味着需要执行极大量的开销极高的查找操作</li><li>这些应用程序通常部署在分布式拓扑中，其中的多个节点必须能够访问同一个缓存</li><li>在进程回收和停用后，缓存的令牌必须能够幸存</li><li>出于上述所有原因，在实施 Web 应用程序时，建议使用 Azure Redis 缓存等可缩放的替代方案来覆盖标识服务器的默认令牌缓存</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>确保部署的应用程序的二进制文件经过数字签名

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | 计算机信任边界 |
| **SDL 阶段**               | 部署 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| **步骤** | 确保部署的应用程序的二进制文件经过数字签名，以便能够验证二进制文件的完整性|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>连接到 WCF 中的 MSMQ 队列时启用身份验证

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | WCF |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型、NET Framework 3 |
| **特性**              | 不可用 |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **步骤** | 如果程序在连接到 MSMQ 队列时无法启用身份验证，攻击者可能会以匿名方式将消息提交到队列进行处理。 如果不使用身份验证连接到用于向另一程序传送消息的 MSMQ 队列，攻击者可能会提交恶意的匿名消息。|

### <a name="example"></a>示例
以下 WCF 配置文件中的 `<netMsmqBinding/>` 元素指示 WCF 在连接到用于消息传送的 MSMQ 队列时禁用身份验证。
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
请将 MSMQ 配置为要求随时针对任何传入或传出的消息执行 Windows 域或证书身份验证。

### <a name="example"></a>示例
以下 WCF 配置文件中的 `<netMsmqBinding/>` 元素指示 WCF 在连接到 MSMQ 队列时启用证书身份验证。 客户端使用 X.509 证书进行身份验证。 客户端证书必须在服务器的证书存储中存在。
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF - 不要将消息 clientCredentialType 设置为 none

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | WCF |
| **SDL 阶段**               | 构建 |
| **适用的技术** | .NET Framework 3 |
| **特性**              | 客户端凭据类型 - None |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **步骤** | 缺少身份验证意味着每个人都能访问此服务。 不对其客户端进行身份验证的服务允许所有用户访问。 将应用程序配置为对客户端凭据进行身份验证。 为此，可将消息 clientCredentialType 设置为 Windows 或 Certificate。 |

### <a name="example"></a>示例
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF - 不要将传输 clientCredentialType 设置为 none

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | WCF |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型、.NET Framework 3 |
| **特性**              | 客户端凭据类型 - None |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **步骤** | 缺少身份验证意味着每个人都能访问此服务。 不对其客户端进行身份验证的服务允许所有用户访问其功能。 将应用程序配置为对客户端凭据进行身份验证。 为此，可将传输 clientCredentialType 设置为 Windows 或 Certificate。 |

### <a name="example"></a>示例
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>确保使用标准身份验证技术保护 Web API

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Web API |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [ASP.NET Web API 中的身份验证和授权](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api)、[使用 ASP.NET Web API 的外部身份验证服务 (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **步骤** | <p>身份验证是某个实体证明其身份的过程，这通常是通过用户名和密码等凭据完成的。 可以考虑使用多种身份验证协议。 下面列出了其中一些协议：</p><ul><li>客户端证书</li><li>基于 Windows</li><li>基于窗体</li><li>联合身份验证 - ADFS</li><li>联合身份验证 - Azure AD</li><li>联合身份验证 - 标识服务器</li></ul><p>参考部分中的链接提供了有关如何实施每种身份验证方案来保护 Web API 的低级别详细信息。</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>使用 Azure Active Directory 支持的标准身份验证方案

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Azure AD |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [Azure AD 的身份验证方案](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/)、[Azure Active Directory 代码示例](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/)、[Azure Active Directory 开发人员指南](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **步骤** | <p>Azure Active Directory (Azure AD) 通过提供标识即服务并支持 OAuth 2.0 和 OpenID Connect 等行业标准协议，简化了对开发人员的身份验证。 下面是 Azure AD 支持的五种主要应用程序方案：</p><ul><li>Web 浏览器到 Web 应用程序：用户需要登录到由 Azure AD 保护的 Web 应用程序</li><li>单页面应用程序 (SPA)：用户需要登录到由 Azure AD 保护的单页面应用程序</li><li>本机应用程序到 Web API：在手机、平板电脑或电脑上运行的本机应用程序需要对用户进行身份验证以从 Azure AD 所保护的 Web API 获取资源</li><li>Web 应用程序到 Web API：Web 应用程序需要从 Azure AD 所保护的 Web API 获取资源。</li><li>后台或服务器应用程序到 Web API：没有 Web 用户界面的后台应用程序或服务器应用程序需要从 Azure AD 所保护的 Web API 获取资源</li></ul><p>请参阅参考部分中的链接，了解低级别实施详细信息</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>使用可缩放的替代方案覆盖默认的 ADAL 令牌缓存

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Azure AD |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [使用 Azure Active Directory 对 Web 应用程序执行新式身份验证](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/)、[使用 Redis 作为 ADAL 令牌缓存](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **步骤** | <p>ADAL（Active Directory 身份验证库）使用的默认缓存是依赖于静态存储、可在进程范围内使用的内存中缓存。 尽管这很适合用于本机应用程序，但是，出于以下原因，它无法根据中间层和后端应用程序缩放：</p><ul><li>这些应用程序同时由许多用户访问。 在同一个存储中保存所有访问令牌会产生隔离问题，并且在大规模运行时会带来难题：如果用户数目众多，并且每个用户的令牌数与应用代表他们访问的资源数相当，则可能意味着需要执行极大量的开销极高的查找操作</li><li>这些应用程序通常部署在分布式拓扑中，其中的多个节点必须能够访问同一个缓存</li><li>在进程回收和停用后，缓存的令牌必须能够幸存</li></ul><p>出于上述所有原因，在实施 Web 应用程序时，建议使用 Azure Redis 缓存等可缩放的替代方案来覆盖默认的 ADAL 令牌缓存。</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>确保使用 TokenReplayCache 防止 ADAL 身份验证令牌重放

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Azure AD |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [使用 Azure Active Directory 对 Web 应用程序执行新式身份验证](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **步骤** | <p>开发人员可以使用 TokenReplayCache 属性来定义令牌重放缓存，这是一个可以用来保存令牌的存储，用途是验证没有多次使用某个令牌。</p><p>这是针对一种所谓的令牌重放攻击的常见攻击采取的措施：截获登录时发送的令牌的攻击者可能会尝试再次将该令牌发送到应用（“重放”它），以求建立新的会话。 例如，在 OIDC 代码授予流中，用户身份验证成功后，向信赖方的“/signin-oidc”终结点发出的请求包含“id_token”、“code”和“state”参数。</p><p>信赖方会验证此请求并建立新的会话。 如果攻击者捕获到此请求并重放它，就可以建立成功的会话并欺骗用户。 在 OpenID Connect 中使用 nonce 可以限制但不能完全消除攻击成功得手的结果。 为了保护应用程序，开发人员可以提供 ITokenReplayCache 的实现，并向 TokenReplayCache 分配一个实例。</p>|

### <a name="example"></a>示例
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>示例
下面是 ITokenReplayCache 接口的示例实现。 （请自定义并实现自己的特定于项目的缓存框架）
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
必须通过“TokenValidationParameters”属性在 OIDC 选项中引用实现的缓存，如下所示。
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

请注意，若要测试此配置的有效性，请登录到受 OIDC 保护的本地应用程序，并在 Fiddler 中捕获向 `"/signin-oidc"` 终结点发出的请求。 如果未实施保护，在 Fiddler 中重放此请求会设置一个新的会话 Cookie。 添加 TokenReplayCache 保护后重放请求时，应用程序将引发异常，如下所示：`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>使用 ADAL 库来管理从 OAuth2 客户端发往 AAD（或本地 AD）的令牌请求

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Azure AD |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **步骤** | <p>通过 Azure AD 身份验证库 (ADAL)，客户端应用程序开发人员能够轻松利用云或本地 Active Directory (AD) 对用户进行身份验证，并获取访问令牌，以进行安全的 API 调用。</p><p>ADAL 提供许多可以方便开发人员进行身份验证的功能，例如，异步支持、用于存储访问令牌和刷新令牌的可配置令牌缓存、访问令牌过期时和提供刷新令牌时自动刷新令牌，等等。</p><p>ADAL 可以应对大部分复杂情况，因而可以帮助开发人员集中处理其应用程序中的业务逻辑，并可轻松保护资源而不必成为安全方面的专家。 适用于 .NET、JavaScript （客户端和 node.js）、Python、iOS、Android 和 Java 的单独库。</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>对连接到现场网关的设备进行身份验证

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | IoT 现场网关 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用  |
| **参考**              | 不可用  |
| **步骤** | 确保现场网关在接受每个设备的数据以及帮助上游与云网关通信之前，对每个设备进行身份验证。 此外，确保设备使用每个设备的凭据建立连接，以便能够唯一标识各个设备。|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>确保对连接到云网关的设备进行身份验证

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | IoT 云网关 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型、C#、Node.JS,  |
| **特性**              | 不适用，网关选项 - Azure IoT 中心 |
| **参考**              | 不适用，[包含 .net 的 Azure IoT 中心](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/)，[入门 Iot 中心和 NODE.JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted)，使用[SAS 和证书保护 IoT](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/)， [Git 存储库](https://github.com/Azure/azure-iot-sdks/) |
| **步骤** | <ul><li>**泛型：** 使用传输层安全性 (TLS) 或 IPSec 对设备进行身份验证。 如果设备无法处理完全非对称加密，则基础结构应该支持在这些设备上使用预共享密钥 (PSK)。 利用 Azure AD、Oauth。</li><li>**C#：** 创建 DeviceClient 实例时，Create 方法默认创建使用 AMQP 协议来与 IoT 中心通信的 DeviceClient 实例。 要使用 HTTPS 协议，请使用 Create 方法的重写，它可以让你指定协议。 如果使用 HTTPS 协议，则还应在项目中添加 `Microsoft.AspNet.WebApi.Client` NuGet 包，以包含 `System.Net.Http.Formatting` 命名空间。</li></ul>|

### <a name="example"></a>示例
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>示例
**Node.JS：身份验证**
#### <a name="symmetric-key"></a>对称密钥
* 在 azure 上创建 IoT 中心
* 在设备标识注册表中创建条目
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* 创建模拟设备
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS 令牌
* 使用对称密钥时会在内部生成，但我们也可以显式生成并使用它
* 定义协议：`var Http = require('azure-iot-device-http').Http;`
* 创建 SAS 令牌：
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* 使用 SAS 令牌建立连接：
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>证书
* 使用 OpenSSL 等任何工具生成自签名的 X509 证书，以便生成分别用于存储证书和密钥的 .cert 和 .key 文件
* 使用证书预配接受安全连接的设备。
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* 使用证书连接设备
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    };
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>使用每个设备的身份验证凭据

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | IoT 云网关  |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 网关选项 - Azure IoT 中心 |
| **参考**              | [Azure IoT 中心安全令牌](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **步骤** | 结合基于 SAS 令牌的设备密钥或客户端证书使用每个设备的身份验证凭据，而不要使用 IoT 中心级别的共享访问策略。 这可以防止其他人重复使用一个设备或现场网关的身份验证令牌 |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>确保只对所需的容器和 Blob 授予匿名读取访问权限

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Azure 存储 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | StorageType - Blob |
| **参考**              | [管理对容器和 Blob 的匿名读取访问](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/)、[共享访问签名，第 1 部分：了解 SAS 模型](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **步骤** | <p>默认情况下，仅存储帐户的所有者能够访问容器以及其中的所有 Blob。 若要授予匿名用户对容器及其 Blob 的读取权限，可以设置容器权限以允许公共访问。 匿名用户可以读取可公开访问的容器中的 Blob，而无需对请求进行身份验证。</p><p>容器提供了下列用于管理容器访问的选项：</p><ul><li>完全公共读取访问：可通过匿名请求读取容器和 Blob 数据。 客户端可以通过匿名请求枚举容器中的 Blob，但无法枚举存储帐户中的容器。</li><li>仅针对 Blob 的公共读取访问：可通过匿名请求读取此容器中的 Blob 数据，但容器数据不可用。 客户端无法通过匿名请求枚举容器中的 Blob</li><li>无公共读取访问：仅帐户所有者可读取容器和 Blob 数据</li></ul><p>如果想要始终允许对某些 Blob 进行匿名读取访问，最好是启用匿名访问。 若要进行更精细的控制，可以创建一个共享访问签名，这样便可使用不同的权限在指定时间间隔内委派受限访问。 确保不要意外地授予对可能包含敏感数据的容器和 Blob 的匿名访问权限</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>使用 SAS 或 SAP 对 Azure 存储中的对象授予受限的访问权限

| Title                   | 详细信息      |
| ----------------------- | ------------ |
| 组件                | Azure 存储 |
| **SDL 阶段**               | 构建 |
| **适用的技术** | 泛型 |
| **特性**              | 不可用 |
| **参考**              | [共享访问签名，第 1 部分：了解 SAS 模型](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)、[共享访问签名，第 2 部分：创建 SAS 并将 SAS 用于 Blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/)、[如何使用共享访问签名和存储访问策略来委派对帐户中对象的访问权限](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **步骤** | <p>使用共享访问签名 (SAS) 是将对存储帐户中对象的受限访问权限授予其他客户端且不必公开帐户访问密钥的一种高度有效的方法。 SAS 是在其查询参数中包含对存储资源进行验证了身份的访问所需的所有信息的 URI。 要使用 SAS 访问存储资源，客户端只需将 SAS 传入到相应的构造函数或方法。</p><p>需要将存储帐户中资源的访问权限提供给不能使用帐户密钥进行信任的客户端时，可以使用 SAS。 存储帐户密钥包括主密钥和辅助密钥，这两种密钥都授予对帐户以及其中所有资源的管理访问权限。 公开这两种帐户密钥的任何一种都会向可能的恶意或负面使用开放帐户。 共享访问签名提供一种安全的方法，允许其他客户端根据你授予的权限读取、写入和删除存储帐户中的数据，而无需帐户密钥。</p><p>如果每次都有一组类似的逻辑参数，使用存储访问策略 (SAP) 是个不错的想法。 由于使用派生自存储访问策略的 SAS 可以立即吊销该 SAS，因此建议的最佳做法是始终使用存储访问策略（如果可能）。</p>|
