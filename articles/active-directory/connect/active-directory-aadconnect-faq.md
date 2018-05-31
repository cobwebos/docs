---
title: Azure Active Directory Connect：常见问题 - Azure | Microsoft 文档
description: 此页包含有关 Azure AD Connect 的常见问题。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: billmath
ms.openlocfilehash: 46a9bf47b4998c4d5be47f67556fbdb3ba7b71db
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054088"
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Azure Active Directory Connect 常见问题

## <a name="general-installation"></a>常规安装
**问：如果 Azure AD 全局管理员已启用 2FA，安装是否能够正常进行？**  
2016 年 2 月版本开始支持此功能。

**问：Azure AD Connect 是否提供无人值守安装方法？**  
仅支持使用安装向导来安装 Azure AD Connect。 不支持无人值守和静默安装。

**问：我有一个林，但无法连接到其中的某个域。如何安装 Azure AD Connect？**  
2016 年 2 月版本开始支持此功能。

**问：AD DS Health 代理是否可以在 Server Core 上运行？**  
是的。 安装代理后，可以使用以下 PowerShell cmdlet 完成注册过程： 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**问：AADConnect 是否支持从两个域同步到一个 Azure AD？**</br>
能，系统支持该操作。 请参阅[多个域](active-directory-aadconnect-multiple-domains.md)
 
**问：是否支持在 Azure AD Connect 中同一个 Active Directory 域具有多个连接器？**</br> 否，不支持 

## <a name="network"></a>网络
**问：我的防火墙、网络设备或其他软硬件会限制在网络上打开连接的最长时间。使用 Azure AD Connect 时，客户端超时阈值应设为多少？**  
所有网络软件、物理设备或其他软硬件限制最长连接时间的阈值应该至少为 5 分钟 (300 秒)，使装有 Azure AD Connect 客户端的服务器能够与 Azure Active Directory 连接。 这同样适用于以前发布的 Microsoft 标识同步工具。

**是否支持 SLD（单一标签域）？**  
Azure AD Connect 不支持使用 SLD 的本地林/域。

**问：是否支持具有非连续 AD 域的林？**  
Azure AD Connect 不支持包含非连续命名空间的本地林。

**问：是否支持包含句点的 NetBios 名称？**  
Azure AD Connect 不支持 NetBios 名称包含句点“.”的本地林/域。

**问：是否支持纯 IPv6 环境？**  
Azure AD Connect 不支持纯 IPv6 环境。

## <a name="federation"></a>联合
**问：如果我收到一封电子邮件，要求我续订 Office 365 证书，我该怎么办？**  
请参考[续订证书](active-directory-aadconnect-o365-certs.md)主题中所述的关于如何续订证书的指南。

**问：我为 O365 信赖方设置了“自动更新信赖方”。当我的令牌签名证书自动滚动更新时，我是否需要采取任何措施？**  
请参考[续订证书](active-directory-aadconnect-o365-certs.md)一文中所述的指导。

## <a name="environment"></a>环境
**问：安装 Azure AD Connect 之后，是否支持重命名服务器？**  
不会。 更改服务器名称将导致同步引擎无法连接到 SQL 数据库，并且服务将无法启动。

## <a name="identity-data"></a>标识数据
**问：Azure AD 中的 UPN (userPrincipalName) 属性与本地 UPN 不匹配，这是为什么？**  
请参阅以下文章：

* [Office 365、Azure 或 Intune 中的用户名与本地 UPN 或备用登录 ID 不匹配](https://support.microsoft.com/en-us/kb/2523192)
* [在将用户帐户的 UPN 更改为使用不同的联合域后，Azure Active Directory 同步工具未同步更改](https://support.microsoft.com/en-us/kb/2669550)

还可以根据 [Azure AD Connect 同步服务功能](active-directory-aadconnectsyncservice-features.md)中所述配置 Azure AD，以允许同步引擎更新 userPrincipalName。

**问：是否支持本地 AD 组/联系人对象与现有 Azure AD 组/联系人对象的软匹配？**  
是，这将取决于 proxyAddress。  未启用邮件的组不支持软匹配。

**问：是否支持手动设置现有 Azure AD 组/联系人对象的 ImmutableId 属性，以将其硬匹配到本地 AD 组/联系人对象？**  
不能，目前不支持。

## <a name="custom-configuration"></a>自定义配置
**问：在哪里可以找到 Azure AD Connect 的 PowerShell cmdlet 介绍？**  
仅支持客户使用本站点上介绍的 cmdlet，而不支持使用 Azure AD Connect 中的其他 PowerShell cmdlet。

**问：我是否可以使用 *Synchronization Service Manager* 中的“服务器导出/服务器导入”在服务器之间移动配置？**  
不会。 此选项不会检索所有配置设置，因此不应使用。 应该改用向导在第二台服务器上创建基础配置，并使用同步规则编辑器生成 PowerShell 脚本，如此即可在服务器之间移动任何自定义规则。 请参阅[交叉迁移](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)。

**问：是否可以为 Azure 登录页缓存密码，这是否会因为它包含一个具有 autocomplete = "false" 属性的密码输入元素而被阻止？**</br>
目前不支持修改密码输入字段的 HTML 属性，包括 autocomplete 标记。 我们目前正在开发一种功能，它将允许使用自定义 Javascript 向密码字段添加任何属性。 此功能应当会在 2017 年的下半年推出。

**问：Azure 登录页上会显示之前已成功登录的用户的用户名。是否可以关闭此行为？**</br>
目前不支持修改登录页的 HTML 属性。 我们目前正在开发一种功能，它将允许使用自定义 Javascript 向密码字段添加任何属性。 此功能应当会在 2017 年的下半年推出。

**问：是否有方法来阻止并发会话？**</br>
不会。

## <a name="auto-upgrade"></a>自动升级

**问：使用自动升级有什么好处？其结果是什么？**</br>
建议所有客户为安装的 Azure AD Connect 启用自动升级。 好处是客户可以一直接收最新的修补程序，包括在 Azure AD Connect 中发现的漏洞的安全更新。 升级过程很轻松，只要有新版本发布就会自动进行。 每次发布新版本，我们都会通过自动升级为成千上万的 Azure AD Connect 客户提供服务。

自动升级过程始终会先确定某个安装是否符合自动升级的条件（这包括查找对规则的自定义更改、特定的环境因素等），如果符合，则会进行升级和升级测试。 如果测试表明升级未成功，则会自动还原以前的版本。

根据环境大小，此过程可能需要数小时才能完成，而在升级过程中，不会在 Windows Server AD 和 Azure AD 之间进行同步。

**问：我收到一封电子邮件，指出我的自动升级失效，需安装新版本。为什么我需要这样做？**</br>
我们去年发布了一个 Azure AD Connect 版本，该版本在特定情况下会禁用服务器上的自动升级功能。 我们在上月末发布的 Azure AD Connect 1.1.750.0 版中修复了此问题。 受此问题影响的客户需手动升级到最新版 Azure AD Connect 才能解决问题。 若要手动进行升级，必须下载并运行最新版的 AADConnect.msi 文件。
 
-  如果当前版本低于 1.1.750.0，则必须升级到[可在此处下载](https://www.microsoft.com/en-us/download/details.aspx?id=47594)的最新版本。
- 如果 Azure AD Connect 版本为 1.1.750.0 或更高，则不需执行任何操作来解决自动升级问题，因为你所使用的版本已经包含该问题的修复程序。 

**问：我收到一封电子邮件，要求我升级到最新版本，以便重新启用自动升级。我的版本是 1.1.654.0，我需要升级吗？** </br>    
需要。你需要升级到 1.1.750 或更高版本才能重新启用自动升级。 这是说明如何升级到更高版本的链接

**问：我收到一封电子邮件，要求我升级到最新版本，以便重新启用自动升级。我已经通过 PowerShell 启用了自动升级，是否仍需安装最新版本？**</br>    
是的，你仍需升级到 1.1.750.0 或更高版本。 通过 PowerShell 启用自动升级服务不会解决在 1.1.750 之前的版本中发现的自动升级问题

**问：我想要升级到更高版本，但不确定谁安装了 Azure AD Connect，而且我们没有用户名和密码。我们需要该凭据吗？**</br>
你不需要知道最初用来升级 Azure AD Connect 的用户名和密码 – 任何具有全局管理员角色的 Azure AD 帐户都可以使用。

**问：如何确定所用 Azure AD Connect 的版本？**</br>   
若要确定安装在服务器上的 Azure AD Connect 的具体版本，请转到“控制面板”，然后在“程序 > 程序和功能”中查找 Microsoft Azure AD Connect 的已安装版本：

![版本](media/active-directory-aadconnect-faq/faq1.png)

**问：如何升级到最新版的 AADConnect？**</br>    
[此文](active-directory-aadconnect-upgrade-previous-version.md)说明了如何升级到更高版本。 

**问：我们去年已升级到最新版的 AADConnect，是否需要再次升级？**</br> Azure AD Connect 团队会对服务进行频繁的更新，因此你必须使用最新的版本来更新服务器，这样才能充分利用 Bug 修复、安全更新和新功能的优势。 如果启用自动升级，则会自动更新软件版本。 若要查找 Azure AD Connect 的版本发行历史记录，请单击此[链接](active-directory-aadconnect-version-history.md)。

**问：进行升级需要多长时间？对我的用户有什么影响？**</br>    
升级所需时间取决于租户大小。如果公司较大，最好是在晚上或周末升级。 注意，在升级期间，不能进行同步。

**问：我认为我升级到了 AADConnect，但在 Office 门户中，仍然显示 DirSync。为什么会这样？**</br>    
Office 团队会更新 Office 门户，使之反映当前的产品名称，但不是反映你使用的同步工具。

**问：我查看了我的自动升级状态，发现它显示“已暂停”。为什么是“已暂停”？我应该启用它吗？**</br>     
在以前的版本中存在一个 Bug，该 Bug 在特定情况下会将自动升级状态设置为“已暂停”。 手动启用它在技术上是可以做到的，但需要执行多项复杂的步骤，因此最好是安装最新版本的 Azure AD Connect

**问：我的公司有严格的更改管理要求，而我希望控制它的推出时间，我能控制自动升级的启动时间吗？**</br> 不能。目前没有此类功能，但我们会考虑在将来的版本中推出。

**问：如果自动升级失败，是否会通过电子邮件通知我？怎么才能知道升级成功？**</br>     
不会将升级结果通知给你。我们会考虑在将来的版本中增加此功能。

**问：你们是否会发布一个关于何时计划推出自动升级的时间线？**</br>    
我们在发布新版本时，第一步就是自动升级，因此只要有新版本，我们就会推送自动升级。 新版 Azure AD Connect 会在 [Azure AD 路线图](../../active-directory/whats-new.md)中预先公告。

**问：自动升级是否会升级 AAD Connect Health？**</br>   是的，自动升级也会升级 AAD Connect Health

**问：你们是否也会自动升级暂存模式下的 AAD Connect 服务器？**</br>   
否。不能自动升级暂存模式下的 Azure AD Connect 服务器。

**问：如果自动升级失败而 AAD Connect 服务器无法启动，该怎么办？**</br>   
Azure AD Connect 服务偶尔会在升级以后无法启动。 在这种情况下，请重启服务器，通常就会解决问题。 如果 Azure AD Connect 服务仍然无法启动，请开具支持票证。 这是说明如何开票的[链接](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)。 

**问：我不知道升级到新版 Azure AD Connect 后会有什么风险。你们能通过电话帮助我升级吗？**</br>
如果在升级到新版 Azure AD Connect 时需要帮助，请开具支持票证。这是说明如何开票的[链接](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)。

## <a name="troubleshooting"></a>故障排除
**问：如何获取有关 Azure AD Connect 的帮助？**

[搜索 Microsoft 知识库 (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* 在 Microsoft 知识库 (KB) 中搜索有关 Azure AD Connect 支持的常见故障维修服务问题的技术解决方案。

[Microsoft Azure Active Directory 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* 单击[此处](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，在社区中搜索和浏览技术问题与答案，或提出自己的问题。

[如何获取 Azure AD 支持](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* 使用此链接，以便通过 Azure 门户获取支持。

