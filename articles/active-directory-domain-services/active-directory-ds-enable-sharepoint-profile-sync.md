---
title: Azure Active Directory 域服务：启用 SharePoint 用户配置文件服务的支持 | Microsoft 文档
description: 配置 Azure Active Directory 域服务托管域以支持 SharePoint Server 的配置文件同步
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 9e21547f6e1088677bb5699d17d81d170b4cab3d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332127"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>配置托管域以支持 SharePoint Server 的配置文件同步
SharePoint Server 包含一个用户配置文件服务用于用户配置文件同步。 若要设置用户配置文件服务，需要在 Active Directory 域中授予相应的权限。 有关详细信息，请参阅 [grant Active Directory Domain Services permissions for profile synchronization in SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)（在 SharePoint Server 2013 中授予配置文件同步的 Active Directory 域服务权限）。

本文介绍如何配置 Azure AD 域服务托管域，以部署 SharePoint Server 用户配置文件同步服务。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>“AAD DC 服务帐户”组
托管域上的“用户”组织单位中提供了一个名为“**AAD DC 服务帐户**”的安全组。 在托管域上的“Active Directory 用户和计算机”MMC 管理单元中可以看到此组。

![“AAD DC 服务帐户”安全组](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

此安全组的成员具有以下委托权限：
- 对托管域的根 DSE 的“复制目录更改”权限。
- 对托管域的配置命名上下文（cn=配置容器）的“复制目录更改”权限。

此安全组也是内置组 **Pre-Windows 2000 Compatible Access** 的成员。

![“AAD DC 服务帐户”安全组](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>使托管域能够支持 SharePoint Server 用户配置文件同步
可将用于 SharePoint 用户配置文件同步的服务帐户添加到“AAD DC 服务帐户”组。 因此，同步帐户可获得足够的权限来复制对目录所做的更改。 此配置步骤可让 SharePoint 服务器用户配置文件同步正常工作。

![AAD DC 服务帐户 - 添加成员](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD DC 服务帐户 - 添加成员](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>相关内容
* [技术参考 - 在 SharePoint Server 2013 中授予配置文件同步的 Active Directory 域服务权限](https://technet.microsoft.com/library/hh296982.aspx)
