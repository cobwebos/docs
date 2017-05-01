---
title: "Azure AD Connect：直通身份验证 | Microsoft Docs"
description: "本文介绍 Azure Active Directory (Azure AD) 直通身份验证，以及它如何通过针对本地 Active Directory 验证用户密码来实现 Azure AD 登录。"
services: active-directory
keywords: "什么是 Azure AD Connect 直通身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 0f54fb7d2d8cf010baf79409bc6a528d34982500
ms.lasthandoff: 04/22/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>使用 Azure Active Directory 直通身份验证配置用户登录

## <a name="what-is-azure-active-directory-azure-ad-pass-through-authentication"></a>什么是 Azure Active Directory (Azure AD) 直通身份验证？

如果能够允许用户使用相同的凭据（密码）登录到本地资源和基于云的服务，则对用户和组织而言都非常有利。 用户可以少记住一个密码。 这还可以提供更佳的用户体验，减少用户忘记登录方式的可能性。 这种优势又可以降低技术支持成本，因为密码相关的问题通常会占用大多数支持资源。

许多组织使用 [Azure AD 密码同步](active-directory-aadconnectsync-implement-password-synchronization.md)（一项用于将用户密码从本地 Active Directory 同步到 Azure AD 的 [Azure AD Connect](active-directory-aadconnect.md) 功能）向用户提供相同的凭据，让他们在各种本地资源和基于云的服务中使用。 但是，有些组织要求密码（即使采用哈希处理的形式）不得离开其内部组织边界。

Azure AD 直通身份验证为这些组织提供一个简单的解决方案。 当用户登录到 Azure AD 时，它可以确保针对本地 Active Directory 直接验证用户的密码。 此功能还提供以下优势：

- 易于使用
  - 无需复杂的本地部署或网络配置即可执行密码验证。
  - 它只利用用于侦听和响应密码验证请求的轻型本地连接器。
  - 它可以连同 [Azure AD Connect](active-directory-aadconnect.md) 一起进行配置。 轻型本地连接器安装在 Azure AD Connect 所在的同一台服务器上。
- 安全
  - 本地密码永远不会以任何形式存储在云中。
  - 轻型本地连接器只从你的网络内部建立出站连接。 因此，不需要在外围网络中安装连接器。
  - 直通身份验证与 Azure 多重身份验证无缝配合。
- 可靠且可缩放
  - 可在多台服务器上安装其他轻型本地连接器，实现登录请求的高可用性。

![Azure AD 直通身份验证](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

与[无缝单一登录](active-directory-aadconnect-sso.md)功能结合使用时，如果用户位于你的企业网络中的企业计算机上，则他们甚至不需要键入密码即可登录到 Azure AD。

## <a name="whats-available-during-preview"></a>预览版提供哪些功能？

>[!NOTE]
>Azure AD 直通身份验证目前以预览版提供。 这是一个免费功能，你不需要拥有任何付费版本的 Azure AD 即可使用此功能。 

预览版完全支持以下方案：

- 所有基于 Web 浏览器的应用程序。
- 支持[新式身份验证](https://aka.ms/modernauthga)的 Office 365 客户端应用程序。

预览版不支持以下方案：

- 旧式 Office 客户端应用程序和 Exchange ActiveSync（即，移动设备上的本机电子邮件应用程序）。
  - 我们建议组织在可能的情况下改用新式身份验证。 这样不仅可以获得直通身份验证支持，而且还可以帮助你使用[条件访问](../active-directory-conditional-access.md)功能（例如多重身份验证）保护标识。
- 适用于 Windows 10 设备的 Azure AD Join。

>[!IMPORTANT]
>当你启用直通身份验证时，默认还会启用密码同步，这样就可以实现直通身份验证目前尚不支持的方案（旧式 Office 客户端应用程序、Exchange ActiveSync 和适用于 Window 10 设备的 Azure AD Join）。 密码同步只在这些特定的方案中充当回退机制。 如果不需要密码同步，可以在 Azure AD Connect 中的“可选功能”页上将它关闭。[](active-directory-aadconnect-get-started-custom.md#optional-features)

## <a name="how-to-enable-azure-ad-pass-through-authentication"></a>如何启用 Azure AD 直通身份验证？

### <a name="pre-requisites"></a>先决条件

在启用 Azure AD 直通身份验证之前，需要事先满足以下先决条件：

- 一个 Azure AD 租户（你是该租户的全局管理员）。

>[!NOTE]
>建议使用仅限云的全局管理员帐户，以便在本地服务出现故障或不可用时管理租户的配置。 可根据[此处](../active-directory-users-create-azure-portal.md)所示添加仅限云的全局管理员帐户。

- Azure AD Connect 1.1.484.0 或更高版本。 建议使用[最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。
- 运行 Windows Server 2012 R2 或更高版本的服务器，在其上可运行 Azure AD Connect。
  - 此服务器必须是需要验证其密码的用户所在的 AD 林的成员。
  - 请注意，需要在 Azure AD Connect 所在的同一台服务器上安装连接器。

>[!NOTE]
>如果 AD 林之间存在信任关系并且正确配置了名称后缀路由，则可支持多林环境。

- 若要获得高可用性，需在运行 Windows Server 2012 R2 或更高版本的其他服务器上安装独立的连接器。
- 如果任何连接器与 Azure AD 之间存在防火墙，请确保：
    - 如果启用了 URL 筛选，请确保连接器能够与以下 URL 通信：
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - 连接器还与 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=41653)建立直接 IP 连接。
    - 确保当连接器使用客户端证书与 Azure AD 通信时，防火墙不执行 SSL 检查。
    - 确保连接器可通过端口 80 和 443 向 Azure AD 发出 HTTPS (TCP) 请求。
      - 如果防火墙根据发起用户强制实施规则，请针对来自作为网络服务运行的 Windows 服务的流量打开这些端口。

>[!NOTE]
>我们最近做了改进，减少了连接器与我们的服务通信时所需的端口数。 如果运行旧版 Azure AD Connect 和/或独立连接器，应继续保持打开这些附加端口（5671、8080、9090、9091、9350、9352、10100-10120）。

### <a name="enabling-azure-ad-pass-through-authentication"></a>启用 Azure AD 直通身份验证

可通过 Azure AD Connect 启用 Azure AD 直通身份验证。

如果你要执行 Azure AD Connect 全新安装，请选择“自定义安装路径”。[](active-directory-aadconnect-get-started-custom.md) 在“用户登录”页上，选择“直通身份验证”。 成功完成上述步骤后，将在 Azure AD Connect 所在的同一台服务器上安装直通身份验证连接器。 此外，还会在租户中启用直通身份验证功能。

![Azure AD Connect - 用户登录](./media/active-directory-aadconnect-sso/sso3.png)

如果你已使用[快速安装](active-directory-aadconnect-get-started-express.md)或[自定义安装](active-directory-aadconnect-get-started-custom.md)路径安装了 Azure AD Connect，请在 Azure AD Connect 上选择“更改用户登录页”并单击“下一步”。 然后选择“直通身份验证”，在 Azure AD Connect 所在的同一台服务器上安装直通身份验证连接器并在租户中启用该功能。

![Azure AD Connect - 更改用户登录](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Azure AD 直通身份验证是租户级功能。 它会影响租户中所有托管域上的用户登录。 但是，联合域中的用户将继续使用 Active Directory 联合身份验证服务 (ADFS) 或你以前配置的其他任何联合身份验证提供程序登录。 如果你将联合域转换为托管域，该域中的所有用户将自动开始使用直通身份验证登录。 仅限云的用户不受直通身份验证的影响。

### <a name="ensuring-high-availability"></a>确保高可用性

如果你打算在生产部署中使用直通身份验证，我们强烈建议在独立的服务器（不是运行 Azure AD Connect 和第一个连接器的服务器）上安装另一个连接器，确保获得登录请求的高可用性。 可以安装任意数目的附加连接器；具体的数目取决于租户处理的登录请求的峰值数目和平均数目。

遵照以下说明部署独立的连接器：

#### <a name="step-1-download-and-install-the-connector"></a>步骤 1：下载并安装连接器

此步骤在服务器中下载并安装连接器软件。

1.    [下载](https://go.microsoft.com/fwlink/?linkid=837580)最新的连接器。
2.    以管理员身份打开命令提示符。
3.    运行以下命令（/q 表示静默安装 - 安装程序不会提示你接受最终用户许可协议）：

`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`
>[!NOTE]
>只能在每台服务器上安装一个连接器。

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>步骤 2：将连接器注册到 Azure AD 进行直通身份验证

此步骤将服务器上安装的连接器注册到我们的服务，以便可以使用它来接收登录请求。

1.    以管理员身份打开 PowerShell 窗口。
2.    导航到 **C:\Program Files\Microsoft AAD App Proxy Connector**，然后按如下所示运行脚本：`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    出现提示时，请输入 Azure AD 租户中全局管理员帐户的凭据。

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Azure AD 直通身份验证的工作原理

当用户尝试登录到 Azure AD 时（如果已在租户中启用直通身份验证），将发生以下情况：

1. 用户在 Azure AD 登录页中输入其用户名和密码。 我们的服务将该用户名和密码（已使用公钥加密）排入队列等待验证。
2. 某个可用的本地连接器向该队列发出出站调用，并检索该用户名和密码。
3. 然后，连接器使用标准 Windows API（类似于 ADFS 使用的机制）针对 Active Directory 验证该用户名和密码。 请注意，用户名可以是本地默认用户名（通常为“userPrincipalName”），也可以是 Azure AD Connect 中配置的另一个属性（称为“备用 ID”）。
4. 然后，本地域控制器评估请求并向连接器返回响应（成功或失败结果）。
5. 连接器随后将此响应返回给 Azure AD。
6. 然后，Azure AD 评估结果，并相应地向用户提供响应。 例如，向应用程序发回令牌或者要求执行多重身份验证。

下图也演示了各个步骤。 请注意，所有请求和响应都是通过 HTTPS 发出的。

![直通身份验证](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="note-about-password-writeback"></a>有关密码写回的说明

如果你已在租户中以及针对特定的用户配置[密码写回](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)，则当用户使用直通身份验证登录时，可像以往那样更改或重置其密码。 密码将按预期写回到本地 Active Directory。

但是，如果不满足其中一个条件（未在租户中配置密码写回，或者没有为用户分配有效的 Azure AD 许可证），则不允许用户在云中更新其密码（密码过期时也是如此）。 用户将看到如下所示的消息：“你的组织不允许你更新此站点上的密码”。 请根据组织建议的方法更新密码，或者请求管理员提供帮助。”

## <a name="troubleshooting-pass-through-authentication"></a>直通身份验证故障排除

本部分将帮助你查找有关排查安装、注册或卸载直通身份验证连接器（通过 Azure AD Connect 或独立的连接器）期间，以及在租户中启用和操作该功能期间 遇到的某些常见问题的信息。

### <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>安装连接器（通过 Azure AD Connect 或独立的连接器）期间遇到的问题

#### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Azure AD 应用程序代理连接器已存在

不能在 [Azure AD 应用程序代理](../../active-directory/active-directory-application-proxy-get-started.md)连接器所在的同一台服务器上安装直通身份验证连接器。 需要在独立的服务器上安装直通身份验证连接器。

#### <a name="an-unexpected-error-occured"></a>发生了意外的错误

从服务器[收集连接器日志](#how-to-collect-pass-through-authentication-connector-logs?)，然后联系 Microsoft 支持人员反映问题。

### <a name="issues-during-registration-of-connectors"></a>注册连接器期间遇到的问题

#### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>由于端口被阻止，连接器注册失败

确保安装连接器的服务器能够与我们的服务 URL 和[此处](#pre-requisites)列出的端口通信。

#### <a name="an-unexpected-error-occurred"></a>发生了意外的错误

从服务器[收集连接器日志](#how-to-collect-pass-through-authentication-connector-logs?)，然后联系 Microsoft 支持人员反映问题。

### <a name="issues-during-un-installation-of-connectors"></a>卸载连接器期间遇到的问题

#### <a name="warning-message-when-un-installing-azure-ad-connect"></a>卸载 Azure AD Connect 时出现警告消息

如果在租户中启用了直通身份验证，则当你尝试卸载 Azure AD Connect 时，会显示以下警告消息：“除非已在其他服务器上安装其他直通身份验证代理，否则用户无法登录到 Azure AD。”

在卸载 Azure AD Connect 之前，需要事先设置[高可用性](#ensuring-high-availability)，以免影响用户登录。

### <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>启用直通身份验证功能期间遇到的问题

#### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>由于没有可用的连接器，启用该功能失败

必须至少有一个活动的连接器服务器才能在租户中启用直通身份验证。 可通过安装 Azure AD Connect 或安装独立的连接器来安装连接器。

#### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>由于端口被阻止，启用该功能失败

确保安装 Azure AD Connect 的服务器能够与我们的服务 URL 和[此处](#pre-requisites)列出的端口通信。

### <a name="issues-while-operating-the-pass-through-authentication-feature"></a>操作直通身份验证功能期间遇到的问题

#### <a name="user-facing-sign-in-errors"></a>面向用户的登录错误

该功能在 Azure AD 在登录屏幕中报告了以下面向用户的错误。 下面详细描述了这些错误及其相应的解决步骤。

|错误|说明|解决方法
| --- | --- | ---
|AADSTS80001|无法连接到 Active Directory|确保连接器服务器是需要验证其密码的用户所在的 AD 林的成员，并且能够连接到 Active Directory。  
|AADSTS8002|连接到 Active Directory 时超时|检查以确保 Active Directory 可用，并且可以响应连接器的请求。
|AADSTS80004|传递到连接器的用户名无效|确保用户尝试使用正确的用户名登录。
|AADSTS80005|验证遇到了不可预知的 WebException|这可能是一个暂时性的错误。 重试请求。 如果持续失败，请与 Microsoft 支持人员联系。
|AADSTS80007|与 Active Directory 通信时出错|检查连接器日志以了解更多信息，并验证 Active Directory 是否按预期方式运行。

### <a name="how-to-collect-pass-through-authentication-connector-logs"></a>如何收集直通身份验证连接器日志？

根据遇到的问题类型，需要在不同的位置查看直通身份验证连接器日志。

#### <a name="connector-event-logs"></a>连接器事件日志

对于与连接器相关的错误，请在服务器上打开事件查看器应用程序，然后检查 **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin** 中的日志。

若要获取详细的分析和调试日志，可以启用“会话”日志。 在正常操作期间，请不要在启用此日志的情况下运行连接器；只将此日志用于故障排除。 请注意，日志内容只会在再次禁用日志后才可见。

#### <a name="detailed-trace-logs"></a>详细跟踪日志

若要排查用户登录失败，请查看 **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace** 中的跟踪日志。 这些日志包含特定用户使用直通身份验证功能登录失败的原因。 下面提供了示例日志条目：

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

可以通过打开命令提示符并运行以下命令来获取错误（在上述示例中为错误“1328”）的描述性详细信息。 注意：需要将“1328”替换为日志中显示的实际错误编号。

`Net helpmsg 1328`

结果应如下所示：

![直通身份验证](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

#### <a name="domain-controller-logs"></a>域控制器日志

如果已启用审核日志记录，可以在域控制器的安全日志中找到更多信息。 下面演示了一种查询直通身份验证连接器发送的登录请求的简单方法：

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="feedback"></a>反馈

你的反馈对我们非常重要。 欢迎给我们发送电子邮件：[aadopauthfeedback@microsoft.com](mailto:aadopauthfeedback@microsoft.com)。 如果你要请求添加新的功能，请在我们的 [UserVoice 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)中发贴 - 我们将会关注。

