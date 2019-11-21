---
title: Manage your organization's access to apps & groups - Azure AD
description: Learn how to perform an access review to manage security access for your organization's apps and groups from the My Apps portal.
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
ms.openlocfilehash: 232f4b6fde84b7e1cd706b62e0ba9a0998b4171e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231844"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Perform an access review from the My Apps portal

可以在基于 Web 的“我的应用”门户中使用工作或学校帐户，以便查看并启动组织的许多基于云的应用、更新你的部分配置文件和帐户信息、查看**组**信息，以及为应用和组执行“访问评审”。 如果无法访问“我的应用”门户，则必须联系支持人员以获取相关权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)中查找有关如何设置和管理基于云的应用的详细信息。

## <a name="manage-access-reviews"></a>管理访问评审

If your administrator has given you permission to perform your own access reviews, you can manage your groups or apps access from the **Access reviews** tile on the **My Apps** portal page.

>[!Note]
>If you don't see the **Access reviews** tile it either means that you don't have permission to perform access reviews, or that you don't have any pending reviews waiting for your approval. If you think you should have access to the tile, contact your Help desk for assistance.

### <a name="to-perform-your-access-reviews"></a>To perform your access reviews

1. 登录到工作或学校帐户。

2. Open your web browser and go to https://myapps.microsoft.com, or use the link provided by your organization. For example, you might be directed to a customized page for your organization, such as https://myapps.microsoft.com/contoso.com.

    The **Apps** page appears, showing all the cloud-based apps owned by your organization and available for you to use.

    ![Apps page in the My Apps portal](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Select the **Access reviews** tile to see a list of access reviews waiting for your approval.

    ![Access reviews page with pending access reviews for the organization](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Select **Begin review** to start your access review.

5. Review your access and determine whether it's still necessary.

    ![Access review page, showing the review details](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >If you're an administrator, and allowed to review your organization's access to groups and apps, you'll see a different page. For more information about reviewing groups or apps for your organization, see [Review access to groups or applications in Azure AD Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Select **Yes** to keep your access or **No** to remove your access.

    If you select **Yes**, you might need to specify a justification in the **Reason** box.

    ![Access review page, showing the Reason box with sample text](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 选择“提交”。

    Your access review is complete and you return to the **My Apps** portal.

    >[!Note]
    >You can change your access at any time until your access review period ends. If you remove your access to an app or group, it's not removed immediately. The removal happens when the access review period ends or when an administrator closes the review.

## <a name="next-steps"></a>后续步骤

- [Access and use apps on the My Apps portal](my-apps-portal-end-user-access.md).

- [Change your profile information](my-apps-portal-end-user-update-profile.md).

- [View and update your groups-related information](my-apps-portal-end-user-groups.md).
