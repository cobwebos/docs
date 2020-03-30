---
title: 管理组织对应用&组的访问 - Azure AD
description: 了解如何执行访问审核，以从"我的应用"门户管理组织的应用和组的安全访问权限。
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
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062366"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>从"我的应用"门户执行访问审核

您可以将工作或学校帐户与基于 Web**的"我的应用"** 门户一起用于应用和组的访问审核。 访问审核可帮助您管理过时的访问或更改访问要求，并确保这些访问被审查和更新。

如果无法访问“我的应用”门户，请联系支持人员以获取相关权限。****

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于“我的应用”用户。**** 如果你是管理员，可以在[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)中查找有关如何设置和管理基于云的应用的详细信息。

## <a name="manage-access-reviews"></a>管理访问评审

如果管理员已授予您执行自己的访问评论的权限，则可以从 **"我的应用"** 门户页面上的 **"访问"评论**磁贴管理组或应用访问权限。

>[!Note]
>如果您没有看到 **"访问审核"** 磁贴，则意味着您没有执行访问审核的权限，或者您没有任何待决审核等待您的批准。 如果您认为您应该有权访问磁贴，请联系您的帮助台寻求帮助。

## <a name="to-perform-your-access-reviews"></a>执行访问审核

1. 登录到工作或学校帐户。

2. 打开 Web 浏览器并转到https://myapps.microsoft.com，或使用组织提供的链接。 例如，您可能会被定向到组织的自定义页面，例如https://myapps.microsoft.com/contoso.com。

    将显示 **"应用"** 页面，显示组织拥有并可供您使用的所有基于云的应用。

    !["我的应用"门户中的应用页面](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 选择 **"访问审核"** 磁贴以查看等待您的批准的访问审核列表。

    ![访问审核页面，包含组织的挂起访问审核](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 选择 **"开始审阅"** 以开始访问审核。

5. 查看您的访问权限并确定是否仍有必要。

    ![访问审阅页面，显示审核详细信息](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >如果您是管理员，并且允许查看组织对组和应用的访问权限，您将看到其他页面。 有关查看组织的组或应用的详细信息，请参阅[在 Azure AD 访问审核中查看对组或应用程序的访问权限](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。

6. 选择 **"是**"可保留您的访问权限或 **"否**"以删除您的访问权限。

    如果选择 **"是**"，则可能需要在 **"原因"** 框中指定对齐方式。

    ![访问审阅页面，显示带有示例文本的"原因"框](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 选择“提交”****。

    您的访问审核已完成，您将返回到 **"我的应用"** 门户。

    >[!Note]
    >您可以随时更改访问权限，直到访问审核期结束。 如果删除了对应用或组的访问，则不会立即删除。 当访问审阅期结束时或管理员关闭审核时，将发生删除。

## <a name="next-steps"></a>后续步骤

- [访问和使用"我的应用"门户中的应用](my-apps-portal-end-user-access.md)
- [更改个人资料信息](my-apps-portal-end-user-update-profile.md)
- [查看和更新与组相关的信息](my-apps-portal-end-user-groups.md)
