---
title: 教程：使用 Azure 活动目录配置代理单击以自动预配用户 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配用户帐户并将其预配到代理单击。
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063350"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>教程：为自动用户预配配置代理单击

本教程的目的是演示在代理单击和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消将用户和/或组预配到代理单击。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [代理单击租户](https://www.proxyclick.com/pricing)
* 具有管理员权限的"代理单击"中的用户帐户。

## <a name="add-proxyclick-from-the-gallery"></a>从库中添加 Proxyclick

在使用 Azure AD 配置代理单击以自动预配用户之前，需要将 Azure AD 应用程序库中的代理单击添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加代理单击，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，在结果面板中输入 **"代理单击"，** 然后单击"**添加**"按钮以添加应用程序。 **Proxyclick**

    ![结果列表中的 Proxyclick](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>将用户分配给代理单击

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问代理单击。 决定后，您可以按照此处的说明将这些用户和/或组分配给 Proxyclick：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>将用户分配给代理单击的重要提示

* 建议将单个 Azure AD 用户分配给 Proxyclick 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 Proxyclick 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>将自动用户预配配置为代理单击 

本节将指导您完成将 Azure AD 预配服务配置为基于 Azure AD 中的用户和/或组分配在代理单击中创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 您也可以根据[代理单击单一登录教程](proxyclick-tutorial.md)中提供的说明，选择启用基于 SAML 的单一登录代理单击。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>要在 Azure AD 中配置代理单击的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Proxyclick”。****

    ![应用程序列表中的 Proxyclick 链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 要检索代理单击帐户的**租户 URL**和**秘密令牌**，请按照步骤 6 中所述的演练进行操作。

6. 登录到您的[代理单击管理控制台](https://app.proxyclick.com/login//?destination=%2Fdefault)。 导航到**设置** > **集成** > **浏览市场**。

    ![代理单击设置](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![代理单击集成](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![代理单击市场](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    选择“Azure AD”****。 单击 **“立即安装”**。

    ![代理单击 Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![代理单击安装](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    选择**用户预配**，然后单击 **"开始集成**"。 

    ![代理单击用户预配](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    适当的设置配置 UI 现在应该显示在 **"设置** > **集成**"下。 选择 Azure AD 下的**设置****（用户预配）。**

    ![代理单击"创建"](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    您可以在此处找到**租户 URL**和**秘密令牌**。

    ![代理单击创建令牌](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. 填充步骤 5 中所示的字段后，单击 **"测试连接**"以确保 Azure AD 可以连接到代理单击。 如果连接失败，请确保您的代理单击帐户具有管理员权限，然后重试。

    ![标记](common/provisioning-testconnection-tenanturltoken.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击“保存”。****

10. 在 **"映射**"部分下，选择 **"将 Azure 活动目录用户同步到代理单击**"。

    ![代理单击用户映射](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. 在**属性映射**部分中查看从 Azure AD 同步到代理单击的用户属性。 选择为 **"匹配属性"** 的属性用于匹配"代理单击"中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![代理单击用户属性](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

14. 要启用代理单击的 Azure AD 预配服务，请在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

15. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到代理单击的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

16. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在代理单击上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* 代理单击要求**电子邮件**和**用户名**具有相同的源值。 对任一属性的任何更新都将修改其他值。
* 代理单击不支持组预配。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

