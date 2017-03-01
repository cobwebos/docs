---
title: "Azure AD Connect 和 Federation | Microsoft Docs"
description: "此页是使用 Azure AD Connect 进行 AD FS 操作的所有相关文档的中央位置"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: fd3699e4ce423ae9490d5bf788729c43eaa623eb
ms.openlocfilehash: 19ebfae5de8576402348e3163f99a54b9308a078


---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect 和联合身份验证
通过 Azure AD Connect，你可以配置与本地 AD FS 和 Azure AD 的联合身份验证。 通过联合身份验证登录，你可以让用户能够使用本地密码登录基于 Azure AD 的服务；使用公司网络时，无需再次输入密码就可登录服务。 使用 AD FS 的联合选项可让你在 Windows Server 2012 R2 场中部署新的或指定现有的 AD FS。

本主题是关于 Azure AD Connect 的联合身份验证相关功能的信息主页，其中列出了指向所有其他相关主题的链接。 有关 Azure AD Connect 的链接，请参阅“将本地标识与 Azure Active Directory 集成”。

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect - 联合身份验证主题
| 主题 | 涵盖的内容和阅读时机 |
|:--- |:--- |
| **Azure AD Connect 用户登录选项** | |
| [了解用户登录选项](active-directory-aadconnect-user-signin.md) |各种用户登录选项以及它们如何影响 Azure 登录的用户体验的说明 |
| **使用 Azure AD Connect 安装 AD FS** | |
| [先决条件](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |通过 Azure AD Connect 成功安装 AD FS 的前提条件 |
| [配置 AD FS 场](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |如何使用 Azure AD Connect 安装新的 AD FS 场 |
| **修改 AD FS 配置** | |
| [修复信任](active-directory-aadconnect-federation-management.md#repairthetrust) |如何修复当前本地 AD FS 和 Office 365/Azure 之间的信任 |
| [添加新的 AD FS 服务器](active-directory-aadconnect-federation-management.md#addadfsserver) |初始安装后，使用其他 AD FS 服务器扩展 AD FS 场 |
| [添加新的 AD FS WAP 服务器](active-directory-aadconnect-federation-management.md#addwapserver) |初始安装后，使用其他 WAP 服务器扩展 AD FS 场 |
| [添加新的联合域](active-directory-aadconnect-federation-management.md#addfeddomain) |添加另一个域来与 Azure AD 联合 |
| **安装后任务** | |
| [添加自定义公司徽标/插图](active-directory-aadconnect-federation-management.md#customlogo) |通过指定将在 AD FS 登录页显示的自定义徽标来调整登录体验 |
| [添加登录说明](active-directory-aadconnect-federation-management.md#addsignindescription) |更改 AD FS 登录页上的登录说明 |
| [修改 AD FS 声明规则](active-directory-aadconnect-federation-management.md#modclaims) |对应于 Azure AD Connect 同步配置修改/添加 AD FS 中的声明规则 |

## <a name="additional-resources"></a>其他资源
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
* [Azure 中的 AD FS 部署](active-directory-aadconnect-azure-adfs.md)
* [使用 Azure 流量管理器在 Azure 中跨地理部署 AD FS 实现高可用性](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)



<!--HONumber=Jan17_HO2-->


