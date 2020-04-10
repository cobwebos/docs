---
title: Azure AD 应用程序代理常见问题 |微软文档
description: 了解有关使用 Azure AD 应用程序代理向远程用户发布内部本地应用程序的常见问题 （FAQ） 的答案。
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
ms.openlocfilehash: d1929f937d86001a0f2a399b1ebd92e47bbd2c86
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990899"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>活动目录 （Azure AD） 应用程序代理常见问题

此页回答了有关 Azure 活动目录 （Azure AD） 应用程序代理的常见问题。

## <a name="enabling-azure-ad-application-proxy"></a>启用 Azure AD 应用程序代理

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理需要哪些许可证？

要使用 Azure AD 应用程序代理，必须具有 Azure AD 高级 P1 或 P2 许可证。 有关许可的详细信息，请参阅[Azure 活动目录定价](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>为什么"启用应用程序代理"按钮灰显？

确保至少安装了 Azure AD 高级 P1 或 P2 许可证，并安装了 Azure AD 应用程序代理连接器。 成功安装第一个连接器后，将自动启用 Azure AD 应用程序代理服务。

## <a name="connector-configuration"></a>连接器配置

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>应用程序代理连接器服务是否可以在不同于默认值的用户上下文中运行？

不，不支持此方案。 默认设置为：

- 微软AAD应用程序代理连接器 - WAPCSvc - 网络服务
- 微软AAD应用程序代理连接器更新程序 - WAPC更新器Svc - NT权威_系统

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>我的后端应用程序托管在多个 Web 服务器上，需要用户会话持久性（粘性）。 如何实现会话持久性？ 

有关建议，请参阅[应用程序代理连接器和应用程序的高可用性和负载平衡](application-proxy-high-availability-load-balancing.md)。

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>是否支持从连接器服务器到 Azure 的流量上的 TLS 终止（TLS/HTTPS 检查或加速）？

应用程序代理连接器对 Azure 执行基于证书的身份验证。 TLS 终止（TLS/HTTPS 检查或加速）会破坏此身份验证方法，不受支持。 从连接器到 Azure 的流量必须绕过执行 TLS 终止的任何设备。  

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>是否可以在连接器服务器和后端应用程序服务器之间放置转发代理设备？
是的，支持从连接器版本 1.5.1526.0 开始此方案。 请参阅[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>我应该创建专用帐户以将连接器注册到 Azure AD 应用程序代理？

没有理由. 任何全局管理员或应用程序管理员帐户都将工作。 安装过程中输入的凭据在注册过程后不使用。 相反，证书颁发给连接器，该连接器从该点开始用于身份验证。

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>如何监视 Azure AD 应用程序代理连接器的性能？

还有与连接器一起安装的性能监视器计数器。 查看管理共享：  

1. 选择 **"开始**"，键入"Perfmon"，然后按 ENTER。
2. 选择 **"性能监视器**"并单击**+** 绿色图标。
3. 添加要监视的**Microsoft AAD 应用程序代理连接器**计数器。

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD 应用程序代理连接器是否必须与资源位于同一子网上？

连接器不需要位于同一子网上。 但是，它需要对资源的名称解析（DNS、主机文件）和必要的网络连接（路由到资源、在资源上打开的端口等）。 有关建议，请参阅[使用 Azure 活动目录应用程序代理时的网络拓扑注意事项](application-proxy-network-topology.md)。

## <a name="application-configuration"></a>应用程序配置

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>默认超时和"长"后端超时的长度是多少？ 超时可以延长吗？

默认长度为 85 秒。 "长"设置为 180 秒。 无法延长超时限制。

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>如何更改应用程序加载的着陆页？

在"应用程序注册"页中，您可以将主页 URL 更改为着陆页所需的外部 URL。 当从"我的应用"或 Office 365 门户启动应用程序时，将加载指定的页面。 有关配置步骤，请参阅[使用 Azure AD 应用程序代理为已发布应用设置自定义主页](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>只能发布基于 IIS 的应用程序吗？ 在非 Windows Web 服务器上运行的 Web 应用程序如何？ 连接器是否必须安装在安装了 IIS 的服务器上？

否，对于发布的应用程序没有 IIS 要求。 您可以发布在 Windows 服务器以外的服务器上运行的 Web 应用程序。 但是，您可能无法使用非 Windows 服务器的预身份验证，具体取决于 Web 服务器是否支持协商（Kerberos 身份验证）。 安装连接器的服务器上不需要 IIS。

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>在设置 Kerberos 约束委派 （KCD） 时，我应该何时使用"委托委派给帐户"方法？

当连接器服务器与 Web 应用程序服务帐户位于不同的域中时，将使用委托委托委托到帐户方法。 它要求使用基于资源的约束委派。
如果连接器服务器和 Web 应用程序服务帐户位于同一域中，则可以使用 Active Directory 用户和计算机在每个连接器计算机帐户上配置委派设置，从而允许它们委派给目标 SPN。

如果连接器服务器和 Web 应用程序服务帐户位于不同的域中，则使用基于资源的委派。 委派权限在目标 Web 服务器和 Web 应用程序服务帐户上配置。 这种约束委派的方法相对较新。 该方法在 Windows Server 2012 中引入，它允许资源（Web 服务）所有者控制哪些计算机和服务帐户可以委托给它，从而支持跨域委派。 没有 UI 可帮助处理此配置，因此您需要使用 PowerShell。
有关详细信息，请参阅使用[应用程序代理了解 Kerberos 约束委派](https://aka.ms/kcdpaper)的白皮书。

## <a name="pass-through-authentication"></a>直通身份验证

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>对于通过直通身份验证发布的应用程序，是否可以使用条件访问策略？

条件访问策略仅适用于 Azure AD 中成功预身份验证的用户。 直通身份验证不会触发 Azure AD 身份验证，因此无法强制执行条件访问策略。 使用直通身份验证，必须在本地服务器上实现 MFA 策略（如果可能）或通过使用 Azure AD 应用程序代理启用预身份验证。

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>是否可以发布具有客户端证书身份验证要求的 Web 应用程序？

不，此方案不受支持，因为应用程序代理将终止 TLS 流量。  

## <a name="remote-desktop-gateway-publishing"></a>远程桌面网关发布

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>如何通过 Azure AD 应用程序代理发布远程桌面网关？

请参阅[使用 Azure AD 应用程序代理发布远程桌面](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>我可以在远程桌面网关发布方案中使用 Kerberos 受限委派（单登录 - Windows 集成身份验证）吗？

不，不支持此方案。  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>我的用户不使用 Internet Explorer 11，预身份验证方案不适合他们。 这是正常情况吗？

是的，这是预料之中的。 预身份验证方案需要 ActiveX 控件，第三方浏览器不支持该控件。

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>是否支持远程桌面 Web 客户端 （HTML5）？

否，此方案当前不受支持。 请关注我们的[用户语音](https://aka.ms/aadapuservoice)反馈论坛，了解此功能的更新。

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>配置预身份验证方案后，我意识到用户必须进行两次身份验证：首先在 Azure AD 登录窗体上，然后在 RDWeb 登录窗体上进行身份验证。 这是正常情况吗？ 如何将此减少到一个登录？

是的，这是预料之中的。 如果用户的计算机已加入 Azure AD，则用户将自动登录到 Azure AD。 用户只需仅在 RDWeb 登录窗体上提供其凭据。

## <a name="sharepoint-publishing"></a>共享点发布

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>如何通过 Azure AD 应用程序代理发布共享点？

请参阅[使用 Azure AD 应用程序代理启用对 SharePoint 的远程访问](application-proxy-integrate-with-sharepoint-server.md)。

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>我能否使用 SharePoint 移动应用 （iOS/ Android） 访问已发布的 SharePoint 服务器？

[SharePoint 移动应用](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises)当前不支持 Azure 活动目录预身份验证。

## <a name="active-directory-federation-services-ad-fs-publishing"></a>活动目录联合服务 （AD FS） 发布 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>是否可以使用 Azure AD 应用程序代理作为 AD FS 代理（如 Web 应用程序代理）？

不是。 Azure AD 应用程序代理旨在与 Azure AD 配合使用，并且不符合作为 AD FS 代理的要求。

## <a name="websocket"></a>Websocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket 支持是否适用于 QlikSense 以外的应用程序？

目前，WebSocket 协议支持仍处于公共预览状态，它可能不适用于其他应用程序。 一些客户使用 WebSocket 协议与其他应用程序取得了喜忧参半的成功。 如果您测试此类方案，我们很乐意听到您的结果。 请在 上aadapfeedback@microsoft.com向我们发送您的反馈。

Windows 管理中心 （WAC） 或远程桌面 Web 客户端 （HTML5） 中的功能（事件日志、PowerShell 和远程桌面服务）目前无法通过 Azure AD 应用程序代理工作。

## <a name="link-translation"></a>链接翻译

### <a name="does-using-link-translation-affect-performance"></a>使用链接转换会影响性能吗？

是的。 链接转换会影响性能。 应用程序代理服务扫描应用程序以寻找硬编码链接，并将其替换为各自的已发布的外部 URL，然后再将其呈现给用户。 

为了获得最佳性能，我们建议通过配置[自定义域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)使用相同的内部和外部 URL。 如果无法使用自定义域，则可以在移动设备上使用"我的应用安全登录扩展"或"Microsoft 边缘浏览器"来提高链接翻译性能。 有关[使用 Azure AD 应用程序代理发布的应用，请参阅重定向硬编码链接](application-proxy-configure-hard-coded-link-translation.md)。

## <a name="wildcards"></a>通配符

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>如何使用通配符发布两个具有相同自定义域名但具有不同协议的应用程序，一个用于 HTTP，一个用于 HTTPS？

此方案不受直接支持。 此方案的选项包括：

1. 将 HTTP 和 HTTPS URL 发布为具有通配符的单独应用程序，但为每个 URL 提供不同的自定义域。 此配置将工作，因为它们具有不同的外部 URLS。

2. 通过通配符应用程序发布 HTTPS URL。 使用这些应用程序代理 PowerShell cmdlet 分别发布 HTTP 应用程序：
   - [应用程序代理应用程序管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [应用程序代理连接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
