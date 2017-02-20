---
title: "从 Microsoft Forefront 过渡到 Azure AD 应用代理 | Microsoft 文档"
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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>从 Microsoft Forefront 过渡到 Azure AD 应用代理

本文介绍如何从 Microsoft Forefront 的威胁管理网关 (TMG) 和联合访问网关 (UAG) 解决方案转移到以下 Azuare AD 应用程序代理：Web 应用程序代理 (WAP) 和 Azure AD 应用程序代理 (AADAP)。 

> [!NOTE]
> 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。
> 
 
许多客户询问我们该如何从 Forefront UAG 和 TMG 转移到新的应用程序代理。 有关信息，请参阅此[白皮书](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx)，其中详细介绍了这种过渡。 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>TMG/UAG 到 Azure AD 应用程序代理的转换对照表
 
|**TMG/UAG 功能**|**Web 应用程序代理 (WAP)/Azure AD 应用程序代理 (AADAP)**|
|:-----|:-----|
|适用于浏览器应用的选择性 HTTP 发布|已在 Windows Server 2012 R2 中的 WAP 内推出（目前已在 AADAP 中推出）|
|ADFS 集成|已在 Windows Server 2012 R2 中的 WAP 内推出（目前已在 AADAP 中推出）|
|丰富协议发布（例如 Citrix、Lync、RDG）|已在 Windows Server 2012 R2 中的 WAP 内推出（目前已在 AADAP 中推出）|
|ActiveSync 的预身份验证 (HTTP Basic) 和 RDG|将在 Windows Server vNext 中的 WAP 内推出（将在 AADAP 中推出）|
|门户|使用适用于 WAP 的 Intune/System Center（使用适用于 AADAP 的 AAD 访问面板或 Office 365 应用启动器）|
|终结点运行状况检测|使用 Intune/System Center|
|SSL 隧道|使用 Windows SSL/VPN 功能|
|第 2/3 层防火墙|使用 Windows Server 功能|
|Web 应用程序防火墙|Microsoft 未提供最新解决方案|
|安全 Web 网关（正向代理）|Microsoft 未提供最新解决方案|


## <a name="next-steps"></a>后续步骤

[博客：Web 应用程序代理](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[博客：应用程序代理](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)



<!--HONumber=Feb17_HO1-->


