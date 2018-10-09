---
title: 与 LUIS 应用的其他参与者进行协作
titleSuffix: Azure Cognitive Services
description: 应用所有者可以向应用添加协作者。 这些协作者可以修改模型，训练并发布应用。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: b7d108bfc0c4283e7856b93daba3f4f92af4cc5b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042187"
---
# <a name="how-to-manage-authors-and-collaborators"></a>如何管理作者和协作者 

应用所有者可以向应用添加协作者。 这些协作者可以修改模型，训练并发布应用。 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>添加协作者

一个应用只有一个作者、所有者，但可以有许多协作者。 若要允许协作者编辑你的 LUIS 应用，必须将他们用来访问 LUIS 门户的电子邮件添加到协作者列表中。 在添加后，应用将显示在他们的 LUIS 门户中。

1. 从右上角的菜单中选择“管理”，然后在左侧菜单中选择“协作者”。

2. 从工具栏中选择“添加协作者”。

    [![](./media/luis-how-to-collaborate/add-collaborator.png "添加协作者")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. 输入协作者用来登录到 LUIS 门户的电子邮件地址。

    ![添加协作者的电子邮件地址](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>转让所有权

虽然 LUIS 目前不支持转让所有权，但你可以导出应用，而其他 LUIS 用户可以导入该应用。 两个应用程序之间的 LUIS 分数可能存在细微差别。 

## <a name="azure-active-directory-resources"></a>Azure Active Directory 资源

如果你在组织中使用了 Azure Active Directory (Azure AD)，则在用户希望使用 LUIS 时，LUIS 需要有权访问用户的相关信息。 LUIS 需要的资源是最少的。 

尝试使用已获得管理员同意或不需要管理员同意的帐户进行登录时，你将看到详细说明，例如管理员同意：

* 允许你使用组织帐户登录到应用并让应用读取你的配置文件。 它还允许应用读取基本的公司信息。
* 允许应用查看和更新你的数据，即使你当前未使用应用。

第一个权限授权 LUIS 读取基本的配置文件数据，例如用户 ID、电子邮件、姓名。 第二个权限是刷新用户的访问令牌时所必需的。

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租户用户

LUIS 使用标准的 Azure Active Directory (Azure AD) 许可流程。 

租户管理员直接处理需要访问权限才能在 Azure AD 中使用 LUIS 的用户。 

用户首先要登录 LUIS，此时看到需要管理员批准的弹出对话框。 继续操作之前，用户需联系租户管理员。 

然后，租户管理员登录 LUIS，并看到一个“同意流”弹出对话框。 管理员需在此对话框中向用户授予权限。 管理员接受权限后，用户才能够继续使用 LUIS。

如果租户管理员不登录 LUIS，还可访问 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications)部分。 

![应用网站授予的 Azure Active Directory 权限](./media/luis-how-to-collaborate/tenant-permissions.png)

如果租户管理员只希望某些用户使用 LUIS，请参阅此[标识博客](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)。

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>具有多个协作者电子邮件的用户帐户

如果将协作者添加到 LUIS 应用，则要指定协作者将 LUIS 用作协作者所需的确切电子邮件地址。 虽然 Azure Active Directory (Azure AD) 允许单名用户交替使用多个电子邮件帐户，但 LUIS 要求用户使用协作者列表中指定的电子邮件地址登录。

