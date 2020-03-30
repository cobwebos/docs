---
title: 教程：配置 iPass 智能连接，以便使用 Azure 活动目录进行自动用户预配 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消预配用户帐户到 iPass SmartConnect。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057507"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>教程：配置 iPass 智能连接，用于自动用户预配

本教程的目的是演示在 iPass 智能连接和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向 iPass SmartConnect 预配用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [iPass 智能连接租户](https://www.ipass.com/buy-ipass/)。
* iPass SmartConnect 中的用户帐户具有管理员权限。

## <a name="assigning-users-to-ipass-smartconnect"></a>将用户分配给 iPass 智能连接

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问 iPass SmartConnect。 一旦确定，您可以按照此处的说明将这些用户和/或组分配给 iPass SmartConnect：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>将用户分配给 iPass 智能连接的重要提示

* 建议将单个 Azure AD 用户分配给 iPass 智能连接以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 iPass SmartConnect 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="setup-ipass-smartconnect-for-provisioning"></a>设置 iPass 智能连接以进行预配

在配置 iPass 智能连接以使用 Azure AD 自动预配用户之前，您需要从 iPass 智能连接管理控制台检索配置信息：

1. 要检索针对 iPass SmartConnect SCIM 终结点进行身份验证所需的承载令牌，请参阅您第一次设置 iPass SmartConnect 时，因为此值仅在那时提供。 
2. 如果您没有承载令牌，请联系[iPass SmartConnect 的支持团队](mailto:help@ipass.com)以检索新的令牌。

## <a name="add-ipass-smartconnect-from-the-gallery"></a>从图库添加 iPass 智能连接

要配置 iPass 智能连接以使用 Azure AD 进行自动用户预配，您需要将 iPass SmartConnect 从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加 iPass 智能连接，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，在结果面板中选择**iPass** **智能连接**，然后单击 **"添加**"按钮以添加应用程序。

    ![结果列表中的 iPass SmartConnect](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>配置自动用户预配到 iPass 智能连接 

本节将指导您完成将 Azure AD 预配服务配置为基于 Azure AD 中的用户和/或组分配在 iPass SmartConnect 中创建、更新和禁用用户和/或组的步骤。

> [!TIP]
>  您也可以选择启用基于SAML的单一登录为比塔BIZ，按照[在iPass智能连接单一登录教程](ipasssmartconnect-tutorial.md)中提供的说明。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>要在 Azure AD 中配置 iPass 智能连接的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“iPass SmartConnect”。****

    ![应用程序列表中的 iPass SmartConnect 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下`https://openmobile.ipass.com/moservices/scim/v1`，在**租户 URL**中输入 。 输入在**秘密令牌**中检索较早的承载令牌。 单击 **"测试连接**"以确保 Azure AD 可以连接到 iPass 智能连接。 如果连接失败，请确保您的 iPass 智能连接帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到 iPass 智能连接**。

    ![iPass 智能连接用户映射](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到 iPass 智能连接的用户属性。 选择为 **"匹配属性"** 的属性用于匹配 iPass SmartConnect 中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![iPass 智能连接用户映射](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要启用 iPass 智能连接的 Azure AD 预配服务，请将 **"设置"** 部分中的 **"预配状态**更改为**打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 iPass SmartConnect 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 您可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 iPass SmartConnect 上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* iPass 智能连接只接受在 iPass 智能连接管理控制台中注册其域的用户名。  

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
