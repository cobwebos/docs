---
title: "Azure AD Connect：常见问题 | Microsoft Docs"
description: "此页包含有关 Azure AD Connect 的常见问题。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: a2b4c14fa7f167e9a7e0cddeedbf18579117c478
ms.openlocfilehash: 7d8ce3c869c7e3734fa1d4bc27e52325dafc651a


---
# <a name="azure-ad-connect-faq"></a>Azure AD Connect 常见问题
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

## <a name="network"></a>网络
**问：我的防火墙、网络设备或其他软硬件会限制在网络上打开连接的最长时间。使用 Azure AD Connect 时，客户端超时阈值应设为多少？**  
所有网络软件、物理设备或其他软硬件限制最长连接时间的阈值应该至少为 5 分钟 (300 秒)，使装有 Azure AD Connect 客户端的服务器能够与 Azure Active Directory 连接。 这同样适用于以前发布的 Microsoft 标识同步工具。

**是否支持 SLD（单一标签域）？**  
Azure AD Connect 不支持使用 SLD 的本地林/域。

**问：是否支持包含句点的 NetBios 名称？**  
Azure AD Connect 不支持 NetBios 名称包含句点“.”的本地林/域。

## <a name="federation"></a>联合
**问：如果我收到一封电子邮件，要求我续订 Office 365 证书，我该怎么办？**  
请参考[续订证书](active-directory-aadconnect-o365-certs.md)主题中所述的关于如何续订证书的指南。

**问：我为 O365 信赖方设置了“自动更新信赖方”。当我的令牌签名证书自动滚动更新时，我是否需要采取任何措施？**  
请参考[续订证书](active-directory-aadconnect-o365-certs.md)一文中所述的指导。

## <a name="environment"></a>环境
**问：安装 Azure AD Connect 之后，是否支持重命名服务器？**  
否。 更改服务器名称将导致同步引擎无法连接到 SQL 数据库，并且服务将无法启动。

## <a name="identity-data"></a>标识数据
**问：Azure AD 中的 UPN (userPrincipalName) 属性与本地 UPN 不匹配，这是为什么？**  
请参阅以下文章：

* [Office 365、Azure 或 Intune 中的用户名与本地 UPN 或备用登录 ID 不匹配](https://support.microsoft.com/en-us/kb/2523192)
* [在将用户帐户的 UPN 更改为使用不同的联合域后，Azure Active Directory 同步工具未同步更改](https://support.microsoft.com/en-us/kb/2669550)

你还可以根据 [Azure AD Connect 同步服务功能](active-directory-aadconnectsyncservice-features.md)中所述配置 Azure AD，以允许同步引擎更新 userPrincipalName。

**问：是否支持本地 AD 组/联系人对象与现有 Azure AD 组/联系人对象的软匹配？**  
不能，目前不支持。

**问：是否支持手动设置现有 Azure AD 组/联系人对象的 ImmutableId 属性，以将其硬匹配到本地 AD 组/联系人对象？**  
不能，目前不支持。

## <a name="custom-configuration"></a>自定义配置
**问：在哪里可以找到 Azure AD Connect 的 PowerShell cmdlet 介绍？**  
仅支持客户使用本站点上介绍的 cmdlet，而不支持使用 Azure AD Connect 中的其他 PowerShell cmdlet。

**问：我是否可以使用 *Synchronization Service Manager* 中的“服务器导出/服务器导入”在服务器之间移动配置？  
否。 此选项不会检索所有配置设置，因此不应使用。 应该改用向导在第二台服务器上创建基础配置，并使用同步规则编辑器生成 PowerShell 脚本，如此即可在服务器之间移动任何自定义规则。 请参阅[将自定义配置从活动服务器移到暂存服务器](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server)。

## <a name="troubleshooting"></a>故障排除
**问：如何获取有关 Azure AD Connect 的帮助？**

[搜索 Microsoft 知识库 (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* 在 Microsoft 知识库 (KB) 中搜索有关 Azure AD Connect 支持的常见故障维修服务问题的技术解决方案。

[Microsoft Azure Active Directory 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* 单击[此处](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，在社区中搜索和浏览技术问题与答案，或提出自己的问题。

[Azure AD Connect 客户支持](https://manage.windowsazure.com/?getsupport=true)

* 使用此链接，以便通过 Azure 门户获取支持。




<!--HONumber=Jan17_HO1-->


