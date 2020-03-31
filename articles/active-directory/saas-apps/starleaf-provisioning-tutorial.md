---
title: 教程：使用 Azure 活动目录配置 StarLeaf 以自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消向 StarLeaf 预配用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064285"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 StarLeaf

本教程的目的是演示在 StarLeaf 和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向 StarLeaf 预配用户和/或组。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [星叶租户](https://www.starleaf.com/solutions/)。
* 具有管理员权限的 StarLeaf 中的用户帐户。

## <a name="assign-users-to-starleaf"></a>将用户分配给 StarLeaf
Azure 活动目录使用称为分配的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和组需要访问 StarLeaf。 然后，您可以按照[这些说明](../manage-apps/assign-user-or-group-access-portal.md)将用户和组分配给 StarLeaf。

## <a name="important-tips-for-assigning-users-to-starleaf"></a>将用户分配给 StarLeaf 的重要提示

* 建议将单个 Azure AD 用户分配给 StarLeaf 以测试自动用户预配配置。 稍后可以分配其他用户和组。

* 将用户分配给 StarLeaf 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有默认访问权限角色的用户从预配中排除。

## <a name="set-up-starleaf-for-provisioning"></a>设置用于预配的 StarLeaf

在为使用 Azure AD 进行自动用户预配之前，需要在 StarLeaf 中配置 SCIM 预配：

1. 登录到您的[StarLeaf 管理控制台](https://portal.starleaf.com/#page=login)。 导航到**集成** > **添加集成**。

    ![星叶添加 SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. 选择 **"类型**"为 Microsoft Azure 活动目录。 在**名称**中输入合适的名称。 单击 **“应用”**。

    ![星叶添加 SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  然后将显示**SCIM 基本 URL**和**Access 令牌**值。 这些值将在 Azure 门户 StarLeaf 应用程序的预配选项卡中的 **"租户 URL"** 和 **"秘密令牌"** 字段中输入。 

    ![星叶创建令牌](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>从图库添加星叶

要配置 StarLeaf 以使用 Azure AD 进行自动用户预配，需要将 Azure AD 应用程序库中的 StarLeaf 添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加 StarLeaf，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**StarLeaf**，在结果面板中选择**StarLeaf。**
    ![结果列表中的 StarLeaf](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>配置自动用户预配到 StarLeaf

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 StarLeaf 中创建、更新和禁用用户和/或组的步骤。

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择**StarLeaf**。

    !["应用程序"列表中的 StarLeaf 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在"管理凭据"部分下，输入之前在**租户 URL**和 **"秘密令牌"** 中检索的**SCIM 基本 URL**和**访问令牌**值。 单击 **"测试连接**"以确保 Azure AD 可以连接到 StarLeaf。 如果连接失败，请确保您的 StarLeaf 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在 **"通知电子邮件"** 字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并在**发生故障时选中"发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到 StarLeaf**。

    ![星叶创建令牌](media/starleaf-provisioning-tutorial/usermapping.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到 StarLeaf 的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 StarLeaf 中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![星叶创建令牌](media/starleaf-provisioning-tutorial/userattribute.png)


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。


11. 要为 StarLeaf 启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 StarLeaf 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 StarLeaf 上执行的所有操作。

有关如何读取 Azure AD 预配日志的详细信息，请参阅[报告自动用户帐户预配](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>连接器限制

* StarLeaf 目前不支持组预配。 
* StarLeaf 要求**电子邮件**和**用户名**值具有相同的源值。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* 了解如何[查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)。
