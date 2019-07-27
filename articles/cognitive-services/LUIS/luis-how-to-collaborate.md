---
title: 与他人协作-LUIS
titleSuffix: Azure Cognitive Services
description: 应用所有者可以向应用添加协作者。 这些协作者可以修改模型，训练并发布应用。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/12/2019
ms.author: diberry
ms.openlocfilehash: 004bb979adcc0d7ba4860a917242e2219d7f1379
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560468"
---
# <a name="how-to-manage-authors-and-collaborators"></a>如何管理作者和协作者 

应用所有者可以向应用添加协作者。 这些协作者可以修改模型，训练并发布应用。 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>添加协作者

一个应用只有一个作者、所有者，但可以有许多协作者。 若要允许协作者编辑你的 LUIS 应用，必须将他们用来访问 LUIS 门户的电子邮件添加到协作者列表中。 在添加后，应用将显示在他们的 LUIS 门户中。

1. 从右上角的菜单中选择“管理”，然后在左侧菜单中选择“协作者”。

2. 从工具栏中选择“添加协作者”。

    [![添加协作者](./media/luis-how-to-collaborate/add-collaborator.png "添加协作者")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. 输入协作者用来登录到 LUIS 门户的电子邮件地址。

    ![添加协作者的电子邮件地址](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>转让所有权

虽然 LUIS 目前不支持转让所有权，但你可以导出应用，而其他 LUIS 用户可以导入该应用。 两个应用程序之间的 LUIS 分数可能存在细微差别。 

## <a name="azure-active-directory-resources"></a>Azure Active Directory 资源

如果你在组织中使用了 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD)，则在用户希望使用语言理解 (LUIS) 时，LUIS 需要有权访问用户的访问权限相关信息。 LUIS 需要的资源是最少的。 

尝试使用已获得管理员同意或不需要管理员同意的帐户进行登录时，你将看到详细说明，例如管理员同意：

* 允许你使用组织帐户登录到应用并让应用读取你的配置文件。 它还允许应用读取基本的公司信息。 这将授权 LUIS 读取基本的配置文件数据，例如用户 ID、电子邮件、姓名
* 允许应用查看和更新你的数据，即使你当前未使用应用。 此权限是刷新用户的访问令牌时所必需的。


## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租户用户

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

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>具有多个协作者电子邮件的用户帐户

如果将协作者添加到 LUIS 应用，则要指定协作者将 LUIS 用作协作者所需的确切电子邮件地址。 虽然 Azure Active Directory (Azure AD) 允许单名用户交替使用多个电子邮件帐户，但 LUIS 要求用户使用协作者列表中指定的电子邮件地址登录。

