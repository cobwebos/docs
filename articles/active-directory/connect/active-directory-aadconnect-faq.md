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
ms.date: 03/09/2017
ms.author: billmath
ms.openlocfilehash: 07b0209ef94f91c00b98b8801323a58cd9d14494
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
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

## <a name="troubleshooting"></a>故障排除
**问：如何获取有关 Azure AD Connect 的帮助？**

[搜索 Microsoft 知识库 (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* 在 Microsoft 知识库 (KB) 中搜索有关 Azure AD Connect 支持的常见故障维修服务问题的技术解决方案。

[Microsoft Azure Active Directory 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* 单击[此处](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，在社区中搜索和浏览技术问题与答案，或提出自己的问题。

[如何获取 Azure AD 支持](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* 使用此链接，以便通过 Azure 门户获取支持。

