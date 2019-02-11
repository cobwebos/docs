---
title: Azure AD Connect 和联合身份验证 | Microsoft Docs
description: 此页是与使用 Azure AD Connect 进行 AD FS 操作相关的所有文档的中央位置。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 28c0ddbb735de041497b6705759d03c881b7e87e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494351"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect 和联合身份验证
Azure Active Directory (Azure AD) Connect 可让你配置使用本地 Active Directory 联合身份验证服务 (AD FS) 和 Azure AD 进行的联合身份验证。 通过联合身份验证登录，可以让用户能够使用本地密码登录基于 Azure AD 的服务；使用公司网络时，无需再次输入密码就可登录服务。 使用 AD FS 的联合身份验证选项，可以部署 AD FS 的新安装，也可以在 Windows Server 2012 R2 场中指定现有安装。

本主题是关于 Azure AD Connect 的联合身份验证相关功能的信息主页。 它列出了指向所有相关主题的链接。 有关 Azure AD Connect 的链接，请参阅[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)。

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect：联合身份验证主题
| 主题 | 涵盖的内容和阅读时机 |
|:--- |:--- |
| **Azure AD Connect 用户登录选项** | |
| [了解用户登录选项](plan-connect-user-signin.md) |了解各个用户登录选项以及这些选项如何影响 Azure 登录用户体验。 |
| **使用 Azure AD Connect 安装 AD FS** | |
| [先决条件](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |了解通过 Azure AD Connect 成功安装 AD FS 的先决条件。 |
| [配置 AD FS 场](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |使用 Azure AD Connect 安装新的 AD FS 场。 |
| [使用备用登录 ID 与 Azure AD 联合](how-to-connect-fed-management.md#alternateid) | 使用备用登录 ID 配置联合  |
| **修改 AD FS 配置** | |
| [修复信任](how-to-connect-fed-management.md#repairthetrust) |修复当前本地 AD FS 和 Office 365/Azure 之间的信任。 |
| [新增 AD FS 服务器](how-to-connect-fed-management.md#addadfsserver) |初始安装后，使用其他 AD FS 服务器扩展 AD FS 场。 |
| [新增 AD FS WAP 服务器](how-to-connect-fed-management.md#addwapserver) |初始安装后，使用其他 Web 应用程序代理 (WAP) 服务器扩展 AD FS 场。 |
| [添加新的联合域](how-to-connect-fed-management.md#addfeddomain) |添加其他要与 Azure AD 联合的域。 |
| [更新 SSL 证书](how-to-connect-fed-ssl-update.md)| 更新 AD FS 场的 SSL 证书。 |
| [续订 Office 365 和 Azure AD 的联合身份验证证书](how-to-connect-fed-o365-certs.md)|使用 Azure AD 续订 O365 证书。|
| **其他联合配置** | |
| [将 Azure AD 的多个实例与 AD FS 的单个实例联合](how-to-connect-fed-single-adfs-multitenant-federation.md) | 联合多个 Azure AD 与单个 AD FS 场| 
| [添加自定义公司徽标/插图](how-to-connect-fed-management.md#customlogo) |通过指定 AD FS 登录页上显示的自定义徽标来修改登录体验。 |
| [添加登录说明](how-to-connect-fed-management.md#addsignindescription) |更改 AD FS 登录页上的登录说明。 |
| [修改 AD FS 声明规则](how-to-connect-fed-management.md#modclaims) |在 AD FS 中修改或添加对应于 Azure AD Connect 同步配置的声明规则。 |


## <a name="additional-resources"></a>其他资源
* [联合两个 Azure AD 与单个 AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Azure 中的 AD FS 部署](how-to-connect-fed-azure-adfs.md)
* [使用 Azure 流量管理器在 Azure 中部署高可用性跨地域 AD FS](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
