---
title: 教程：配置 OfficeSpace 软件，以便使用 Azure 活动目录进行自动用户预配 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消向 OfficeSpace 软件预配用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f832a0a6-ad0a-453f-a747-9cd717e11181
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: 3d472b300400cf230773ba01f3f4362988c34e81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063391"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>教程：为自动用户预配配置办公空间软件

本教程的目的是演示在 OfficeSpace 软件和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向 OfficeSpace 软件预配和取消预配用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [办公空间软件租户](https://www.officespacesoftware.com/)
* 具有管理员权限的 OfficeSpace 软件中的用户帐户。

## <a name="assigning-users-to-officespace-software"></a>将用户分配给办公空间软件

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问 OfficeSpace 软件。 一旦确定，您可以按照此处的说明将这些用户和/或组分配给 OfficeSpace 软件：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>将用户分配给 OfficeSpace 软件的重要提示

* 建议将单个 Azure AD 用户分配给 OfficeSpace 软件以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 OfficeSpace 软件时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="set-up-officespace-software-for-provisioning"></a>设置用于预配的办公空间软件

1. 登录到[您的 OfficeSpace 软件管理控制台](https://support.officespacesoftware.com/hc)。 导航到**设置>连接器**。

    ![办公空间软件管理控制台](media/officespace-software-provisioning-tutorial/settings.png)

2.  导航到**目录同步> SCIM**。

    ![办公空间软件添加SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  复制**SCIM 身份验证令牌**。 此值将在 Azure 门户中的 OfficeSpace 软件应用程序的"预配"选项卡中的"机密令牌"字段中输入。

    ![办公空间软件创建令牌](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>从图库添加办公空间软件

在配置 OfficeSpace 软件以使用 Azure AD 进行自动用户预配之前，需要将 OfficeSpace 软件从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加 OfficeSpace 软件，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**OfficeSpace 软件**，在结果面板中选择**OfficeSpace 软件**，然后单击 **"添加**"按钮以添加应用程序。

    ![结果列表中的 OfficeSpace Software](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>配置自动用户预配到办公空间软件 

本节将指导您完成将 Azure AD 预配服务配置为根据 Azure AD 中的用户和/或组分配创建、更新和禁用 OfficeSpace 软件中的用户和/或组的步骤。

> [!TIP]
> 您也可以按照[OfficeSpace 软件单一登录教程](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial)中提供的说明，选择启用基于 SAML 的单一登录。 单一登录可以独立于自动用户预配进行配置，尽管这两个功能相辅相成。

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>要在 Azure AD 中配置 OfficeSpace 软件的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“OfficeSpace Software”。****

    ![应用程序列表中的 OfficeSpace Software 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理员凭据"** 部分下`https://<subdomain>.officespacesoftware.com/api/scim/v2/`，在**租户 URL**中输入 URL 格式。 例如，`https://contoso.officespacesoftware.com/api/scim/v2/`。 输入在**秘密令牌**中检索到的**SCIM 身份验证令牌**值。 单击 **"测试连接**"以确保 Azure AD 可以连接到办公空间软件。 如果连接失败，请确保您的 OfficeSpace 软件帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在 **"通知电子邮件"** 字段中，输入应接收预配错误通知的个人或组的电子邮件地址，然后选中"**在发生故障时发送电子邮件通知**"复选框。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到办公空间软件**。

    ![办公空间软件用户映射](media/officespace-software-provisioning-tutorial/usermappings.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到 OfficeSpace 软件的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 OfficeSpace 软件中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![办公空间软件用户属性](media/officespace-software-provisioning-tutorial/userattributes.png)

11. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

12. 要启用 OfficeSpace 软件的 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

13. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 OfficeSpace 软件的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

14. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 您可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 OfficeSpace 软件上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

