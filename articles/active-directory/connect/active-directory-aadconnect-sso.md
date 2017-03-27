---
title: "Azure AD Connect：单一登录 | Microsoft Docs"
description: "本主题介绍了你需要了解的如何从本地 Active Directory (AD) 单一登录到基于云的 Azure Active Directory (Azure AD) 和连接的服务的信息。"
services: active-directory
keywords: "什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e208b2bf861d698901b287458a3969e833540e44
ms.openlocfilehash: f8f67af3cb6adb333924714bd758609b950845af
ms.lasthandoff: 02/17/2017

---

# <a name="what-is-single-sign-on-sso-preview"></a>什么是单一登录 (SSO)（预览）
单一登录是可以在 Azure Active Directory Connect 中启用的用于[密码同步](active-directory-aadconnectsync-implement-password-synchronization.md)或[直通身份验证](active-directory-aadconnect-pass-through-authentication.md)的选项。 如果启用了该选项，那么当用户使用其公司的计算机并连接到公司网络时，只需键入其用户名（不需键入密码）即可登录到 Azure Active Directory (Azure AD) 或其他云服务。

![单一登录](./media/active-directory-aadconnect-sso/sso1.png)

向最终用户提供 SSO 功能后，对基于云的服务的访问更加熟悉，并向组织提供安全而简单的流程，无需其他任何本地的组件。

SSO 是通过 Azure AD Connect 启用的一项功能，该功能用于密码同步或直通身份验证和本地 Active Directory。 若要让最终用户在环境中使用单一登录，需要确保用户：

- 在已加入域的计算机上
- 具有与域控制器的直接连接，例如在公司的有线或无线网络上，或使用了远程访问连接，如 VPN 连接。
- 将云中的 Kerberos 终结点定义为浏览器 Intranet 区域的一部分。

如果不满足上述任何一项要求，如计算机不在公司网络上，则会提示用户输入其密码，就和没有单一登录时一样。

## <a name="supported-clients"></a>支持的客户端
在能够进行 Kerberos 身份验证的计算机（如 Windows 计算机）上，支持[新式身份验证](https://aka.ms/modernauthga)的基于 Web 浏览器的客户端和 Office 客户端支持单一登录。 下表详细描述了各个操作系统上基于浏览器的客户端。

| 操作系统\浏览器 |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|是|是|是*|否
|Windows 8.1|是|是|是*|不适用
|Windows 8|是|是|是*|不适用
|Windows 7|是|是|是*|不适用
|Mac|不适用|不适用|不适用|不适用

\*需要单独的配置。

>[!NOTE]
>对于 Windows 10 客户端，建议使用 [Azure AD join](../active-directory-azureadjoin-overview.md)，以获取 Azure AD 的最佳体验。

## <a name="how-single-sign-on-works"></a>单一登录的使用方式

在 Azure AD Connect 中启用单一登录后，在本地 Active Directory 中创建名为 AZUREADSSOACCT 的计算机帐户，并与 Azure AD 安全地共享 Kerberos 解密密钥。 此外，创建了两个 Kerberos 服务主体名称 (SPN) 来表示云 URL，客户端与 Azure AD 之间的身份验证过程使用此 URL。

此设置完成后，身份验证过程与任何其他基于集成 Windows 身份验证 (IWA) 的应用程序相同。 如果熟悉 IWA 的使用过程，那么你就知道如何在 Azure AD 中使用单一登录。 如果不熟悉，IWA 的过程如下：

![单一登录](./media/active-directory-aadconnect-sso/sso2.png)

首先，用户尝试访问一项资源，该资源信任从 Azure AD（例如 SharePoint Online）颁发的令牌。 SharePoint Online 然后通过重定向方式让用户使用 Azure AD 进行身份验证。 然后，用户提供其用户名，这样 Azure AD 就可以确定用户的组织是否已启用单一登录。 假定已为组织启用单一登录，则会出现以下通信。

1.    Azure AD 通过“401 未授权”响应质询客户端，以提供 Kerberos 票证。
2.    客户端为 Azure AD 从 Active Directory 请求票证。
3.    Active Directory 定位 Azure AD Connect 所创建的计算机帐户，并将使用此计算机帐户的密码加密的 Kerberos 票证返回给客户端。 此票证包括当前登录到此计算机的用户的标识。
4.    客户端将其从 Active Directory 获取的 Kerberos 票证发送到 Azure AD。
5.    Azure AD 使用以前共享的密钥解密 Kerberos 票证， 然后向用户返回令牌或要求用户提供其他证明，例如资源所需的多重身份验证。

单一登录是机会性功能，这表示如果因为某些原因无法使用该功能，用户只需和平常一样在登录页输入密码即可。

## <a name="single-sign-on-sso-prerequisites"></a>单一登录 (SSO) 先决条件
如果要通过直通身份验证启用单一登录，除了直通身份验证所需项以外，不需要任何其他先决条件。

如果要通过密码同步启用单一登录，并且如果 Azure AD Connect 和 Azure AD 之间有防火墙，请确保：
- Azure AD Connect 服务器可以与 *.msappproxy.net 通信
- Azure AD Connect 可在以下端口上向 Azure AD 发出 HTTPS 请求：

|协议|端口号|说明
| --- | --- | ---
|HTTPS|9090|    启用 SSO 注册（只有 SSO 注册过程才需要）。

## <a name="enabling-sso-with-pass-through-authentication-or-password-sync"></a>为直通身份验证或密码同步启用 SSO
Azure AD Connect 提供了简单的过程来为直通身份验证或密码同步启用单一登录。 请确保具有同步的每个林中一个域的域管理员权限，以便为计算机帐户配置 Kerberos 服务主体名称 (SPN)。 用户名和密码未存储在 Azure AD Connect 或 Azure AD 中，并且仅用于此操作。

安装 Azure AD Connect 时选择自定义安装，以便能够在用户登录页面上选择单一登录选项。 有关详细信息，请参阅 [Azure AD Connect 的自定义安装](active-directory-aadconnect-get-started-custom.md)。

![单一登录](./media/active-directory-aadconnect-sso/sso3.png)

启用单一登录后，可以继续完成安装向导，直到到达单一登录页面。

![单一登录](./media/active-directory-aadconnect-sso/sso4.png)

对于列出的每个林，提供相应的帐户详细信息，并为 Azure 目录启用单一登录。

>[!NOTE]
>若要配置 SSO，Azure AD Connect 需要能够与 \*.msappproxy.net 在端口 9090 (TCP) 上通信。 仅在配置过程中需要这种开放端口的操作，在身份验证过程中最终用户不需要使用此操作。

## <a name="ensuring-clients-sign-in-automatically"></a>确保客户端自动登录
默认情况下，浏览器不会尝试将凭据发送到 Web 服务器，除非 URL 定义为在 Intranet 区域。 通常情况下，浏览器可以通过查看 URL 来计算正确的区域。 例如，如果 URL 为 http://intranet/，则浏览器将自动发送凭据，因为它会映射到 Intranet 区域中的 URL。 但是，如果 URL 包含句点，例如 http://intranet.contoso.com/，那么计算机不会自动发送凭据，并将该 URL 视为 Internet 站点。

由于在 Azure AD 中用于单一登录的 URL 包含句点，因此需将其显式添加到计算机的 Intranet 区域。 此设置允许浏览器自动将当前已登录用户的凭据以 Kerberos 票证的形式发送至 Azure AD。 若要将所需的 URL 添加到 Intranet 区域，最简单方法是在 Active Directory 中创建组策略。

1.    打开组策略管理工具。
2.    编辑适用于所有用户的组策略，例如“默认域策略”。
3.    导航到“用户配置\管理模板\Windows 组件\Internet Explorer\Internet 控制面板\安全性”页面，并选择“区域分配列表的站点”。
![单一登录](./media/active-directory-aadconnect-sso/sso6.png)  
4.    启用策略，并在对话框中输入以下值/数据。  

        值：https://autologon.microsoftazuread-sso.com  
        Data 1  
        值：https://aadg.windows.net.nsatc.net  
        Data 1  
5.    如下图所示：  
![单一登录](./media/active-directory-aadconnect-sso/sso7.png)

6.    请单击两次“确定”。

用户现在可以进行单一登录了。

>[!NOTE]
>默认情况下，Chrome 使用和 Internet Explorer 相同的一组受信任的站点 URL。 如果为 Chrome 配置了不同设置，则需单独更新这些站点。

## <a name="troubleshooting-single-sign-on-issues"></a>排查单一登录问题
请务必确保客户端正确配置单一登录，其中包括：

1.    在 Intranet 区域内定义了 https://autologon.microsoftazuread-sso.com 和 https://aadg.windows.net.nsatc.net。
2.    确保工作站加入到域。
3.    确保用户使用域帐户登录。
4.    请确保计算机已连接到公司网络。
5.    确保计算机的时间与 Active Directory 同步，并且域控制器时间与正确时间的误差在 5 分钟内。
6.    清除客户端的现有 Kerberos 票证（例如在命令提示符处运行“klist purge”命令）。

如果能够确认上述要求，则可查看浏览器的控制台日志以了解其他信息。 可以在开发人员工具下找到控制台日志。 这些日志有助于确定潜在问题。

## <a name="event-log-entries"></a>事件日志条目
如果启用了成功审核功能，那么用户每次使用单一登录登录时，就会在域控制器的事件日志中记录一个条目。 若要查找这些事件，可以查看与计算机帐户 **AzureADSSOAcc$** 关联的安全性事件 4769 的事件日志。 下面的筛选器查找与此计算机帐户关联的所有安全性事件：

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

