---
title: 在 Azure AD 域服务中创建组织单位（OU） |Microsoft Docs "
description: 了解如何在 Azure AD 域服务托管域中创建和管理自定义组织单位（OU）。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 7d651849f5c8d930d99e87931eed5b823e90113c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474763"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 域服务托管域中创建组织单位（OU）

Active Directory 域服务（AD DS）中的组织单位（Ou）允许您以逻辑方式将对象分组，例如用户帐户、服务帐户或计算机帐户。 然后，可以将管理员分配给特定 Ou，并应用组策略来强制实施目标配置设置。

Azure AD DS 托管域包括两个内置 Ou- *AADDC 计算机*和*AADDC 用户*。 *AADDC 计算机*OU 包含已加入到托管域的所有计算机的计算机对象。 *AADDC 用户*OU 包括 Azure AD 租户中的同步用户和组。 当您创建和运行使用 Azure AD DS 的工作负荷时，可能需要为应用程序创建服务帐户以对其自身进行身份验证。 若要组织这些服务帐户，通常在 Azure AD DS 托管域中创建自定义 OU，然后在该 OU 中创建服务帐户。

在混合环境中，在本地 AD DS 环境中创建的 Ou 不会同步到 Azure AD DS。 Azure AD DS 托管域使用平面 OU 结构。 尽管所有用户帐户和组都是从不同的本地域或林进行同步，但即使已在此处配置了分层 OU 结构，所有用户帐户和组都将存储在*AADDC Users*容器中。

本文介绍如何在 Azure AD DS 托管域中创建 OU。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

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

## <a name="custom-ou-considerations-and-limitations"></a>自定义 OU 注意事项和限制

在 Azure AD DS 托管域中创建自定义 Ou 时，可以获得更多的管理灵活性，以便用户管理和应用组策略。 与本地 AD DS 环境相比，在 Azure AD DS 中创建和管理自定义 OU 结构时有一些限制和注意事项：

* 若要创建自定义 Ou，用户必须是*AAD DC Administrators*组的成员。
* 创建自定义 OU 的用户将被授予对该 OU 的管理权限（完全控制权限），并且是资源所有者。
    * 默认情况下， *AAD DC 管理员*组还对自定义 OU 具有完全控制。
* 将创建*AADDC 用户*的默认 OU，其中包含 Azure AD 租户中所有已同步的用户帐户。
    * 不能将用户或组从 " *AADDC 用户*" OU 移到创建的自定义 ou。 只能将 Azure AD DS 托管域中创建的用户帐户或资源移动到自定义 Ou 中。
* 在自定义 Ou 下创建的用户帐户、组、服务帐户和计算机对象在 Azure AD 租户中不可用。
    * 这些对象不会使用 Azure AD 图形 API 或 Azure AD UI 中显示;它们仅在 Azure AD DS 托管域中可用。

## <a name="create-a-custom-ou"></a>创建自定义 OU

若要创建自定义 OU，请使用已加入域的 VM 中的 Active Directory 管理工具。 使用 Active Directory 管理中心可以查看、编辑和创建 Azure AD DS 托管域中的资源，包括 Ou。

> [!NOTE]
> 若要在 Azure AD DS 托管域中创建自定义 OU，你必须登录到作为*AAD DC 管理员*组成员的用户帐户。

1. 登录到管理 VM。 有关如何使用 Azure 门户进行连接的步骤，请参阅[连接到 Windows SERVER VM][connect-windows-server-vm]。
1. 从 "开始" 屏幕中，选择 "**管理工具**"。 其中显示了在[创建管理 VM][tutorial-create-management-vm]教程中安装的可用管理工具列表。
1. 若要创建和管理 Ou，请从管理工具列表中选择 " **Active Directory 管理中心**"。
1. 在左窗格中，选择 Azure AD DS 托管域，如*contoso.com*。 将显示现有 Ou 和资源的列表：

    ![在 Active Directory 管理中心中选择 Azure AD DS 托管域](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. "**任务**" 窗格显示在 Active Directory 管理中心右侧。 在域下（如*contoso.com*），选择 "**新建 > 组织单位**"。

    ![选择用于在 Active Directory 管理中心中创建新 OU 的选项](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. 在 "**创建组织单位**" 对话框中，指定新 OU 的**名称**，如*MyCustomOu*。 提供 OU 的简短说明，例如*服务帐户的自定义 OU*。 如果需要，还可以为 OU 设置 "**管理者**" 字段。 若要创建自定义 OU，请选择 **"确定"** 。

    ![从 Active Directory 管理中心创建自定义 OU](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. 返回 Active Directory 管理中心，自定义 OU 现在已列出并可供使用：

    ![可在 Active Directory 管理中心中使用的自定义 OU](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>后续步骤

有关使用管理工具或创建和使用服务帐户的详细信息，请参阅以下文章：

* [Active Directory 管理中心：入门](https://technet.microsoft.com/library/dd560651.aspx)
* [服务帐户分步指南](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
