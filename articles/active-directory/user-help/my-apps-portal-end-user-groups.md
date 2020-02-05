---
title: 更新 "我的应用" 门户上的组信息-Azure AD
description: 了解如何查看和更新与组相关的信息，包括查看你拥有的组、创建新组、查看已是其成员的组，以及联接你还不属于的所有组。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 00639462fce4e67561b8e50609264d4423872c82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022290"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>更新 "我的应用" 门户上的组信息

可以在基于 Web 的“我的应用”门户中使用工作或学校帐户，以便查看并启动组织的许多基于云的应用、更新你的部分配置文件和帐户信息、查看**组**信息，以及为应用和组执行“访问评审”。 如果无法访问“我的应用”门户，则必须联系支持人员以获取相关权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)中查找有关如何设置和管理基于云的应用的详细信息。

## <a name="view-your-groups-information"></a>查看组信息

如果管理员已授予您查看 "**组**" 磁贴的权限，则可以：

- **作为组成员。** 查看详细信息或离开任何组。

- **作为组所有者。** 查看详细信息，创建新组，添加或删除成员，或删除组。

### <a name="to-view-your-groups-information"></a>查看组信息

1. 登录到工作或学校帐户。

2. 打开 web 浏览器并中转到 https://myapps.microsoft.com ，或使用组织提供的链接。 例如，你可能会被定向到组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。

    此时将显示 "**应用**" 页，其中显示组织所拥有的所有基于云的应用程序，并可供你使用。

    !["我的应用" 门户中的 "应用" 页](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. 选择 "**组**" 磁贴以查看与组相关的信息。

    ![具有拥有和成员组的组页](media/my-apps-portal/my-apps-portal-groups-page.png)

4. 根据您的权限，您可以使用 "**组**" 页执行以下操作：

    - **查看您拥有的组。** 从 "**我拥有的组**" 区域中查看有关你的组织内拥有的任何组的信息。 选择特定的组名可提供有关组的更多详细信息，包括组类型、成员数、联接策略和活动成员列表。

    - **创建新组。** 创建一个新组，并将其作为 "**我拥有的组**" 区域中的所有者。 有关具体步骤，请参阅本文的[创建新组](#create-a-new-group)部分。

    - **编辑现有组。** 编辑任何自己的组的详细信息。 有关具体步骤，请参阅本文的[编辑现有组](#edit-an-existing-group)部分。

    - **添加或删除成员。** 添加或删除您拥有的组的成员。 有关具体步骤，请参阅本文中的[添加或删除成员](#add-or-remove-a-member)部分。

    - **续订 Office 365 组。** 如果你的组织允许，你可以续订你的 Office 365 组。 有关具体步骤，请参阅本文的[续订 Office 365 组](#renew-an-office-365-group)部分。 

    - **删除组。** 删除你拥有的所有组。 有关具体步骤，请参阅本文的[删除组](#delete-a-group)部分。

    - **查看你所属的组。** 从 "我所在的组" 区域**中**查看您所属的任何组的名称。 选择特定的组名可提供有关组的更多详细信息，包括组类型、成员数、联接策略和活动成员列表。

    - **加入组。** 从 "我所在的组" 区域**中**，加入现有组，但你还不是其成员。 有关具体步骤，请参阅[加入现有组](#join-an-existing-group)。

## <a name="create-a-new-group"></a>创建一个新组

1. 在 "**组**" 页上，从 "**我拥有的组**" 区域中选择 "**创建组**"。

    此时将显示 "**创建组**" 框。

    ![创建组框](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. 输入所需信息：

    - **组类型：**

        - **安全性。** 用于管理成员和一组用户对共享资源的计算机访问权限。 例如，可以为某一特定安全策略创建一个安全组。 以此方式可以一次性为所有成员赋予一组权限，而不必单独地向每个成员添加权限。

        - **Office 365。** 通过向成员赋予对共享邮箱、日历、文件、SharePoint 站点等的访问权限，提供协作机会。 你也可以通过此选项向组织外部的人员赋予对组的访问权限。

    - **组名。** 添加组名称，可以使用容易记住以及具有某种意义的名称作为组名称。

    - **组说明（可选）。** 向组添加说明（可选操作）。

    - **组策略。** 选择 "允许每个人加入组" 或 "仅允许组的所有者添加成员"。

3. 选择“创建”。

    新组将作为所有者创建，并显示在 "**我的组**" 列表中。 因为你是所有者，所以此组还会显示在 "**我的组**" 列表中。

## <a name="edit-an-existing-group"></a>编辑现有组

创建组后，可以编辑其详细信息，包括更新任何现有信息。

1. 从 "**组**" 页中选择要编辑的组，然后在 " *&lt;group_name&gt;* " 页上选择 "**编辑详细信息**"。

    此时将显示 "**编辑详细信息**" 框，您可以在最初创建组时更新所添加的信息。

2. 进行所有更改，然后选择 "**更新**"。

## <a name="add-or-remove-a-member"></a>添加或删除成员

你可以添加或删除任何你拥有的组的成员。

1. 选择要向其中添加成员的组，然后在 " *&lt;group_name&gt;* " 页上选择 " **+** "。

    ![添加组成员，其中突出显示了 + 符号](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. 从 "**添加成员**" 框中搜索要添加的成员，然后选择 "**添加**"。

    ![添加成员框，包含要添加的新成员](media/my-apps-portal/my-apps-portal-add-member-page.png)

    邀请发送到新成员，开始访问组织的应用。

3. 如果你错误地添加了成员，或者成员已离开你的组织，则可以通过选择 " *&lt;group_name&gt;* " 页上成员名称旁的 "**删除成员**" 来删除该成员。

    ![删除具有突出显示的删除链接的成员](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>续订 Office 365 组

如果你的组织允许，你可以续订 Office 365 组，延长你的到期日期。

1. 选择要续订的 Office 365 组，然后选择 "**续订组**"。

    ![续订 Office 365 组，延长过期日期](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. 单击 **"确定"** 以关闭确认消息。

    刷新页面后，你将看到更新的**上次续订**日期和**组到期**日期。

## <a name="delete-a-group"></a>删除组

你可以随时删除任何自己的组。 但是，如果你错误地删除了某个组，则必须创建它并再次添加成员。

1. 选择要永久删除的组，然后在 " *&lt;group_name"&gt;* "页上选择"**删除组**"。

    ![突出显示 "删除组" 链接 < Group_name > "页](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. 在确认消息上选择 **"是"** 。

    该组将被永久删除。

## <a name="join-an-existing-group"></a>加入现有组

你可以在 "**组**" 页中加入或离开现有组。

1. 在 "**组**" 页上，从 "**我所在的组**" 区域中选择 "**加入组**"。

    此时将显示 "**加入组**" 页。

    ![突出显示 "加入组" 按钮的 "联接组" 页](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. 在 "**加入组**" 页上，选择要加入的组的名称，查看关联的组详细信息，然后，如果该组可用，请选择 "**加入组**"。

    如果该组需要组所有者批准成员身份，则系统会要求您输入业务理由，要求您加入组，然后选择 "**请求**"。 如果组不需要审批，则会立即将你添加为成员，组将出现在 **"我的组**" 列表中。

3. 如果错误地加入了组，或者不再需要作为组的一部分，则可以从 "**联接组**" 页中选择组名称，然后选择 "**离开组**"。

    !["加入组" 页，突出显示 "退出组" 按钮](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>后续步骤

- [访问和使用 "我的应用" 门户上的应用](my-apps-portal-end-user-access.md)。

- [更改你的配置文件信息](my-apps-portal-end-user-update-profile.md)。

- [执行你自己的访问评审](my-apps-portal-end-user-access-reviews.md)。
