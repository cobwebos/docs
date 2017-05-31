---
title: "升级到 Azure AD 应用程序代理 | Microsoft Docs"
description: "如果要从 Microsoft Forefront 或 Unified Access Gateway 升级，请选择哪个代理解决方案最佳。"
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
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: zh-cn
ms.lasthandoff: 05/13/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>从 Microsoft Forefront 或 Unified Access Gateway 升级到 Azure AD 代理

本文介绍如何从 Microsoft Forefront Threat Management Gateway (TMG) 和 Unified Access Gateway (UAG) 解决方案迁移到 Azure AD 应用程序代理。

若要了解有关从 Forefront TMG 和 UAG 过渡到新的应用程序代理的详细信息，可以[阅读 Microsoft 提供的相关白皮书](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)。

## <a name="functionality-details-for-the-conversion"></a>转换的功能详细信息

|**TMG/UAG 功能**|**现代解决方案**|
|:-----|:-----|
|适用于浏览器应用的选择性 HTTP 发布|Azure AD 应用程序代理|
|Active Directory 联合身份验证服务 (AD FS) 集成|Azure AD 应用程序代理|
|丰富协议发布（例如 Citrix、Lync、RDG）|Azure AD 应用程序代理|
|门户|用于 Azure AD 应用程序代理的 Azure AD 访问面板或 Office 365 应用启动器|
|终结点运行状况检测|Intune 或 System Center|
|SSL 隧道|Windows SSL 或 VPN|
|第 2/3 层防火墙|Windows Server|
|ActiveSync 的预身份验证 (HTTP Basic) 和 RDG|Microsoft 未提供最新解决方案|
|Web 应用程序防火墙|Microsoft 当前未提供解决方案。|
|安全 Web 网关（正向代理）|Microsoft 当前未提供解决方案。|


## <a name="next-steps"></a>后续步骤

[博客：Web 应用程序代理](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[博客：Azure AD 应用程序代理](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

