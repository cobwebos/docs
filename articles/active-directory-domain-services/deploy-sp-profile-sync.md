---
title: 使用 Azure AD DS 启用共享点用户配置文件服务 |微软文档
description: 了解如何配置 Azure 活动目录域服务托管域以支持 SharePoint 服务器的配置文件同步
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 9d983015927d2635f69a327a9c5b168056542519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613867"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>配置 Azure 活动目录域服务以支持 SharePoint 服务器的用户配置文件同步

SharePoint 服务器包括用于同步用户配置文件的服务。 此功能允许将用户配置文件存储在中心位置，并跨多个 SharePoint 站点和服务器场访问。 要配置 SharePoint Server 用户配置文件服务，必须在 Azure 活动目录域服务 （Azure AD DS） 托管域中授予适当的权限。 有关详细信息，请参阅[SharePoint 服务器 中的用户配置文件同步](https://technet.microsoft.com/library/hh296982.aspx)。

本文介绍如何配置 Azure AD DS 以允许 SharePoint 服务器用户配置文件同步服务。

## <a name="before-you-begin"></a>开始之前

要完成本文，您需要以下资源和特权：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成创建[和配置 Azure 活动目录域服务实例][create-azure-ad-ds-instance]的教程。
* 加入到 Azure AD DS 托管域的 Windows 服务器管理 VM。
    * 如果需要，请完成教程以创建[管理 VM][tutorial-create-management-vm]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。**
* 用户配置文件同步服务的 SharePoint 服务帐户。
    * 如果需要，请参阅[计划共享点服务器中的管理和服务帐户][sharepoint-service-account]。

## <a name="service-accounts-overview"></a>服务帐户概述

在 Azure AD DS 托管域中，名为**AAD DC 服务帐户**的安全组作为*用户*组织单元 （OU） 的一部分存在。 此安全组的成员具有以下委托权限：

- **复制根**DSE 上的目录更改权限。
- 复制 *"配置*命名上下文"（`cn=configuration`容器）上的**目录更改**权限。

**AAD DC 服务帐户**安全组也是内置组**前 Windows 2000 兼容访问**的成员。

添加到此安全组时，SharePoint Server 用户配置文件同步服务的服务帐户将被授予正常工作所需的权限。

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>启用对 SharePoint 服务器用户配置文件同步的支持

SharePoint Server 的服务帐户需要足够的权限来复制对目录的更改，并让 SharePoint 服务器用户配置文件同步正常工作。 要提供这些权限，请将用于 SharePoint 用户配置文件同步的服务帐户添加到**AAD DC 服务帐户**组。

从 Azure AD DS 管理 VM 中，完成以下步骤：

> [!NOTE]
> 要编辑 Azure AD DS 托管域中的组成员身份，必须登录到*属于 AAD DC 管理员组的*用户帐户。

1. 在"开始"屏幕中，选择 **"管理工具**"。 在本教程中显示了用于[创建管理 VM][tutorial-create-management-vm]的可用管理工具的列表。
1. 要管理组成员身份，请从管理工具列表中选择**活动目录管理中心**。
1. 在左侧窗格中，选择 Azure AD DS 托管域，如*aaddscontoso.com*。 将显示现有非统组织和资源的列表。
1. 选择 **"用户**OU"，然后选择*AAD DC 服务帐户*安全组。
1. 选择 **"成员**"，然后选择 **"添加..."**
1. 输入 SharePoint 服务帐户的名称，然后选择 **"确定**"。 在下面的示例中，SharePoint 服务帐户名为*spadmin*：

    ![将 SharePoint 服务帐户添加到 AAD DC 服务帐户安全组](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅为[SharePoint 服务器中的配置文件同步授予活动目录域服务权限](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
