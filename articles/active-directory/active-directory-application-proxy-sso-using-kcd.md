---
title: "使用应用程序代理进行单一登录 | Microsoft 文档"
description: "介绍如何使用 Azure AD 应用程序代理提供单一登录。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c308524e41047220fbad026edb6a87f196d89580
ms.openlocfilehash: 3f293996d2565c495f707f99a0bb75bb7c24054e

---

# <a name="single-sign-on-with-application-proxy"></a>使用应用程序代理进行单一登录
单一登录是 Azure AD 应用程序代理的一个关键要素。 它通过以下步骤提供最佳用户体验：

1. 用户登录到云。  
2. 所有安全验证都在云中完成（预身份验证）。  
3. 将请求发送到本地应用程序后，应用程序代理连接器将模拟用户。 后端应用程序将此用户视为已加入域的设备中的普通用户。

![最终用户通过应用程序代理访问企业网络的示意图](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

借助 Azure AD 应用程序代理可为用户提供单一登录 (SSO) 体验。 请参考以下说明来发布使用 SSO 的应用：

## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>配合使用 KCD 和应用程序代理的本地 IWA 应用的 SSO
可以在 Active Directory 中提供应用程序代理连接器权限来模拟用户并代表用户发送和接收令牌，以使用 Windows 集成身份验证 (IWA) 实现应用程序的单一登录。

### <a name="network-diagram"></a>网络示意图
此示意图解释了用户尝试访问采用 IWA 的本地应用程序时的流程。

![Microsoft AAD 身份验证流程示意图](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. 用户输入 URL，通过应用程序代理访问本地应用程序。
2. 应用程序代理将请求重定向到 Azure AD 身份验证服务，以进行预身份验证。 此时，Azure AD 将应用所有适用的身份验证和授权策略，例如多重身份验证。 如果用户通过验证，Azure AD 将创建令牌并将其发送给用户。
3. 用户将令牌传递给应用程序代理。
4. 应用程序代理验证令牌并从中检索用户主体名称 (UPN)，然后通过双重身份验证安全通道，将请求、UPN 和服务主体名称 (SPN) 发送到连接器。
5. 连接器与本地 AD 执行 Kerberos 约束委托 (KCD) 协商，模拟用户将 Kerberos 令牌发送到应用程序。
6. Active Directory 将应用程序的 Kerberos 令牌发送到连接器。
7. 连接器使用从 AD 收到的 Kerberos 令牌，将原始请求发送到应用程序服务器。
8. 应用程序将响应发送到连接器，该响应随后返回到应用程序代理服务，最后返回到用户。

### <a name="prerequisites"></a>先决条件
配合应用程序代理开始使用 SSO 之前，请确保已在环境中完成以下设置和配置：

* 应用（例如 SharePoint Web 应用）已设置为使用 Windows 集成身份验证。 有关详细信息，请参阅 [Enable Support for Kerberos Authentication](https://technet.microsoft.com/library/dd759186.aspx)（启用对 Kerberos 身份验证的支持）；对于 SharePoint，请参阅 [Plan for Kerberos authentication in SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx)（在 SharePoint 2013 中规划 Kerberos 身份验证）。
* 所有应用都有服务主体名称。
* 运行连接器的服务器以及运行应用的服务器都已加入域且属于同一个域或信任域。 有关加入域的详细信息，请参阅 [Join a Computer to a Domain](https://technet.microsoft.com/library/dd807102.aspx)（将计算机加入域）。
* 运行连接器的服务器有权读取用户的 TokenGroupsGlobalAndUniversal。 这是默认设置，可能受环境强化安全性影响。 请参阅 [KB2009157](https://support.microsoft.com/en-us/kb/2009157) 获取有关此项设置的更多帮助。

### <a name="active-directory-configuration"></a>Active Directory 配置
根据应用程序代理连接器和已发布的服务器是否位于同一域，Active Directory 配置有所不同。

#### <a name="connector-and-published-server-in-the-same-domain"></a>连接器和已发布的服务器位于同一域
1. 在 Active Directory 中，转到“工具” > “用户和计算机”。
2. 选择运行连接器的服务器。
.3. 单击右键，然后选择“属性” > “委托”。
4. 选择“仅信任此计算机来委派指定的服务”。 在“可以由此帐户提供委托凭据的服务”下面，添加应用程序服务器的 SPN 标识值。
5. 这样，应用程序代理连接器便可以针对列表中定义的应用程序在 AD 中模拟用户。

![“连接器 SVR 属性”窗口屏幕截图](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>连接器和已发布的服务器位于不同的域
1. 有关跨域使用 KCD 的先决条件列表，请参阅 [Kerberos Constrained Delegation across domains](https://technet.microsoft.com/library/hh831477.aspx)（跨域 Kerberos 约束委托）。
2. 在 Windows 2012 R2 中，使用连接器服务器的 `principalsallowedtodelegateto` 属性可让应用程序代理委托连接器服务器，其中已发布的服务器为 `sharepointserviceaccount`，委托服务器为 `connectormachineaccount`。

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

> [!NOTE]
> `sharepointserviceaccount` 可以是 SPS 计算机帐户，或者是用于运行 SPS 应用池的服务帐户。
>
>

### <a name="azure-classic-portal-configuration"></a>Azure 经典门户配置
1. 根据[使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)中的说明发布应用程序。 请务必选择“Azure Active Directory”作为“预身份验证方法”。
2. 应用程序显示在应用程序列表中之后，选择该应用程序并单击“配置”。
3. 在“属性”下面，将“内部身份验证方法”设置为“Windows 集成身份验证”。  
   ![高级应用程序配置](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. 输入应用程序服务器的**内部应用程序 SPN**。 在本示例中，已发布应用程序的 SPN 为 http/lob.contoso.com。  

> [!IMPORTANT]
> 如果本地 UPN 和 Azure Active Directory 中的 UPN 不同，则需要设置[委托的登录标识](#delegated-login-identity)才能正常进行预身份验证。
>
>

|  |  |
| --- | --- |
| 内部身份验证方法 |如果使用 Azure AD 进行预身份验证，可以设置内部身份验证方法，以便用户能够在此应用程序中单一登录 (SSO)。 <br><br> 如果应用程序使用 IWA 并且你可以将 Kerberos 约束委托 (KCD) 配置为针对此应用程序启用 SSO，请选择“Windows 集成身份验证(IWA)”。 必须通过 KCD 配置使用 IWA 的应用程序，否则，应用程序代理无法发布这些应用程序。 <br><br> 如果应用程序不使用 IWA，请选择“无”。 |
| 内部应用程序 SPN |这是在本地 Azure AD 中配置的内部应用程序服务主体名称 (SPN)。 应用程序代理连接器使用 SPN 提取使用 KCD 的应用程序的 Kerberos 令牌。 |

## <a name="sso-for-non-windows-apps"></a>非 Windows 应用的 SSO
Azure AD 应用程序代理的 Kerberos 委托流程从 Azure AD 在云中验证用户身份开始。 请求到达本地后，Azure AD 应用程序代理连接器将通过与本地 Active Directory 交互，代表用户颁发 Kerberos 票证。 此过程称为 Kerberos 约束委托 (KCD)。 在下一个阶段，请求将发送到具有此 Kerberos 票证的后端应用程序。 有许多协议定义了如何发送此类请求。 大多数非 Windows 服务器预期 Azure AD 应用程序代理支持协商/SPNego。

![非 Windows SSO 示意图](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

有关 Kerberos 的详细信息，请参阅[全面了解 Kerberos 约束委派 (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)。

### <a name="delegated-login-identity"></a>委托的登录标识
委托的登录标识可帮助处理两种不同的登录方案：

* 通常以用户名或 SAM 帐户名形式（而不是电子邮件地址 (username@domain)）获取用户标识的非 Windows 应用程序。
* 备用登录配置，其中 Azure AD 中的 UPN 与本地 Active Directory 中的 UPN 不同。

使用应用程序代理可以选择要用于获取 Kerberos 票证的标识。 此设置特定于应用程序。 其中一些选项适用于不接受电子邮件地址格式的系统，另一些选项适用于替代登录。

![委托的登录标识参数屏幕截图](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

如果使用委托的登录标识，则组织中所有域或林的此值可能不唯一。 可以通过使用两个不同的连接器组两次发布这些应用程序来避免此问题。 由于每个应用程序有不同的用户对象，因此可将其连接器加入不同的域。

## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>在本地标识和云标识不相同的情况下使用 SSO
除非另行配置，否则应用程序代理假设用户在云中与本地具有完全相同的标识。 可以针对每个应用程序配置执行单一登录时要使用的标识。  

此功能可让许多具有不同本地标识与云标识的组织从云单一登录到本地应用程序，而不需要用户输入不同的用户名与密码。 这包括如下所述的组织：

* 在内部有多个域（(joe@us.contoso.com,、joe@eu.contoso.com)），在云中有单个域 ((joe@contoso.com))。
* 在内部有不可路由的域名 ((joe@contoso.usa))，在云中有合法域名。
* 不使用内部域名 (joe)
* 在本地和云中使用不同的别名。 例如 joe-johns@contoso.com 与 joej@contoso.com  

它还有助于不接受电子邮件地址形式的应用程序，这种情况对于非 Windows 后端服务器很常见。

### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>为不同的云标识和本地标识设置 SSO
1. 配置 Azure AD Connect 设置，使主标识采用电子邮件地址（邮件）。 可以在自定义过程中通过更改同步设置中的“用户主体名称”字段来实现此目的。 这些设置同时确定了用户如何登录到 Office&365;、Windows&10; 设备以及其他使用 Azure AD 作为标识存储的应用程序。  
   ![识别用户屏幕截图 -“用户主体名称”下拉列表](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 在要修改的应用程序的“应用程序配置”设置中，选择要使用的**委托的登录识别**：

   * 用户主体名称：joe@contoso.com  
   * 备用用户主体名称：joed@contoso.local  
   * 用户主体名称的用户名部分：joe  
   * 备用用户主体名称的用户名部分：joed  
   * 本地 SAM 帐户名：取决于本地域控制器配置

   ![“委托的登录标识”下拉菜单屏幕截图](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>排查不同标识的 SSO 问题
如果在 SSO 过程中发生错误，连接器计算机事件日志中会显示该错误，如[故障排除](active-directory-application-proxy-troubleshoot.md)中所述。
但在某些情况下，请求将成功发送到后端应用程序，同时此应用程序将以其他各种 HTTP 响应做出回复。 排查这些问题时，应该先检查连接器计算机上应用程序代理会话事件日志中的事件编号 24029。 用于委托的用户标识出现在事件详细信息的“user”字段中。 若要打开会话记录，请在事件查看器视图菜单中选择“显示分析和调试日志”。

## <a name="see-also"></a>另请参阅
* [使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)
* [解决使用应用程序代理时遇到的问题](active-directory-application-proxy-troubleshoot.md)
* [使用声明感知应用程序](active-directory-application-proxy-claims-aware-apps.md)
* [启用条件性访问](active-directory-application-proxy-conditional-access.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg



<!--HONumber=Feb17_HO2-->


