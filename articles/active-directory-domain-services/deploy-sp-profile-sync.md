---
title: 使用 Azure AD DS 启用 SharePoint 用户配置文件服务 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 域服务托管域以支持 SharePoint Server 的配置文件同步
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: iainfou
ms.openlocfilehash: a24ff1d5f94a50dc30819e6ab86318592da72e45
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705206"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>配置 Azure Active Directory 域服务以支持 SharePoint Server 的用户配置文件同步

SharePoint Server 包括同步用户配置文件的服务。 此功能可让用户配置文件存储在一个中心位置，并可跨多个 SharePoint 站点和场进行访问。 若要配置 SharePoint Server 用户配置文件服务，必须在 Azure Active Directory 域服务（Azure AD DS）托管域中授予适当的权限。 有关详细信息，请参阅[SharePoint Server 中的用户配置文件同步](https://technet.microsoft.com/library/hh296982.aspx)。

本文介绍如何配置 Azure AD DS 以允许 SharePoint Server 用户配置文件同步服务。

## <a name="before-you-begin"></a>开始之前

若要完成本文，需要具备以下资源和权限：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建和配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]的教程。
* 已加入到 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建管理 VM][tutorial-create-management-vm]教程。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。
* 用户配置文件同步服务的 SharePoint 服务帐户。
    * 如果需要，请参阅[规划 SharePoint Server 中的管理帐户和服务帐户][sharepoint-service-account]。

## <a name="service-accounts-overview"></a>服务帐户概述

在 Azure AD DS 托管域中，名为 " **AAD DC 服务帐户**" 的安全组作为 "*用户*" 组织单位（OU）的一部分存在。 此安全组的成员具有以下委托权限：

- 根 DSE 上的 "**复制目录更改**" 特权。
- 对*配置*命名上下文（`cn=configuration` 容器）的 "**复制目录更改**" 权限。

**AAD DC 服务帐户**安全组也是内置组**Windows 2000 兼容访问权限**的成员。

添加到此安全组后，将向 SharePoint Server 用户配置文件同步服务的服务帐户授予所需的权限才能正常工作。

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>启用 SharePoint Server 用户配置文件同步支持

SharePoint 服务器的服务帐户需要足够的权限来复制对目录所做的更改，并让 SharePoint Server 用户配置文件同步正常工作。 若要提供这些权限，请将用于 SharePoint 用户配置文件同步的服务帐户添加到 " **AAD DC 服务帐户**" 组。

在 Azure AD DS 管理虚拟机中，完成以下步骤：

> [!NOTE]
> 若要编辑 Azure AD DS 托管域中的组成员身份，你必须登录到作为*AAD DC 管理员*组成员的用户帐户。

1. 从 "开始" 屏幕中，选择 "**管理工具**"。 其中显示了在[创建管理 VM][tutorial-create-management-vm]教程中安装的可用管理工具列表。
1. 若要管理组成员身份，请从管理工具列表中选择 " **Active Directory 管理中心**"。
1. 在左窗格中，选择 Azure AD DS 托管域，如*aadds.contoso.com*。 将显示现有 Ou 和资源的列表。
1. 选择 "**用户**" OU，然后选择 " *AAD DC 服务帐户*" 安全组。
1. 选择 "**成员**"，然后选择 "**添加 ...** "。
1. 输入 SharePoint 服务帐户的名称，然后选择 **"确定"** 。 在下面的示例中，SharePoint 服务帐户的名称为*spadmin*：

    ![将 SharePoint 服务帐户添加到 "AAD DC 服务帐户" 安全组](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[在 SharePoint Server 中授予配置文件同步的 Active Directory 域服务权限](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
