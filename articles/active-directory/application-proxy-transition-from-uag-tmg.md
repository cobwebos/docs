---
title: "从 Microsoft Forefront 过渡到 Azure AD 应用程序代理 | Microsoft 文档"
description: "介绍了有关如何从 Microsoft Forefront TMG 和 UAG 解决方案迁移到 Azure Active Directory 应用程序代理的基础知识。"
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
ms.date: 04/17/2017
ms.author: kgremban
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 6e95e9abac988ae54a401927a92bdb397dd63eed
ms.lasthandoff: 04/21/2017

---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>从 Microsoft Forefront 过渡到 Azure AD 应用程序代理

本文介绍如何从 Microsoft Forefront Threat Management Gateway (TMG) 和 Unified Access Gateway (UAG) 解决方案迁移到 Azure AD 应用程序代理。

若要了解有关从 Forefront TMG 和 UAG 过渡到新的应用程序代理的详细信息，可以[阅读 Microsoft 提供的相关白皮书](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)。

## <a name="functionality-details-for-the-conversion"></a>转换的功能详细信息

|**TMG/UAG 功能**|**Web 应用程序代理/Azure AD 应用程序代理**|
|:-----|:-----|
|适用于浏览器应用的选择性 HTTP 发布|在 Windows Server 2012 R2 中的 Web 应用程序代理中可用。 当前在 Azure AD 应用程序代理中可用。|
|Active Directory 联合身份验证服务 (AD FS) 集成|在 Windows Server 2012 R2 中的 Web 应用程序代理中可用。 当前在 Azure AD 应用程序代理中可用。|
|丰富协议发布（例如 Citrix、Lync、RDG）|在 Windows Server 2012 R2 中的 Web 应用程序代理中可用。 当前在 Azure AD 应用程序代理中可用。|
|ActiveSync 的预身份验证 (HTTP Basic) 和 RDG|当前在 Web 应用程序代理和 Azure AD 应用程序代理中都不可用。|
|门户|将 Intune 或 System Center 用于 Web 应用程序代理。 将 Azure AD 访问面板或 Office 365 应用启动器用于 Azure AD 应用程序代理。|
|终结点运行状况检测|使用 Intune 或 System Center。|
|SSL 隧道|使用 Windows SSL 或 VPN 功能。|
|第 2/3 层防火墙|使用 Windows Server 功能。|
|Web 应用程序防火墙|Microsoft 当前未提供解决方案。|
|安全 Web 网关（正向代理）|Microsoft 当前未提供解决方案。|


## <a name="next-steps"></a>后续步骤

[博客：Web 应用程序代理](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[博客：Azure AD 应用程序代理](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

