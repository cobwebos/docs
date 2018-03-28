---
title: Azure AD Connect：无缝单一登录 - GDPR 符合性 | Microsoft Docs
description: 本文讨论 Azure Active Directory (Azure AD) 无缝 SSO 和 GDPR 符合性。
services: active-directory
keywords: 什么是 Azure AD Connect, GDPR, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Azure AD 无缝单一登录：GDPR 符合性

## <a name="overview"></a>概述

欧洲隐私法律——[一般数据保护条例 (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm)将于 2018 年 5 月生效。 GDPR 针对向欧盟 (EU) 居民提供产品及服务，或收集和分析欧盟居民相关信息的公司、政府机构、非营利组织和其他组织制定了新的规则。 无论你身在何处，均需遵守 GDPR。 

Microsoft 产品和服务可立即用于帮助你满足 GDPR 要求。 请访问[信任中心](https://www.microsoft.com/trustcenter)，深入了解 Microsoft 隐私策略。

Azure AD 无缝 SSO 创建可包含 EUII 的以下日志类型：

- Azure AD Connect 跟踪日志文件。

无缝 SSO 可以通过两种方式达到 GDPR 符合性：

1.  根据请求提取用户的数据以及从安装项中删除该用户的数据。
2.  确保数据保留时长均不超过 48 小时。

我们强烈建议选择第二个选项，因为它更易于实现和维护。 请参阅以下有关每个日志类型的说明：

### <a name="delete-azure-ad-connect-trace-log-files"></a>删除 Azure AD Connect 跟踪日志文件

在安装或升级 Azure AD Connect 或修改无缝 SSO 配置后的 48 小时内，请检查 **%ProgramData%\AADConnect** 文件夹的内容并删除该文件夹的跟踪日志内容（**trace-\*.log** 文件），因为此操作可能会产生 GDPR 所涵盖的数据。

>[!IMPORTANT]
>请勿删除该文件夹中的 **PersistedState.xml** 文件，因为此文件用于维持 Azure AD Connect 的以前安装状态，并在完成升级安装时使用。 此文件将永远不会包含有关人员的任何数据，因此永远不应删除。

可使用 Windows 资源管理器查看和删除这些跟踪日志文件，也可使用以下 PowerShell 脚本执行必要的操作：

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

将脚本保存在具有“.PS1”扩展名的文件中。 根据需要运行此脚本。

若要详细了解相关 Azure AD Connect GDPR 要求，请参阅[此文](active-directory-aadconnect-gdpr.md)。

### <a name="note-about-domain-controller-logs"></a>关于域控制器日志的注意事项

如果启用了审核日志，此产品可能会为域控制器生成安全日志。 若要了解有关配置审核策略的详细信息，请阅读[此文](https://technet.microsoft.com/library/dd277403.aspx)。

## <a name="next-steps"></a>后续步骤

- [故障排除](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
