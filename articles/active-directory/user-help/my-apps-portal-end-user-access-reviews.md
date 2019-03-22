---
title: 从我的应用门户-Azure Active Directory 执行访问评审 |Microsoft Docs
description: 了解如何查看和管理组织的应用程序和组的安全访问权限。
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
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340194"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>从我的应用门户中执行访问评审
可以使用基于 web 的使用工作或学校帐户**我的应用**门户中，来查看和启动很多组织的基于云的应用，以更新您的部分配置文件和帐户信息，若要查看你**组**信息，并执行**访问评审**应用和组。 如果您不能访问**我的应用**门户中，您必须与支持人员联系的权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以找到有关如何设置和管理基于云的应用程序中的详细信息[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)。

## <a name="manage-access-reviews"></a>管理访问评审
如果你的管理员已授予权限以执行你自己的访问评审，你可以管理从您的组或应用程序的访问权限**访问评审**磁贴**我的应用**门户页。

>[!Note]
>如果没有看到**访问评审**平铺其任一方式没有权限执行访问评审或你不具有任何挂起的等待批准的评审。 如果您认为应该有权访问该磁贴，以获取帮助与支持人员联系。

### <a name="to-perform-your-access-reviews"></a>若要执行访问评审

1.  登录到工作或学校帐户。

2.  打开 web 浏览器并转到 https://myapps.microsoft.com，或使用由你的组织提供的链接。 例如，你可能会定向到自定义页面为你的组织，例如 https://myapps.microsoft.com/contoso.com。

    **应用**页出现后，显示所有基于云的应用，由你的组织拥有和可供你使用。

    ![我的应用门户中的应用程序页](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 选择**访问评审**磁贴以查看一系列访问评审你的审批等待。

    ![访问评审页与挂起的组织的访问评审](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 选择**开始评审**若要启动访问评审。

5. 查看你的访问权限并确定它是否仍有必要。

    ![访问评审页面，显示查看详细信息](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >如果您是管理员，并允许评审你的组织组和应用的访问权限，您将看到不同的页。 有关查看组或应用为你的组织的详细信息，请参阅[评审访问权限给组或应用程序在 Azure AD 访问评审](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。

6. 选择**是**保护访问或**否**删除你的访问权限。

    如果选择**是**，可能需要指定在理由**原因**框。

    ![访问评审页面，显示原因框中带示例文本](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 选择“提交”。

    访问评审已完成并返回到**我的应用**门户。

    >[!Note]
    >可以在任何时候您访问评审期结束之前更改你的访问权限。 如果删除应用程序或组的访问，它是不会立即删除。 访问评审期结束时或当管理员关闭评审时，会发生删除。 

## <a name="next-steps"></a>后续步骤

- [访问和我的应用门户上使用应用](my-apps-portal-end-user-access.md)。

- [更改个人资料信息](my-apps-portal-end-user-update-profile.md)。

- [查看和更新你与组相关的信息](my-apps-portal-end-user-groups.md)。