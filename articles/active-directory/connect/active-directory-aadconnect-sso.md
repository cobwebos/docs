---
title: "Azure AD Connect：无缝单一登录 | Microsoft Docs"
description: "本主题介绍 Azure Active Directory (Azure AD) 无缝单一登录，以及如何使用它来为企业网络中的企业桌面用户提供真正的单一登录。"
services: active-directory
keywords: "什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: b3eebdd714b38ffd9432404944829d05ef3c3dc6
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory 无缝单一登录

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>什么是 Azure Active Directory 无缝单一登录？

Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）为登录到企业网络中连接的企业台式机的用户提供真正的单一登录。 启用此功能后，用户无需键入其密码即可登录到 Azure AD；在大多数情况下，甚至无需键入其用户名。 此功能可让用户轻松访问基于云的服务，而无需使用其他任何本地组件。

无缝 SSO 可通过 Azure AD Connect 启用，可与[密码同步](active-directory-aadconnectsync-implement-password-synchronization.md)或[直通身份验证](active-directory-aadconnect-pass-through-authentication.md)结合使用。

>[!NOTE]
>此功能不适用于 Active Directory 联合身份验证服务 (ADFS)，因为其中已提供此功能。

要对特定的用户使用此功能，需要满足以下条件：

- 该用户正在登录到某个企业桌面。
- 该桌面事先已加入 Active Directory (AD) 域。
- 该桌面已通过远程访问连接（例如 VPN 连接）与企业有线或无线网络中的域控制器 (DC) 建立了直接连接。
- 已将我们的服务终结点包含在浏览器的 Intranet 区域中。

如果不满足上述任一条件，系统会像以往一样提示用户输入其用户名和密码。

![无缝单一登录](./media/active-directory-aadconnect-sso/sso1.png)

## <a name="whats-available-during-preview"></a>预览版提供哪些功能？

>[!NOTE]
>Azure AD 无缝 SSO 目前以预览版提供。 这是一个免费功能，你不需要拥有任何付费版本的 Azure AD 即可使用此功能。

在能够进行 Kerberos 身份验证的台式机（例如基于 Windows 的台式机）上，通过支持[新式身份验证](https://aka.ms/modernauthga)的基于 Web 浏览器的客户端和 Office 客户端支持无缝 SSO。 下表详细描述了各个操作系统上基于浏览器的客户端。

| 操作系统\浏览器 |Internet Explorer|Google Chrome|Mozilla Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|是|是|是\*|否
|Windows 8.1|是|是|是\*|不适用
|Windows 8|是|是|是\*|不适用
|Windows 7|是|是|是\*|不适用
|Mac OS X|不适用|是\*|是\*|不适用

\*需要额外的配置。

>[!NOTE]
>对于 Windows 10，建议使用 [Azure AD join](../active-directory-azureadjoin-overview.md)，以获得最佳的 Azure AD 体验。

如果 Azure AD 登录请求包括 `domain_hint` 或 `login_hint` 参数（由租户中的应用程序发起），则无缝 SSO 将利用它，并且用户将可免于输入其用户名和密码。

## <a name="how-does-azure-ad-seamless-sso-work"></a>Azure AD 无缝 SSO 的工作原理

可按[下面](#how-to-enable-azure-ad-seamless-sso?)所示在 Azure AD Connect 中启用无缝 SSO。 启用此功能后，将在本地 Active Directory (AD) 中创建名为 AZUREADSSOACCT 的计算机帐户，并与 Azure AD 安全共享其 Kerberos 解密密钥。 此外，将创建两个 Kerberos 服务主体名称 (SPN) 来表示 Azure AD 登录期间使用的两个服务 URL。

>[!NOTE]
> 需要在与 Azure AD 同步（通过 Azure AD Connect）且要为其用户启用无缝 SSO 的每个 AD 林中创建计算机帐户和 Kerberos SPN。 如果 AD 林具有计算机帐户的组织单位 (OU)，在启用无缝 SSO 功能后，请将 AZUREADSSOACCT 计算机帐户移到一个 OU，以确保它不会被删除，并且与其他计算机帐户以相同方式进行管理。

完成此设置后，Azure AD 登录的工作方式与其他任何使用 Windows 集成身份验证 (IWA) 的登录方式相同。 无缝 SSO 的过程如下：

假设你的用户正在尝试访问某个由 Azure AD 保护的基于云的资源，例如 SharePoint Online。 SharePoint Online 会将该用户的浏览器重定向到 Azure AD 以便登录。

如果向 Azure AD 发出的登录请求包含 `domain_hint`（标识 Azure AD 租户，例如 contoso.onmicrosoft.com）或 `login_hint`（标识用户的用户名，例如 user@contoso.onmicrosoft.com 或 user@contoso.com）参数，则执行步骤 1-5。

如果请求中不包含上述任何参数，则在 Azure AD 登录页上，系统将要求该用户提供其用户名。 仅当用户用 tab 键将焦点移出用户名字段或单击“继续”按钮后，才会执行步骤 1-5。

1. Azure AD 通过“401 未授权”响应质询客户端，以提供 Kerberos 票证。
2. 客户端从 Active Directory 请求 Azure AD 的票证（由以前设置的计算机帐户表示）。
3. Active Directory 查找计算机帐户，然后将使用计算机帐户机密加密的 Kerberos 票证返回给客户端。 该票证包含当前登录到该桌面的用户的标识。
4. 客户端将其从 Active Directory 获取的 Kerberos 票证发送到 Azure AD。
5. Azure AD 使用以前共享的密钥解密 Kerberos 票证， 如果成功，Azure AD 将会向用户返回令牌或者要求用户执行其他验证，例如资源所需的多重身份验证。

无缝 SSO 是个机会型功能，这意味着，如果由于任何原因失败，用户登录体验将回退到其常规行为 - 即，用户将需要在登录页面上输入其密码。

下图也说明了此过程：

![无缝单一登录](./media/active-directory-aadconnect-sso/sso2.png)

## <a name="how-to-enable-azure-ad-seamless-sso"></a>如何启用 Azure AD 无缝 SSO？

### <a name="pre-requisites"></a>先决条件

如果要结合直通身份验证启用无缝 SSO，除了直通身份验证功能所需的项以外，不需要满足其他任何先决条件。

如果要结合密码同步启用无缝 SSO，并且 Azure AD Connect 与 Azure AD 之间有防火墙，请确保：

- Azure AD Connect 服务器可与 `*.msappproxy.net` URL 通信。
- Azure AD Connect（版本 1.1.484.0 或更高）可以通过端口 443 向 Azure AD 发出 HTTPS 请求。 此条件仅适用于启用该功能，而不适用于实际用户登录。
- Azure AD Connect 还可以与 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=41653)建立直接 IP 连接。 同样，这仅用于启用该功能。

>[!NOTE]
> 旧版 Azure AD Connect（版本低于 1.1.484.0）需要能够通过端口 9090 与 Azure AD 通信。

### <a name="enabling-the-azure-ad-seamless-sso-feature"></a>启用 Azure AD 无缝 SSO 功能

可通过 Azure AD Connect 启用 Azure AD 无缝 SSO。

如果你要执行 Azure AD Connect 全新安装，请选择“自定义安装路径”。[](active-directory-aadconnect-get-started-custom.md) 在“用户登录”页上，选中“启用单一登录”选项。

![Azure AD Connect - 用户登录](./media/active-directory-aadconnect-sso/sso8.png)

如果你已使用[快速安装](active-directory-aadconnect-get-started-express.md)或[自定义安装](active-directory-aadconnect-get-started-custom.md)路径安装了 Azure AD Connect，请在 Azure AD Connect 上选择“更改用户登录页”并单击“下一步”。 然后选中“启用单一登录”选项。

![Azure AD Connect - 更改用户登录](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

在安装向导中继续操作，直到出现“启用单一登录”页。 需要提供与 Azure AD 同步的（通过 Azure AD Connect）的每个 AD 林以及要启用无缝 SSO 的用户的域管理员凭据。 请注意，域管理员凭据不会存储在 Azure AD Connect 或 Azure AD 中，而只会用于创建计算机帐户和配置 Kerberos SPN，如前所述。

此时，已在租户中启用无缝 SSO。 请注意，只有在完成下一步部分中的步骤之后，你的用户才可以利用此功能。

## <a name="rolling-the-feature-out-to-your-users"></a>向用户实施该功能

若要将无缝 SSO 功能扩展到你的用户，你需要通过 Active Directory 中的组策略将两个 Azure AD URL（https://autologon.microsoftazuread-sso.com 和 https://aadg.windows.net.nsatc.net）添加到用户的 Intranet 区域设置。 请注意，这仅适用于 Internet Explorer 和 Google Chrome（如果它与 Internet Explorer 共用一组相同的受信任站点 URL）。 你需要为 Mozilla Firefox 单独配置。

### <a name="why-do-you-need-this"></a>为什么需要这样做？

默认情况下，浏览器不会将 Kerberos 票证发送到云终结点，除非该终结点的 URL 已定义为浏览器 Intranet 区域的一部分。 浏览器会自动从该 URL 计算适当的区域（Internet 或 Intranet）。 例如，http://contoso/ 将映射到 Intranet 区域，而 http://intranet.contoso.com/ 将映射到 Internet 区域（因为 URL 包含句点）。

由于用于无缝 SSO 的 Azure AD URL 包含句点，因此需要将这些 URL 显式添加到每个浏览器的 Intranet 区域设置。 这样，浏览器便可以自动将当前登录用户的 Kerberos 票证发送到 Azure AD。 虽然可以在每个台式机上手动执行此操作，但是将所需 URL 添加到所有用户的 Intranet 区域的最简单方法是在 Active Directory 中创建组策略。

### <a name="detailed-steps"></a>详细步骤

1. 打开“组策略管理”工具。
2. 编辑已应用到所有用户的组策略，例如“默认域策略”。
3. 导航到“用户配置\管理模板\Windows 组件\Internet Explorer\Internet 控制面板\安全性”页面，并选择“区域分配列表的站点”。
![单一登录](./media/active-directory-aadconnect-sso/sso6.png)  
4. 启用策略，并在对话框中输入以下值/数据。 这是 Kerberos 票证要发送到的 Azure AD URL。

        Value: https://autologon.microsoftazuread-sso.com  
        Data: 1  
        Value: https://aadg.windows.net.nsatc.net  
        Data: 1  
5. 请单击两次“确定”。

它看起来应该如下所示：

![单一登录](./media/active-directory-aadconnect-sso/sso7.png)

>[!NOTE]
>默认情况下，Chrome 使用和 Internet Explorer 相同的一组受信任的站点 URL。 如果为 Chrome 配置了不同的设置，需要单独更新这些设置。

## <a name="troubleshooting-seamless-sso"></a>排查无缝 SSO 问题

使用以下查检表排查无缝 SSO 的问题：

1. 在 Azure AD Connect 工具中检查是否已在租户中启用无缝 SSO 功能。 如果无法启用该功能（例如，由于端口被阻止），请确保事先满足所有[先决条件](#pre-requisites)。 如果启用该功能时仍出现问题，请联系 Microsoft 支持部门。
2. 已将两个服务 URL（https://autologon.microsoftazuread-sso.com 和 https://aadg.windows.net.nsatc.net）定义为 Intranet 区域设置的一部分。
3. 确保企业桌面已加入 AD 域。
4. 确保用户使用 AD 域帐户登录到桌面。
5. 确保用户的帐户来自已设置无缝 SSO 的 AD 林。
6. 确保已在企业网络中连接该桌面。
7. 确保桌面的时间与 Active Directory 同步，并且各域控制器的时间偏差不超过 5 分钟。
8. 从用户的桌面中清除现有的 Kerberos 票证。 为此，可以在命令提示符下运行 **klist purge** 命令。
9. 查看浏览器的控制台日志（在“开发人员工具”下）来帮助确定潜在问题。

### <a name="domain-controller-logs"></a>域控制器日志

如果在域控制器上成功启用审核，则每当用户使用无缝 SSO 登录时，将在事件日志中记录一个安全条目（与计算机帐户 **AzureADSSOAcc$** 关联的事件 4769）。 可以使用以下查询来查找这些安全事件：

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

