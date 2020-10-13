---
title: 在 Azure AD 域服务中创建组织单位 (OU) | Microsoft Docs'
description: 了解如何在 Azure AD 域服务托管域中创建和管理自定义组织单位 (OU)。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: df8d32fb38d9ea6750ef47651e4f660428fd1fbe
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960994"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>在 Azure Active Directory 域服务托管域中创建组织单位 (OU)

Active Directory 域服务 (AD DS) 托管的域中的组织单位 (OU) 使你能够对用户帐户、服务帐户或计算机帐户等对象进行逻辑分组。 然后，你可以将管理员分配到特定的 OU，并应用组策略来强制实施目标配置设置。

Azure AD DS 托管域包括以下两个内置 OU：

* AADDC 计算机 - 容器包含已加入托管域的所有计算机的计算机对象。
* AADDC 用户 - 包含从 Azure AD 租户同步的用户和组。

创建和运行使用 Azure AD DS 的工作负载时，可能需要为应用程序创建服务帐户，用于为其进行身份验证。 为了组织这些服务帐户，通常在托管域中创建一个自定义 OU，然后在该 OU 中创建服务帐户。

在混合环境中，在本地 AD DS 环境中创建的 OU 不会与托管域同步。 托管域使用平面 OU 结构。 所有用户帐户和组都存储在“AADDC 用户”容器中，尽管它们是从不同的本地域或林进行同步，即使你在其中配置了分层 OU 结构。

本文说明如何在托管域中创建 OU。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
* 已加入 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建管理 VM][tutorial-create-management-vm] 的教程。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="custom-ou-considerations-and-limitations"></a>自定义 OU 注意事项和限制

在托管域中创建自定义 OU 时，在用户管理和组策略应用方面可以获得额外的管理上的灵活性。 与本地 AD DS 环境相比，在托管域中创建和管理自定义 OU 结构时存在一些限制和注意事项：

* 若要创建自定义 OU，用户必须是“AAD DC 管理员”组的成员。
* 创建自定义 OU 的用户会获得对该 OU 的管理权限（完全控制），并且是资源所有者。
    * 默认情况下，AAD DC 管理员组也能充分控制自定义 OU。
* 将为 AADDC 用户创建默认 OU，其中包含 Azure AD 租户中所有已同步的用户帐户。
    * 无法将用户或组从“AADDC 用户”OU 移到创建的自定义 OU。 只有在托管域中创建的用户帐户或资源才能移动到自定义 OU 中。
* 在自定义 OU 下创建的用户帐户、组、服务帐户和计算机对象无法在 Azure AD 租户中使用。
    * 这些对象不会在使用 Microsoft Graph API 时或在 Azure AD UI 中显示；它们仅在托管域中可用。

## <a name="create-a-custom-ou"></a>创建自定义 OU

若要创建自定义 OU，应使用已加入域的 VM 中的 Active Directory 管理工具。 在 Active Directory 管理中心可查看、编辑和创建托管域（包括 OU）中的资源。

> [!NOTE]
> 若要在托管域中创建自定义 OU，需要使用 AAD DC 管理员组成员的用户帐户进行登录。

1. 登录到管理 VM。 有关如何使用 Azure 门户进行连接的步骤，请参阅[连接到 Windows Server VM][connect-windows-server-vm]。
1. 在“开始”屏幕中选择“管理工具”。 其中显示了可用管理工具列表，这些工具是在教程[创建管理 VM][tutorial-create-management-vm] 中安装的。
1. 若要创建和管理 OU，请从管理工具列表中选择“Active Directory 管理中心”。
1. 在左窗格中，选择托管域，例如 aaddscontoso.com。 现有 OU 和资源列表如下所示：

    ![在 Active Directory 管理中心中选择你的托管域](./media/create-ou/create-ou-adac-overview.png)

1. “任务”窗格显示在 Active Directory 管理中心的右侧。 在域下（例如 aaddscontoso.com），选择“新建”>“组织单位”。

    ![选择在 Active Directory 管理中心中创建新 OU 的选项](./media/create-ou/create-ou-adac-new-ou.png)

1. 在“创建组织单位”对话框中，指定新 OU 的名称（例如 MyCustomOu）。  提供 OU 的简短描述，例如“服务帐户的自定义 OU”。 如果需要，还可以为 OU 设置“管理方”字段。 若要创建自定义 OU，请选择“确定”。

    ![从 Active Directory 管理中心创建自定义 OU](./media/create-ou/create-ou-dialog.png)

1. 返回 Active Directory 管理中心，自定义 OU 现已列出并可供使用：

    ![可在 Active Directory 管理中心使用的自定义 OU](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>后续步骤

有关如何使用管理工具或创建和使用服务帐户的详细信息，请参阅以下文章：

* [Active Directory 管理中心：入门](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [服务帐户分步指南](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm