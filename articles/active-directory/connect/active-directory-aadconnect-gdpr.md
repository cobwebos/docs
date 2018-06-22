---
title: Azure AD Connect 和用户隐私 | Microsoft Docs
description: 本文介绍如何保证 Azure AD Connect 符合 GDPR 的规定。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8e3f81a6480e9de55c8f803e2266c4ac6e33c316
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590532"
---
# <a name="user-privacy-and-azure-ad-connect"></a>用户隐私和 Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>本文介绍 Azure AD Connect 和用户隐私。  有关 Azure AD Connect Health 和用户隐私的信息，请参阅[此处](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)的文章。

可通过以下两种方式提高 Azure AD Connect 安装的用户隐私：

1.  根据请求提取用户的数据以及从安装项中删除该用户的数据
2.  确保数据保留时长均不超过 48 小时。

Azure AD Connect 团队建议使用第二个选项，因为它更易于实施和维护。

Azure AD Connect 同步服务器可存储以下用户隐私数据：
1.  Azure AD Connect 数据库中的用户相关数据
2.  Windows 事件日志文件中可能包含用户相关信息的数据
3.  Azure AD Connect 安装日志文件中可能包含用户信息的数据

删除用户数据时，Azure AD Connect 客户应遵循以下准则：
1.  定期删除包含 Azure AD Connect 安装日志文件的文件夹中的内容——至少每 48 小时删除一次
2.  此产品还可创建事件日志。  要深入了解事件日志，请参阅[此处的文档](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx)。

当从生成用户数据的源系统中删除此用户数据时，会自动删除 Azure AD Connect 数据库中的此数据。 管理员无需执行特定操作既能保证符合 GDPR。  但是，至少每两天需要将 Azure AD Connect 数据与数据源进行同步一次。

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>删除 Azure AD Connect 安装日志文件文件夹内容
定期检查和删除 c:\programdata\aadconnect 文件夹中的内容，但保留 PersistedState.Xml 文件。 此文件会保留 Azure A Connect 先前安装的状态，并在进行升级安装时使用。 此文件不包含任何用户数据，不得删除。

>[!IMPORTANT]
>请勿删除 PersistedState.xml 文件。  此文件不包含任何用户信息，仅保存先前安装的状态。

可使用 Windows 资源管理器查看和删除这些文件，也可使用如下脚本执行必需的操作：


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>将此脚本设置为每 48 小时运行一次
请使用以下步骤将脚本设置为每 48 小时运行一次。

1.  将脚本保存在扩展名为 .PS1 的文件中，然后打开控制面板并单击“系统和安全”。
    ![系统](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  在“管理工具”标题下，单击“安排任务”。
    ![任务](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  在任务计划程序中，右键单击“任务计划库”，再单击“创建基本任务...”
4.  输入新任务的名称，然后单击“下一步”。
5.  针对任务触发器选择“每日”，再单击“下一步”。
6.  将重复周期设置为“两天”并单击“下一步”。
7.  选择“启动项目”作为操作，并单击“下一步”。
8.  在“程序/脚本”框中键入 PowerShell，在标记为“添加参数(可选)”的框中输入之前创建的脚本的完整路径，然后单击“下一步”。
9.  下一屏幕会显示要创建的任务摘要。 验证各个值，然后单击“完成”以创建任务。



## <a name="next-steps"></a>后续步骤
* [查看信任中心上的 Microsoft 隐私策略](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health 和用户隐私](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
