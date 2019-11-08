---
title: 从 "我的应用" 门户执行访问评审-Azure AD
description: 了解如何查看和管理组织的应用和组的安全访问权限。
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
ms.openlocfilehash: bf803027aac7c62ea500d1af104ae3234fadf8b5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73804045"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>从 "我的应用" 门户执行访问评审

可以在基于 Web 的“我的应用”门户中使用工作或学校帐户，以便查看并启动组织的许多基于云的应用、更新你的部分配置文件和帐户信息、查看**组**信息，以及为应用和组执行“访问评审”。 如果无法访问“我的应用”门户，则必须联系支持人员以获取相关权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)中查找有关如何设置和管理基于云的应用的详细信息。

## <a name="manage-access-reviews"></a>管理访问评审

如果管理员已授予你执行自己的访问评审的权限，则可以在 "**我的应用**" 门户页上的 "**访问评审**" 磁贴中管理组或应用的访问权限。

>[!Note]
>如果看不到 "**访问评审**" 磁贴，这意味着无权执行访问评审，或者没有任何等待审批的挂起评审。 如果你认为你应该有权访问磁贴，请与支持人员联系以获得帮助。

### <a name="to-perform-your-access-reviews"></a>执行访问评审

1. 登录到工作或学校帐户。

2. 打开 web 浏览器并中转到 https://myapps.microsoft.com，或使用组织提供的链接。 例如，你可能会被定向到组织的自定义页面，如 https://myapps.microsoft.com/contoso.com。

    此时将显示 "**应用**" 页，其中显示组织所拥有的所有基于云的应用程序，并可供你使用。

    !["我的应用" 门户中的 "应用" 页](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 选择 "**访问评审**" 磁贴，查看等待批准的访问评审的列表。

    ![针对组织的待定访问评审的 "访问评审" 页](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 选择 "**开始检查**" 开始访问评审。

5. 查看访问权限并确定是否仍有必要。

    !["访问评审" 页，其中显示了查看详细信息](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >如果你是管理员，并且允许审查你的组织对组和应用的访问权限，你将看到不同的页面。 有关查看组织的组或应用的详细信息，请参阅[在 Azure AD 访问评审中查看对组或应用程序的访问权限](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。

6. 选择 **"是"** 以保留访问权限，或选择 "**否**" 以删除访问权限。

    如果选择 **"是"** ，则可能需要在 "**原因**" 框中指定理由。

    !["访问评审" 页，显示 "原因" 框和示例文本](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 选择“提交”。

    你的访问评审已完成，你将返回到 "**我的应用**" 门户。

    >[!Note]
    >你可以随时更改你的访问权限，直到访问评审期间结束。 如果删除对应用或组的访问权限，则不会立即删除它。 当访问评审期间结束或管理员关闭评审时，将发生删除。

## <a name="next-steps"></a>后续步骤

- [访问和使用 "我的应用" 门户上的应用](my-apps-portal-end-user-access.md)。

- [更改你的配置文件信息](my-apps-portal-end-user-update-profile.md)。

- [查看和更新组相关的信息](my-apps-portal-end-user-groups.md)。
