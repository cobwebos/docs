---
title: Azure AD 应用程序代理常见问题 |Microsoft Docs
description: 了解有关使用 Azure AD 应用程序代理将内部本地应用程序发布到远程用户的常见问题解答（FAQ）的解答。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: a6efe74008b2271b960f877f5f0f6b2b6b549a8d
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583086"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory （Azure AD）应用程序代理常见问题

本页回答了有关 Azure Active Directory （Azure AD）应用程序代理的常见问题。

## <a name="enabling-azure-ad-application-proxy"></a>启用 Azure AD 应用程序代理

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理需要哪些许可证？

若要使用 Azure AD 应用程序代理，必须具有 Azure AD Premium P1 或 P2 许可证。 有关授权的详细信息，请参阅[Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>为什么 "启用应用程序代理" 按钮灰显？

请确保至少安装了 Azure AD Premium P1 或 P2 许可证，并安装了 Azure AD 应用程序代理连接器。 成功安装第一个连接器后，将自动启用 Azure AD 应用程序代理服务。

## <a name="connector-configuration"></a>连接器配置

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>应用程序代理连接器服务是否可以在默认的不同用户上下文中运行？

不，不支持此方案。 默认设置为：

- Microsoft AAD 应用程序代理连接器-WAPCSvc-网络服务
- Microsoft AAD 应用程序代理连接器更新服务 WAPCUpdaterSvc-NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>我的后端应用程序托管在多个 web 服务器上，并且需要用户会话暂留（粘性）。 如何实现会话暂留？ 

有关建议，请参阅[应用程序代理连接器和应用程序的高可用性和负载平衡](application-proxy-high-availability-load-balancing.md)。

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>是否支持从连接器服务器到 Azure 的流量进行 TLS 终止（TLS/HTTPS 检查或加速）？

应用程序代理连接器对 Azure 执行基于证书的身份验证。 TLS 终止（TLS/HTTPS 检查或加速）会中断此身份验证方法，并且不受支持。 从连接器到 Azure 的流量必须绕过任何正在执行 TLS 终止的设备。  

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>是否可以在连接器服务器和后端应用程序服务器之间放置转发代理设备？
是的，从连接器版本1.5.1526.0 开始支持此方案。 请参阅[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>我是否应该创建专用帐户来向 Azure AD 应用程序代理注册连接器？

没有理由。 任何全局管理员或应用程序管理员帐户都将起作用。 注册过程后不使用在安装过程中输入的凭据。 相反，证书将颁发给连接器，后者用于从该点进行的身份验证。

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>如何监视 Azure AD 应用程序代理连接器的性能？

与连接器一起安装了性能监视器计数器。 查看管理共享：  

1. 选择 "**开始**"，键入 "Perfmon"，然后按 enter。
2. 选择 "**性能监视器**"，然后**+** 单击绿色图标。
3. 添加要监视的**MICROSOFT AAD 应用程序代理连接器**计数器。

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD 应用程序代理连接器是否必须与资源位于同一子网？

连接器不需要位于同一子网中。 但是，它需要对资源进行名称解析（DNS、主机文件）和必要的网络连接（路由到资源、资源上打开的端口等）。 有关建议，请参阅[使用 Azure Active Directory 应用程序代理时的网络拓扑注意事项](application-proxy-network-topology.md)。

## <a name="application-configuration"></a>应用程序配置

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>默认值和 "长" 后端超时的长度是多少？ 是否可以扩展超时？

默认长度为85秒。 "Long" 设置为180秒。 无法扩展超时限制。

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>如何实现更改应用程序加载的登录页？

在应用程序注册页中，可以将主页 URL 更改为登录页的所需外部 URL。 从 "我的应用" 或 Office 365 门户启动应用程序时，将加载指定的页面。 有关配置步骤，请参阅[使用 Azure AD 应用程序代理为发布的应用设置自定义主页](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>只能发布基于 IIS 的应用程序吗？ 什么是在非 Windows web 服务器上运行的 web 应用程序？ 连接器是否必须安装在安装了 IIS 的服务器上？

不是，发布的应用程序没有 IIS 要求。 你可以发布在 Windows Server 之外的服务器上运行的 web 应用程序。 但是，你可能无法对非 Windows Server 使用预身份验证，具体取决于 web 服务器是否支持协商（Kerberos 身份验证）。 安装连接器的服务器上不需要 IIS。

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>设置 Kerberos 约束委派（KCD）时，应何时使用 PrincipalsAllowedToDelegateToAccount 方法？

如果连接器服务器与 web 应用程序服务帐户在不同的域中，则使用 PrincipalsAllowedToDelegateToAccount 方法。 它需要使用基于资源的约束委派。
如果连接器服务器和 web 应用程序服务帐户位于同一域中，则可以使用 Active Directory 用户和计算机来配置每个连接器计算机帐户上的委派设置，从而允许它们委托给目标 SPN。

如果连接器服务器和 web 应用程序服务帐户在不同的域中，则使用基于资源的委派。 委托权限在目标 web 服务器和 web 应用程序服务帐户上进行配置。 此约束委派方法相对全新。 此方法是在 Windows Server 2012 中引入的，该方法支持跨域委托，这允许资源（web 服务）所有者控制哪些计算机和服务帐户可以委派给它。 没有可帮助此配置的 UI，因此你需要使用 PowerShell。
有关详细信息，请参阅白皮书[了解具有应用程序代理的 Kerberos 约束委派](https://aka.ms/kcdpaper)。

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>NTLM 身份验证是否适用于 Azure AD 应用程序代理？

NTLM 身份验证不能用作预身份验证或单一登录方法。 仅当可以直接在客户端和已发布的 web 应用程序之间协商 NTLM 身份验证时，才能使用 NTLM 身份验证。 使用 NTLM 身份验证通常会在浏览器中显示登录提示。

## <a name="pass-through-authentication"></a>直通身份验证

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>能否对使用传递身份验证发布的应用程序使用条件性访问策略？

条件性访问策略只会在 Azure AD 中成功进行预先身份验证的用户。 传递身份验证不会触发 Azure AD 身份验证，因此不能强制实施条件访问策略。 使用直通身份验证时，必须在本地服务器上（如果可能），或者通过使用 Azure AD 应用程序代理启用预身份验证来实现 MFA 策略。

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>是否可以使用客户端证书身份验证要求发布 web 应用程序？

不会，不支持此方案，因为应用程序代理将终止 TLS 流量。  

## <a name="remote-desktop-gateway-publishing"></a>远程桌面网关发布

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>如何 Azure AD 应用程序代理发布远程桌面网关？

请参阅将[远程桌面与 Azure AD 应用程序代理一起发布](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>能否在远程桌面网关发布方案中使用 Kerberos 约束委派（单一登录-Windows 集成身份验证）？

不，不支持此方案。  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>我的用户不使用 Internet Explorer 11，预身份验证方案不适用于他们。 这是正常情况吗？

是的，应为 "是"。 预身份验证方案要求在第三方浏览器中不支持 ActiveX 控件。

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>是否支持远程桌面 Web 客户端（HTML5）？

不能，目前不支持此方案。 请访问我们的[UserVoice](https://aka.ms/aadapuservoice)反馈论坛，了解有关此功能的更新。

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>配置预身份验证方案后，我意识到用户必须进行两次身份验证：第一次是 Azure AD 登录表单，然后是 RDWeb 登录窗体。 这是正常情况吗？ 如何将其减少到一个登录？

是的，应为 "是"。 如果用户的计算机 Azure AD 加入，则用户将登录到自动 Azure AD。 用户只需在 RDWeb 登录窗体上提供凭据。

## <a name="sharepoint-publishing"></a>SharePoint 发布

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>如何将 SharePoint 发布 Azure AD 应用程序代理？

请参阅[使用 Azure AD 应用程序代理启用对 SharePoint 的远程访问](application-proxy-integrate-with-sharepoint-server.md)。

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>能否使用 SharePoint 移动应用（iOS/Android）访问已发布的 SharePoint 服务器？

[SharePoint 移动应用程序](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises)当前不支持 Azure Active Directory 预身份验证。

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory 联合身份验证服务（AD FS）发布 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>能否使用 Azure AD 应用程序代理作为 AD FS 代理（如 Web 应用程序代理）？

否。 Azure AD 应用程序代理用于处理 Azure AD，并且不满足充当 AD FS 代理的要求。

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket 是否支持 QlikSense 以外的应用程序？

目前，WebSocket 协议支持仍以公共预览版提供，可能不适用于其他应用程序。 一些客户使用 WebSocket 协议与其他应用程序的混合成功。 如果测试这种情况，我们很乐意听到您的结果。 请向我们发送你的aadapfeedback@microsoft.com反馈。

目前，Windows 管理中心（WAC）或远程桌面 Web 客户端（HTML5）中的功能（事件日志、PowerShell 和远程桌面服务）不适用于 Azure AD 应用程序代理。

## <a name="link-translation"></a>链接转换

### <a name="does-using-link-translation-affect-performance"></a>使用链接转换是否会影响性能？

是的。 链接转换会影响性能。 应用程序代理服务会扫描应用程序以查找硬编码的链接，并将其替换为其相应的已发布外部 Url，然后将其呈现给用户。 

为了获得最佳性能，我们建议通过配置[自定义域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)来使用相同的内部和外部 url。 如果无法使用自定义域，则可以使用移动设备上的 "我的应用安全登录扩展" 或 "Microsoft Edge 浏览器" 改进链接转换性能。 [有关 Azure AD 应用程序代理发布的应用，请参阅重定向硬编码的链接](application-proxy-configure-hard-coded-link-translation.md)。

## <a name="wildcards"></a>通配符

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>如何实现使用通配符发布两个具有相同自定义域名的应用程序，但使用不同的协议，一个用于 HTTP，另一个用于 HTTPS？

不直接支持此方案。 此方案的选项包括：

1. 使用通配符将 HTTP 和 HTTPS Url 发布为单独的应用程序，但为每个 Url 提供不同的自定义域。 此配置将起作用，因为它们具有不同的外部 URL。

2. 通过通配符应用程序发布 HTTPS URL。 使用以下应用程序代理 PowerShell cmdlet 单独发布 HTTP 应用程序：
   - [应用程序代理应用程序管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [应用程序代理连接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
