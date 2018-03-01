---
title: "Azure AD Connect 和一般数据保护条例 | Microsoft Docs"
description: "本文介绍如何保证 Azure AD Connect 符合 GDPR 的规定。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 68b36e1806d3338b07d4c2c400de233aff270132
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect"></a>GDPR 符合性和 Azure AD Connect 

欧洲隐私法律——[一般数据保护条例 (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm)将于 2018 年 5 月生效。 GDPR 针对向欧盟 (EU) 居民提供产品及服务，或收集和分析欧盟居民相关信息的公司、政府机构、非营利组织和其他组织制定了新的规则。 无论你身在何处，均需遵守 GDPR。 

Microsoft 产品和服务可立即用于帮助你满足 GDPR 要求。 请访问[信任中心](https://www.microsoft.com/trustcenter)，深入了解 Microsoft 隐私策略

>[!NOTE] 
>本文介绍 Azure AD Connect 和 GDPR 符合性。  有关 Azure AD Connect Health 和 GDPR 符合性的详细信息，请参阅[此处](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)的文章。

在安装 Azure AD Connect 时，可在以下两方面符合一般数据保护条例的规定：

1.  根据请求提取用户的数据以及从安装项中删除该用户的数据
2.  确保数据保留时长均不超过 48 小时。

Azure AD Connect 推荐采用第二种方法。  原因是至今为止，该方法最容易实施和维护。

Azure AD Connect 同步服务器会存储符合 GDPR 的以下数据：
1.  Azure AD Connect 数据库中的用户相关数据
2.  Windows 事件日志文件中可能包含用户相关信息的数据
3.  Azure AD Connect 安装日志文件中可能包含用户信息的数据

为保证符合 GDPR，Azure AD Connect 客户须采用以下准则：
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
- [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)。
- [Azure AD Connect Health 和 GDPR](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)