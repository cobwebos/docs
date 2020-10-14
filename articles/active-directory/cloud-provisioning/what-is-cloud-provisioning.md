---
title: 什么是 Azure AD Connect 云预配。 | Microsoft Docs
description: 介绍 Azure AD Connect 云预配。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9433945ffe1181b62ee193baf1c2dd0b89ec14c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856800"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>什么是 Azure AD Connect 云预配？
Azure AD Connect 云预配是一个新的 Microsoft 代理，旨在满足和实现在 Azure AD 中同步用户、组与联系人的混合标识目标。  它可以与 Azure AD Connect 同步一起使用，并提供以下优势：
    
- 支持从多林离线 Active Directory 林环境同步到 Azure AD 租户：常见的场景包括企业并购和收购，其中，被收购公司的 AD 林独立于父公司的 AD 林，并且这些公司在过去拥有多个 AD 林。
- 使用轻型预配代理简化安装：代理充当 AD 与 Azure AD 之间的桥梁，所有同步配置托管在云中。 
- 可以使用多个预配代理来简化高可用性部署，这对于依赖于在 AD 与 Azure AD 之间实现密码哈希同步的组织而言尤其重要。


![什么是 Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Azure AD Connect 云预配与 Azure AD Connect 同步有何不同？
使用 Azure AD Connect 云预配时，从 AD 到 Azure AD 的预配将在 Microsoft Online Services 中经过协调。 组织只需在其本地环境和 IaaS 托管环境中，部署一个轻型代理充当 Azure AD 与 AD 之间的桥梁。 预配配置存储在 Azure AD 中，并作为服务的一部分进行管理。

## <a name="azure-ad-connect-cloud-provisioning-video"></a>Azure AD Connect 云预配视频
下面的短视频简单扼要地介绍了 Azure AD Connect 云预配：

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-provisioning"></a>Azure AD Connect 与云预配之间的比较

下表提供 Azure AD Connect 与 Azure AD Connect 云预配之间的比较：

| 功能 | Azure Active Directory Connect 同步| Azure Active Directory Connect 云预配 |
|:--- |:---:|:---:|
|连接到单个本地 AD 林|● |● |
| 连接到多个本地 AD 林 |● |● |
| 连接到多个离线本地 AD 林 | |● |
| 轻型代理安装模式 | |● |
| 用于实现高可用性的多个活动代理 | |● |
| 连接到 LDAP 目录|●| | 
| 支持用户对象 |● |● |
| 支持组对象 |● |● |
| 支持联系人对象 |● |● |
| 支持设备对象 |● | |
| 允许对属性流进行基本自定义 |● |● |
| 同步 Exchange Online 属性 |● |● |
| 同步扩展属性 1-15 |● |● |
| 同步客户定义的 AD 属性（目录扩展） |● | |
| 支持密码哈希同步 |●|●|
| 支持直通身份验证 |●||
| 支持联合身份验证 |●|●|
| 无缝单一登录|● |●|
| 支持在域控制器上安装 |● |● |
| 对 Windows Server 2012 和 Windows Server 2012 R2 的支持 |● |● |
| 按域/OU/组进行筛选 |● |● |
| 按对象的属性值进行筛选 |● | |
| 允许同步极少量的属性 (MinSync) |● |● |
| 允许删除从 AD 流向 Azure AD 的属性 |● |● |
| 允许对属性流进行高级自定义 |● | |
| 支持写回（密码、设备、组） |● | |
| Azure AD 域服务支持|● | |
| [Exchange 混合写回](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| 支持对每个 AD 域配置 50,000 个以上的对象 |● | |

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [安装云预配](how-to-install.md)
