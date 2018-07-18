---
title: 用户隐私和 Azure Active Directory 直通身份验证 | Microsoft Docs
description: 本文介绍如何处理 Azure Active Directory (Azure AD) 直通身份验证和 GDPR 符合性。
services: active-directory
keywords: Azure AD Connect 直通身份验证, GDPR, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
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
ms.custom: seohack1
ms.openlocfilehash: f53f8ffcf8354d35fa552f099302456fa5226ca8
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915871"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>用户隐私和 Azure Active Directory 直通身份验证


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>概述

Azure AD 直通身份验证创建可以包含个人数据的以下日志类型：

- Azure AD Connect 跟踪日志文件。
- 身份验证代理跟踪日志文件。
- Windows 事件日志文件。

可通过两种方式改进直通身份验证的用户隐私：

1.  根据请求提取用户的数据以及从安装项中删除该用户的数据。
2.  确保数据保留时长均不超过 48 小时。

我们强烈建议选择第二个选项，因为它更易于实现和维护。 下面是有关每个日志类型的说明：

### <a name="delete-azure-ad-connect-trace-log-files"></a>删除 Azure AD Connect 跟踪日志文件

在安装或升级 Azure AD Connect 或修改直通身份验证配置后的 48 小时内，请检查 **%ProgramData%\AADConnect** 文件夹的内容并删除该文件夹的跟踪日志内容（**trace-\*.log** 文件），因为此操作可能会产生 GDPR 所涵盖的数据。

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

### <a name="delete-authentication-agent-event-logs"></a>删除身份验证代理事件日志

此产品还可创建 **Windows 事件日志**。 有关详细信息，请参阅[此文](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx)。

若要查看与直通身份验证代理相关的日志，请在服务器上打开“事件查看器”应用程序，然后在 **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** 下查看。

### <a name="delete-authentication-agent-trace-log-files"></a>删除身份验证代理跟踪日志文件

应该每隔 48 小时定期检查 **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\** 的内容，并删除此文件夹的内容。 

>[!IMPORTANT]
>如果身份验证代理服务正在运行，则无法删除此文件夹中的当前日志文件。 重试之前请停止该服务。 为避免用户登录失败，应事先配置直通身份验证以实现[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)。

可以使用 Windows 资源管理器查看和删除这些文件，或使用以下脚本执行所需的操作：

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

若要将此脚本计划为每隔 48 小时运行，请执行以下步骤：

1.  将脚本保存在具有“.PS1”扩展名的文件中。
2.  打开“控制面板”，单击“系统和安全性”。
3.  在“管理工具”标题下，单击“计划任务”。
4.  在“任务计划程序”中，右键单击“任务计划库”，并单击“创建基本任务...”。
5.  输入新任务的名称，然后单击“下一步”。
6.  为**任务触发器**选择**每日**，单击**下一步**。
7.  将重复周期设置为两天，单击“下一步”。
8.  选择“启动程序”作为操作，单击“下一步”。
9.  在“程序/脚本”框中键入 **PowerShell**，在标记为“添加参数(可选)”的框中输入之前创建的脚本的完整路径，然后单击“下一步”。
10. 下一屏幕会显示要创建的任务摘要。 检查各个值，然后单击“完成”以创建任务：
 
### <a name="note-about-domain-controller-logs"></a>关于域控制器日志的注意事项

如果启用了审核日志，此产品可能会为域控制器生成安全日志。 若要了解有关配置审核策略的详细信息，请阅读[此文](https://technet.microsoft.com/library/dd277403.aspx)。

## <a name="next-steps"></a>后续步骤
* [查看信任中心上的 Microsoft 隐私策略](https://www.microsoft.com/trustcenter)
- [故障排除](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解决使用此功能时遇到的常见问题。
