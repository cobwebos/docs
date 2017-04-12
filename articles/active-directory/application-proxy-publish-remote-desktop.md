---
title: "使用 Azure Active Directory 应用程序代理发布远程桌面 | Microsoft 文档"
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
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fd0ecc62fd3cfc860423acd02108648e99f44753
ms.lasthandoff: 04/03/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理发布远程桌面

本文介绍了如何使 Windows 远程桌面部署可供远程用户访问。 远程桌面部署可以驻留在本地，也可以驻留在 IaaS 部署等专用网络中。

> [!NOTE]
> 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory (Azure AD) 后才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。

可以通过用作直通代理应用程序的 Azure AD 应用程序代理发布远程桌面协议 (RDP) 流量。 此解决方案可以解决连接问题并提供基本的安全保护，例如网络缓冲、强化的 Internet 前端和分布式拒绝服务 (DDoS) 保护。

## <a name="remote-desktop-deployment"></a>远程桌面部署

在远程桌面部署中发布了远程桌面网关，以便能够将基于 HTTPS 的远程过程调用 (RPC) 流量转换为基于用户数据报协议 (UDP) 的 RDP 流量。

可将客户端配置为使用远程桌面客户端（如 MSTSC.exe）来访问 Azure AD 应用程序代理。 这样，便可以使用远程桌面网关的连接器来与它建立新的 HTTPS 连接。 如果采用这种做法，网关没有直接向 Internet 公开，所有 HTTPS 请求将首先在云中终止。

下图中显示了拓扑：

 ![Azure AD 服务本地示意图](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>配置远程桌面网关 URL

如果用户配置了远程桌面网关 URL 并触发了 RDP 流量（通常会触发这种流量），他们可以访问文件和其他方法。

可以使用应用代理提供的域名 (msappproxy.net) 或者使用 Azure AD 上配置的自定义域名（例如 rdg.contoso.com）来进行发布。

如果已使用远程桌面网关 URL 配置了客户端设备和 RDP 文件，可以选择使用相同的域名，从而避免更改。 在这种情况下，应将涵盖此域的证书提供给应用程序代理，并确保可以通过 Internet 访问此证书的证书吊销列表 (CRL)。

如果未配置远程桌面网关 URL，用户或管理员可以使用“远程桌面连接”对话框在远程桌面客户端 (MSTSC) 中指定该 URL，如此处所示。

 ![远程桌面连接对话框](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

在“高级”选项卡中单击“设置”时，会出现“连接设置”对话框。

 ![“远程桌面连接”对话框中的“连接设置”窗口](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>远程桌面 Web 访问

如果你的组织使用远程桌面 Web 访问 (RDWA) 门户，则还可以使用 Azure AD 应用程序代理进行发布。 可以使用预身份验证和单一登录 (SSO) 发布到此门户。

下图中显示了 RDWA 方案的拓扑：

 ![RDWA 方案的示意图](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

在上面的案例中，用户在访问 RDWA 之前在 Azure AD 中执行身份验证。 如果他们已在 Azur AD 中完成身份验证（例如，使用了 Office 365 时），则无需为 RDWA 再次执行身份验证。

当用户启动 RDP 会话时，需要通过 RDP 通道再次执行身份验证。 之所以发生这种情况，是因为从 RDWA 到远程桌面网关的 SSO 依赖于使用 ActiveX 在客户端上存储用户凭据。 此过程是通过 RDWA 的基于表单的身份验证触发的。 如果 RDWA 身份验证使用 Kerbros，则不提供基于表单的身份验证，因此 RDWA 到 RDP 的 SSO 不起作用。

如果 RDWA 需要针对 RDP 流量执行 SSO，或者 RDWA 的基于表单的身份验证经过复杂的自定义，则可以在不进行预身份验证的情况下发布 RDWA。

下图中显示了此方案的拓扑：

 ![RDWA 方案的示意图](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

在上面的案例中，用户必须使用基于表单的身份验证向 RDWA 证明身份，但不需要通过 RDP 执行身份验证。

>[!NOTE]
>在上面的两种情况下，对于 RDP 流量，都不需要进行预身份验证。 因此，用户无需首先通过 RDWA 即可访问该流量。

## <a name="next-steps"></a>后续步骤

[使用 Azure AD 应用程序代理启用对 SharePoint 的远程访问](application-proxy-enable-remote-access-sharepoint.md)  
[在 Azure 门户中启用应用程序代理](active-directory-application-proxy-enable.md)

