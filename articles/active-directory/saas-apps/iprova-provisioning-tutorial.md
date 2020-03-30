---
title: 教程：使用 Azure 活动目录配置 iProva 以自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消预配到 iProva 的用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057493"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 iProva

本教程的目的是演示要在 iProva 和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向 iProva 预配用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [iProva 租户](https://www.iProva.com/)
* iProva 中的具有管理员权限的用户帐户。

## <a name="assigning-users-to-iprova"></a>将用户分配给 iProva

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问 iProva。 一旦确定，您可以按照此处的说明将这些用户和/或组分配给 iProva：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>将用户分配给 iProva 的重要提示

* 建议将单个 Azure AD 用户分配给 iProva 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 iProva 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="set-up-iprova-for-provisioning"></a>设置用于预配的 iProva

1. 登录到您的[iProva 管理控制台](https://www.iProva.com/)。 导航**到转到>应用程序管理**。

    ![iProva 管理控制台](media/iprova-provisioning-tutorial/admin.png)

2.  单击**外部用户管理**。

    ![iProva 添加 SCIM](media/iprova-provisioning-tutorial/external.png)

3. 要添加新提供程序，请单击**加号**图标。 在新的 **"添加提供程序"** 对话框中，提供**标题**。 您可以选择添加**基于 IP 的访问限制**。 单击 **"确定"** 按钮。

    ![iProva 添加新](media/iprova-provisioning-tutorial/add.png)

    ![iProva 添加提供程序](media/iprova-provisioning-tutorial/addprovider.png)

4.  单击**永久令牌**按钮。 复制**永久令牌**并保存它，因为这将是您唯一可以查看它的时间。 此值将在 Azure 门户中的 iProva 应用程序的预配选项卡中的"机密令牌"字段中输入。

    ![iProva 创建令牌](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>从库中添加 iProva

在配置 iProva 以使用 Azure AD 进行自动用户预配之前，需要将 iProva 从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加 iProva，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，在结果面板中选择**iProva，** 然后单击 **"添加**"按钮以添加应用程序。 **iProva**

    ![结果列表中的 iProva](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>将自动用户预配配置配置到 iProva 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配在 iProva 中创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 您也可以选择启用基于 SAML 的单登录 iProva，按照[iProva 单点登录教程](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial)中提供的说明。 单一登录可以独立于自动用户预配进行配置，尽管这两个功能相辅相成。

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>要在 Azure AD 中配置 iProva 的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“iProva”。****

    ![应用程序列表中的 iProva 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下`https://identitymanagement.services.iProva.nl/scim`，在**租户 URL**中输入 。 输入在**秘密令牌**中检索到的**永久令牌**值。 单击 **"测试连接**"以确保 Azure AD 可以连接到 iProva。 如果连接失败，请确保您的 iProva 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到 iProva**。

    ![iProva 用户映射](media/iprova-provisioning-tutorial/usermappings.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到 iProva 的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 iProva 中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![iProva 用户属性](media/iprova-provisioning-tutorial/userattributes.png)

10. 在 **"映射"** 部分下，选择**将 Azure 活动目录组同步到 iProva**。

    ![iProva 组映射](media/iprova-provisioning-tutorial/groupmappings.png)

11. 在**属性映射**部分中查看从 Azure AD 同步到 iProva 的组属性。 选择为 **"匹配属性"** 的属性用于匹配 iProva 中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![iProva 组属性](media/iprova-provisioning-tutorial/groupattributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要为 iProva 启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 iProva 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并遵循指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 iProva 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

