---
title: 与 Azure 中的 LUIS 应用上的其他参与者进行协作 | Microsoft Docs
description: 了解如何与语言理解 (LUIS) 应用程序上的其他参与者协作。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397771"
---
# <a name="how-to-manage-authors-and-collaborators"></a>如何管理作者和协作者 

你可以在 LUIS 应用上与其他人协作。 

## <a name="owner-and-collaborators"></a>所有者和协作者

一个应用只有一个作者、所有者，但可以有许多协作者。 

## <a name="add-collaborator"></a>添加协作者

要允许协作者编辑 LUIS 应用，请在 LUIS 应用的“设置”页面上输入协作者的电子邮件，然后单击“添加协作者”。 协作者可以在你使用 LUIS 应用的同时登录和编辑你的 LUIS 应用。

![添加协作者](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>转让所有权

虽然 LUIS 目前不支持转让所有权，但你可以导出应用，而其他 LUIS 用户可以导入该应用。 两个应用程序之间的 LUIS 分数可能存在细微差别。 

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租户用户

LUIS 使用标准的 Azure Active Directory (Azure AD) 许可流程。 

租户管理员直接处理需要访问权限才能在 Azure AD 中使用 LUIS 的用户。 

用户首先要登录 LUIS，此时看到需要管理员批准的弹出对话框。 继续操作之前，用户需联系租户管理员。 

然后，租户管理员登录 LUIS，并看到一个“同意流”弹出对话框。 管理员需在此对话框中向用户授予权限。 管理员接受权限后，用户才能够继续使用 LUIS。

如果租户管理员不登录 LUIS，还可访问 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications)部分。 

![应用网站授予的 Azure Active Directory 权限](./media/luis-how-to-account-settings/tenant-permissions.png)

如果租户管理员只希望某些用户使用 LUIS，请参阅此[标识博客](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)。

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>具有多个协作者电子邮件的用户帐户

如果将协作者添加到 LUIS 应用，则要指定协作者将 LUIS 用作协作者所需的确切电子邮件地址。 虽然 Azure Active Directory (Azure AD) 允许单名用户交替使用多个电子邮件帐户，但 LUIS 要求用户使用协作者列表中指定的电子邮件地址登录。