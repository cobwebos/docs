---
title: 更新你的组信息从我的应用门户-Azure Active Directory |Microsoft Docs
description: 了解如何查看和更新你与组相关的信息，包括查看你拥有的组、 创建新组，查看到您已经是成员，并加入任何组的组名称尚未的一部分。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578ca5fa1de338a92280e2dc7cc252fb616e2111
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706692"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>更新你的组信息从我的应用门户
可以在基于 Web 的“我的应用”门户中使用工作或学校帐户，  以便查看并启动组织的许多基于云的应用、更新你的部分配置文件和帐户信息、查看**组**信息，以及为应用和组执行“访问评审”。  如果无法访问“我的应用”门户，则必须联系支持人员以获取相关权限。 

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)中查找有关如何设置和管理基于云的应用的详细信息。

## <a name="view-your-groups-information"></a>查看组信息
如果管理员已授予权限以查看**组**磁贴中，你可以：

- **作为组成员。** 查看详细信息，或保留的任何组。

- **为组的所有者。** 查看详细信息、 创建新的组、 添加或删除成员，或删除你的组。

### <a name="to-view-your-groups-information"></a>若要查看组信息

1.  登录到工作或学校帐户。

2.  打开 web 浏览器并转到 https://myapps.microsoft.com ，或使用由你的组织提供的链接。 例如，你可能会定向到自定义页面为你的组织，例如 https://myapps.microsoft.com/contoso.com 。

    **应用**页出现后，显示所有基于云的应用，由你的组织拥有和可供你使用。

    ![我的应用门户中的应用程序页](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. 选择**组**磁贴，查看你与组相关的信息。

    ![拥有两个组页面和成员组](media/my-apps-portal/my-apps-portal-groups-page.png)

4. 根据你的权限，您可以使用**组**到页上：

    - **查看你拥有的组。** 查看有关任何信息进行分组，从组织中拥有**我拥有的组**区域。 选择特定的组名称提供更多详细信息组，包括组类型、 成员数、 联接策略和活动成员的列表。

    - **创建新的组。** 与你创建新的组，以从所有者**我拥有的组**区域。 有关特定步骤，请参阅[创建新的组](#create-a-new-group)本文的部分。

    - **编辑现有的组。** 编辑任何你自己的组的详细信息。 有关特定步骤，请参阅[编辑现有的组](#edit-an-existing-group)本文的部分。

    - **添加或删除成员。** 添加或删除自己拥有的组的成员。 有关特定步骤，请参阅[添加或删除成员](#add-or-remove-a-member)本文的部分。

    - **续订 Office 365 组。** 如果你的组织允许它，可以续订 Office 365 组。 有关特定步骤，请参阅[续订 Office 365 组](#renew-an-office-365-group)本文的部分。 

    - **删除的组。** 删除任何你拥有的组。 有关特定步骤，请参阅[删除组](#delete-a-group)本文的部分。

    - **查看您的一部分的组。** 查看您的任何组中的成员的名称**我加入的组**区域。 选择特定的组名称提供更多详细信息组，包括组类型、 成员数、 联接策略和活动成员的列表。

    - **加入组。** 加入现有组，为其尚未成员，从**我加入的组**区域。 有关特定步骤，请参阅[加入现有组](#join-an-existing-group)。

## <a name="create-a-new-group"></a>创建一个新组
1. 上**组**页上，选择**创建一个组**从**我拥有的组**区域。

    **创建组**框会显示。

    ![创建分组框](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. 输入所需信息：

    - **组类型：**
        
        - **安全性。** 用于管理成员和一组用户对共享资源的计算机访问权限。 例如，可以为某一特定安全策略创建一个安全组。 以此方式可以一次性为所有成员赋予一组权限，而不必单独地向每个成员添加权限。

        - **Office 365.** 通过向成员赋予对共享邮箱、日历、文件、SharePoint 站点等的访问权限，提供协作机会。 你也可以通过此选项向组织外部的人员赋予对组的访问权限。

    - **组名。** 添加组名称，可以使用容易记住以及具有某种意义的名称作为组名称。

    - **组描述 （可选）。** 向组添加说明（可选操作）。

    - **组策略。** 选择要允许所有人加入组，或若要仅允许将成员添加的组的所有者。

3. 选择“创建”  。

    与你作为所有者创建新组并出现在你**我拥有的组**列表。 由于你是所有者，此组也将出现在**我加入的组**列表。

## <a name="edit-an-existing-group"></a>编辑现有的组
创建组后，可以编辑其详细信息，包括更新的任何现有信息。

### <a name="to-edit-your-details"></a>若要编辑你的详细信息
1. 选择你想要编辑的组**组**页上，并选择**编辑详细信息**上 *&lt;group_name&gt;* 页。

    **编辑详细信息**框出现，可以更新添加最初创建组时的信息。

2. 进行所有更改，并选择**更新**。

## <a name="add-or-remove-a-member"></a>添加或删除成员
可以添加或删除任何你自己的组的成员。

### <a name="to-add-or-remove-a-member"></a>若要添加或删除成员
1. 选择你想要将成员添加到，然后选择的组 **+** 上 *&lt;group_name&gt;* 页。

    ![添加组成员具有 + 登录突出显示](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. 搜索你想要添加，成员从**将成员添加**框，并选择**添加**。

    ![将成员中，添加与要添加的新成员](media/my-apps-portal/my-apps-portal-add-member-page.png)

    邀请发送到新的成员，若要开始访问组织的应用。

3. 如果错误地添加了成员，或者如果成员已从组织离职，可以通过选择中删除该成员**删除成员**上的成员的名称旁边 *&lt;group_name&gt;* 页。

    ![删除成员，但突出显示的删除链接](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>续订 Office 365 组
如果你的组织允许它，可以续订 Office 365 组，扩展到期日期。

### <a name="to-renew-a-group"></a>若要续订组
1. 选择你想要续订，然后选择 Office 365 组**续订组**。

    ![续订 Office 365 组，扩展的到期日期](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. 单击**确定**关闭确认消息。

    刷新页面后，你将看到更新后**上次续订**并**组过期**日期。

## <a name="delete-a-group"></a>删除组
您可以在任何时候删除任何你自己的组。 但是，如果错误地删除了某个组你必须创建它并再次添加成员。

### <a name="to-delete-the-group"></a>若要删除组
1. 选择你想要永久删除，然后选择的组**删除组**上 *&lt;group_name&gt;* 页。

    ![< 组名 > 页与突出显示删除组链接](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. 选择**是**上确认消息。

    永久删除组。 
    
## <a name="join-an-existing-group"></a>加入现有组
可以加入现有组从**组**页。

### <a name="to-join-or-leave-a-group"></a>加入或离开组

1. 上**组**页上，选择**加入组**从**我加入的组**区域。

    **加入组**页将出现。

    ![加入组页上，突出显示的联接组按钮](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. 上**加入组**页上，选择你想要加入，查看相关联的组的详细信息，并组将变为可用，如果选择的组的名称**加入组**。

    如果组需要组所有者批准的成员身份，您将需要输入有关为何需要加入组的业务理由，然后选择**请求**。 如果组不需要获得批准，您要立即添加为成员的组显示在你**我加入的组**列表。

3. 如果错误地加入某个组，或者如果不再需要为它的一部分，则可以选择中的组名称**加入组**页上，并选择**退出组**。

    ![加入组页上，突出显示的保留组按钮](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>后续步骤

- [访问和我的应用门户上使用应用](my-apps-portal-end-user-access.md)。

- [更改个人资料信息](my-apps-portal-end-user-update-profile.md)。

- [执行你自己的访问评审](my-apps-portal-end-user-access-reviews.md)。