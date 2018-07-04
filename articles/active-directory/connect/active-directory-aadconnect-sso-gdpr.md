---
title: 用户隐私和 Azure AD 无缝单一登录 | Microsoft Docs
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
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a4fc779cdfb177a9817049fd7b62b0014e141ce0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "34592402"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>用户隐私和 Azure AD 无缝单一登录

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>概述


Azure AD 无缝 SSO 创建可包含个人数据的以下日志类型： 

- Azure AD Connect 跟踪日志文件。

通过两种方式改善无缝 SSO 的用户隐私：

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
* [查看信任中心上的 Microsoft 隐私策略](https://www.microsoft.com/trustcenter)
- [故障排除](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
