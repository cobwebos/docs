---
title: 教程：配置 Brivo Onair 标识连接器，以便使用 Azure 活动目录自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消将用户帐户预配到 Brivo Onair 标识连接器。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: f3c1e7337c0ce07b7fbebb9f954deeb75f0b9584
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246647"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>教程：配置 Brivo Onair 标识连接器，以便自动进行用户预配

本教程的目的是演示将在 Brivo Onair 标识连接器和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消将用户和/或组预配到 Brivo Onair 标识连接器。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [布里沃 onair 标识连接器租户](https://www.brivo.com/lp/quote)
* Brivo Onair 标识连接器中的用户帐户，具有高级管理员权限。

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>将用户分配给 Brivo Onair 标识连接器

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Brivo Onair 标识连接器。 一旦确定，您可以按照此处的说明将这些用户和/或组分配给 Brivo Onair 标识连接器：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>将用户分配给 Brivo Onair 标识连接器的重要提示

* 建议将单个 Azure AD 用户分配给 Brivo Onair 标识连接器以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 Brivo Onair 标识连接器时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>设置用于预配的 Brivo Onair 标识连接器

1.    登录到您的[Brivo Onair 身份连接器管理控制台](https://acs.brivo.com/login/)。 导航到**帐户>帐户设置**。

    ![布里沃 Onair 身份连接器管理控制台](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  单击**Azure AD**选项卡。在**Azure AD**详细信息页上重新输入高级管理员帐户的密码。 单击"**提交**"。

    ![布里沃 onair 标识连接器蔚蓝](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.    单击**复制令牌**按钮并保存**秘密令牌**。 此值将在 Azure 门户中的 Brivo Onair 标识连接器应用程序的"预配"选项卡中的"机密令牌"字段中输入。

    ![布里沃 Onair 标识连接器令牌](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>从库中添加 Brivo Onair 标识连接器

在配置 Brivo Onair 标识连接器以使用 Azure AD 进行自动用户预配之前，需要将 Azure AD 应用程序库中的 Brivo Onair 标识连接器添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加 Brivo Onair 标识连接器，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Brivo Onair 标识连接器**，在结果面板中选择**Brivo Onair 标识连接器**，然后单击"**添加**"按钮以添加应用程序。

    ![结果列表中的 Brivo Onair 标识连接器](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>将自动用户预配配置配置到 Brivo Onair 标识连接器 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 Brivo Onair 标识连接器中创建、更新和禁用用户和/或组的步骤。

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>要在 Azure AD 中为 Brivo Onair 标识连接器配置自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择**Brivo Onair 标识连接器**。

    ![应用程序列表中的 Brivo Onair 标识连接器链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下`https://scim.brivo.com/ActiveDirectory/v2/`，在**租户 URL**中输入 。 输入在**秘密令牌**中检索到的**SCIM 身份验证令牌**值。 单击 **"测试连接**"以确保 Azure AD 可以连接到 Brivo Onair 标识连接器。 如果连接失败，请确保您的 Brivo Onair 标识连接器帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射**"部分下，选择**将 Azure 活动目录用户同步到 Brivo Onair 标识连接器**。

    ![布里沃 onair 标识连接器用户映射](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. 在**属性映射**部分中查看从 Azure AD 同步到 Brivo Onair 标识连接器的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 Brivo Onair 标识连接器中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![布里沃 onair 标识连接器用户属性](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. 在 **"映射**"部分下，选择**将 Azure 活动目录组同步到 Brivo Onair 标识连接器**。

    ![布里沃 Onair 标识连接器组映射](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. 查看**属性映射**部分中从 Azure AD 同步到 Brivo Onair 标识连接器的组属性。 选择为 **"匹配属性"** 的属性用于匹配 Brivo Onair 标识连接器中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![布里沃 onair 标识连接器组属性](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要为 Brivo Onair 标识连接器启用 Azure AD 预配服务，请在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 Brivo Onair 标识连接器的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述由 Brivo Onair 标识连接器上 Azure AD 预配服务执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

