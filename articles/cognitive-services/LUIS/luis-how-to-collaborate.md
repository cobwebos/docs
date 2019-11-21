---
title: Collaborate with others - LUIS
titleSuffix: Azure Cognitive Services
description: An app owner can add contributors to the authoring resource. These contributors can modify the model, train, and publish the app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: efeb8b28ef2b8eec480fcf5090e0173d6451deef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225516"
---
# <a name="add-contributors-to-your-app"></a>Add contributors to your app

An app owner can add contributors to apps. 这些协作者可以修改模型，训练并发布应用。 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Add contributor to Azure authoring resource

The following procedure is for all users that have **migrated** to use the Azure authoring resource.

You have migrated if your LUIS authoring experience is tied to an Authoring resource on the **Manage -> Azure resources** page in the LUIS portal.

1. In the Azure portal, find the Language Understanding (LUIS) authoring resource. It has the type `LUIS.Authoring`.
1. On this resource's **Access Control (IAM)** page, select **+Add** then select **Add role assignment**.

    ![In Azure portal, add role assignment on authoring resource.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. In the **Add role assignment** window, select the **Role** of Contributor. In the **Assign access to** option, select **Azure AD user, group, or service principal**. In the **Select** option, enter the user's email address. If the user is known by more than 1 email address for the same domain, make sure the enter the _primary_ email account.

    ![Add user's email to the contributor role for Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    When the user's email is found, select the account and select **Save**. 

    If you have trouble with this role assignment, review [Azure role assignments](../../role-based-access-control/role-assignments-portal.md) and [Azure access control troubleshooting](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Add collaborator to LUIS app

The following procedure is for all users that have **not migrated** to use the Azure authoring resource.

You have not migrated if your LUIS authoring experience is not tied to an Authoring resource on the **Manage -> Azure resources** page in the LUIS portal.

一个应用只有一个作者、所有者，但可以有许多协作者。 若要允许协作者编辑你的 LUIS 应用，必须将他们用来访问 LUIS 门户的电子邮件添加到协作者列表中。 在添加后，应用将显示在他们的 LUIS 门户中。

1. 从右上角的菜单中选择“管理”，然后在左侧菜单中选择“协作者”。

1. 从工具栏中选择“添加协作者”。

1. 输入协作者用来登录到 LUIS 门户的电子邮件地址。

    ![添加协作者的电子邮件地址](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Users with multiple emails 

If you add contributors/collaborators to a LUIS app, you are specifying the exact email address. While Azure Active Directory (Azure AD) allows a single user to have more than one email account used interchangeably, LUIS requires the user to sign in with the email address specified when adding the contributor/collaborator.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory 资源

如果你在组织中使用了 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD)，则在用户希望使用语言理解 (LUIS) 时，LUIS 需要有权访问用户的访问权限相关信息。 LUIS 需要的资源是最少的。 

尝试使用已获得管理员同意或不需要管理员同意的帐户进行登录时，你将看到详细说明，例如管理员同意：

* 允许你使用组织帐户登录到应用并让应用读取你的配置文件。 它还允许应用读取基本的公司信息。 这将授权 LUIS 读取基本的配置文件数据，例如用户 ID、电子邮件、姓名
* 允许应用查看和更新你的数据，即使你当前未使用应用。 此权限是刷新用户的访问令牌时所必需的。


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租户用户

LUIS 使用标准的 Azure Active Directory (Azure AD) 许可流程。 

租户管理员直接处理需要访问权限才能在 Azure AD 中使用 LUIS 的用户。 

* 用户首先要登录 LUIS，此时看到需要管理员批准的弹出对话框。 继续操作之前，用户需联系租户管理员。 
* 然后，租户管理员登录 LUIS，并看到一个“同意流”弹出对话框。 管理员需在此对话框中向用户授予权限。 管理员接受权限后，用户才能够继续使用 LUIS。 如果租户管理员不登录 LUIS，还可访问 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications)部分，如以下屏幕截图中所示。 请注意，列表已经过筛选，仅显示包括名称 `LUIS` 的项。

![应用网站授予的 Azure Active Directory 权限](./media/luis-how-to-collaborate/tenant-permissions.png)

如果租户管理员只希望某些用户使用 LUIS，则有几种可能的解决方案：
* 给予“管理员同意”（同意 Azure AD 的所有用户），但随后在“企业应用程序属性”下将“需要进行用户分配”设置为“是”，最后仅将所需用户分配/添加到应用程序。 使用此方法，管理员仍然向应用提供“管理员同意”，但是，可以控制可以访问应用的用户。
* 第二种解决方案是使用 [Azure AD 图形 API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) 向每个特定用户提供同意。 

详细了解 Azure Active Directory 用户和同意： 
* [将应用限制为](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)供一组用户使用

## <a name="next-steps"></a>后续步骤

* Learn [how to use versions](luis-how-to-manage-versions.md) to control your app life cycle.
* Understand the concepts including the [authoring resource](luis-concept-keys.md#authoring-key) and [contributors](luis-concept-keys.md#contributions-from-other-authors) on that resource.
* Learn [how to create](luis-how-to-azure-subscription.md) authoring and runtime resources
* Migrate to the new [authoring resource](luis-migration-authoring.md) 
