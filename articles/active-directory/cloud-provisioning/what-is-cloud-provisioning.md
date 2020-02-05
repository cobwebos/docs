---
title: 什么是 Azure AD Connect 云设置。 | Microsoft Docs
description: 介绍 Azure AD Connect 云设置。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69b8cbdba018b9d03dca67573b1f4fe6efa546b6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024035"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>什么是 Azure AD Connect 云预配？
Azure AD Connect 云预配是一种新的 Microsoft 代理，旨在满足和实现混合标识目标，以便同步用户、组和联系人以 Azure AD。  它可以与 Azure AD Connect 同步一起使用，并且具有以下优势：
    
- 支持从多林连接 Active Directory 林环境同步到 Azure AD 租户：常见方案包括合并 & 收购，其中获取的公司的 AD 林与父公司的广告林隔离过去有多个 AD 林的林和公司。
- 使用轻型预配代理简化安装：代理作为从 AD 到 Azure AD 的桥，并在云中托管所有同步配置。 
- 可以使用多个设置代理来简化高可用性部署，这对于依赖于从 AD 到 Azure AD 的密码哈希同步的组织尤其重要。


![什么是 Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Azure AD Connect 云设置与 Azure AD Connect 同步有何不同？
通过 Azure AD Connect 云预配，从 AD 预配到 Azure AD 在 Microsoft Online Services 中进行协调。 组织仅需在其本地和 IaaS 托管的环境中部署一个轻型代理，该代理充当 Azure AD 和 AD 之间的桥梁。 设置配置存储在 Azure AD 中，并作为服务的一部分进行管理。

下表提供 Azure AD Connect 和 Azure AD Connect 云预配之间的比较：

| 功能 | Azure Active Directory Connect 同步| Azure Active Directory Connect 云预配 |
|:--- |:---:|:---:|
|连接到单个本地 AD 林|● |● |
| 连接到多个本地 AD 林 |● |● |
| 连接到多个已断开连接的本地 AD 林 | |● |
| 轻型代理安装模式 | |● |
| 用于实现高可用性的多个活动代理 | |● |
| 连接到 LDAP 目录|●| | 
| 支持用户对象 |● |● |
| 支持组对象 |● |● |
| 支持 contact 对象 |● |● |
| 支持设备对象 |● | |
| 允许对属性流进行基本自定义 |● |● |
| 同步 Exchange online 属性 |● |● |
| 同步扩展属性1-15 |● |● |
| 同步客户定义的 AD 属性（目录扩展） |● | |
| 支持密码哈希同步 |●|●|
| 支持直通身份验证 |●||
| 支持联合身份验证 |●|●|
| 无缝单一登录|● |●|
| 支持在域控制器上安装 |● |● |
| 对 Windows Server 2012 和 Windows Server 2012 R2 的支持 |● |● |
| 筛选域/Ou/组 |● |● |
| 筛选对象的属性值 |● | |
| 允许同步极少量的属性 (MinSync) |● |● |
| 允许删除从 AD 流向 Azure AD 的属性 |● |● |
| 允许对属性流进行高级自定义 |● | |
| 支持写回（密码、设备、组） |● | |
| Azure AD 域服务支持|● | |
| [Exchange 混合写回](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| 支持每个 AD 域超过50000个对象 |● | |

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [安装云预配](how-to-install.md)
