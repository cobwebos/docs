---
title: 教程：使用 Azure Active Directory 为 Zscaler Three 配置自动用户预配 | Microsoft Docs
description: 本教程介绍如何将 Azure Active Directory 配置为自动为 Zscaler Three 预配和取消预配用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: d96444984c503da68ccbda3aef9fea0ede5c7ff9
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579047"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>教程：为 Zscaler Three 配置自动用户预配

本教程介绍如何将 Azure Active Directory (Azure AD) 配置为自动为 Zscaler Three 预配和取消预配用户和/或组。

> [!NOTE]
> 本教程将介绍构建在 Azure AD 用户预配服务基础之上的连接器。 有关此服务的功能、工作原理以及常见问题解答的重要详细信息，请参阅[使用 Azure Active Directory 为 SaaS 应用程序自动化用户预配和取消预配](../active-directory-saas-app-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

若要完成本教程中所述的步骤，需要准备好以下各项：

* Azure AD 租户。
* Zscaler Three 租户。
* Zscaler Three 中具有管理员权限的用户帐户。

> [!NOTE]
> Azure AD 预配集成依赖于适用于企业帐户的 Zscaler ZSCloud SCIM API。

## <a name="adding-zscaler-three-from-the-gallery"></a>从库添加 Zscaler Three

在使用 Azure AD 为 Zscaler Three 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Zscaler Three 添加到托管的 SaaS 应用程序列表。

在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”：

![选择“Azure Active Directory”](common/select-azuread.png)

转到“企业应用程序”，选择“所有应用程序”：

![企业应用程序](common/enterprise-applications.png)

若要添加应用程序，请在窗口顶部选择“新建应用程序”：

![选择“新建应用程序”](common/add-new-app.png)

在搜索框中输入 **Zscaler Three**。 在结果中选择“Zscaler Three”，然后选择“添加”。

![结果列表](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>将用户分配到 Zscaler Three

需要为 Azure AD 用户分配对所选应用的访问权限，然后用户才能使用这些应用。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 Zscaler Three。 确定后，可遵照[将用户或组分配到企业应用](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的说明，将这些用户和组分配到 Zscaler Three。

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>有关将用户分配到 Zscaler Three 的重要提示

* 建议先将一个 Azure AD 用户分配到 Zscaler Three 以测试自动用户预配配置。 稍后再分配更多的用户和组。

* 将用户分配到 Zscaler Three 时，需要在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-automatic-user-provisioning"></a>设置自动用户预配

本部分介绍如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和组分配在 Zscaler Three 中创建、更新和禁用用户和组。

> [!TIP]
> 此外，还建议为 Zscaler Three 启用基于 SAML 的单一登录。 若要这样做，请遵照 [Zscaler Three 单一登录教程](zscaler-three-tutorial.md)中的说明操作。 可以独立于自动用户预配配置单一登录，但这两个功能是互补的。

1. 登录到 [Azure 门户](https://portal.azure.com)，选择“企业应用程序” > “所有应用程序” > “Zscaler Three”：

    ![企业应用程序](common/enterprise-applications.png)

2. 在“应用程序”列表中选择“Zscaler Three”：

    ![“应用程序”列表](common/all-applications.png)

3. 选择“预配”选项卡：

    ![Zscaler Three 预配](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. 将“预配模式”设置为“自动”：

    ![设置预配模式](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. 在“管理员凭据”部分中，输入 Zscaler Three 帐户的“租户 URL”和“机密令牌”，如下一步中所述。

6. 若要获取“租户 URL”和“机密令牌”，请转到 Zscaler Three 门户中的“管理” > “身份验证设置”，然后选择“身份验证类型”下的 SAML：

    ![Zscaler Three 身份验证设置](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    选择“配置 SAML”打开“配置 SAML”窗口：

    ![“配置 SAML”窗口](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    选择“启用基于 SCIM 的预配”并复制“基本 URL”和“持有者令牌”，然后保存设置。 在 Azure 门户中，将“基本 URL”粘贴到“租户 URL”框中，将“持有者令牌”粘贴到“机密令牌”框中。

7. 在“租户 URL”和“机密令牌”框中输入值后，选择“测试连接”以确保 Azure AD 可以连接到 Zscaler Three。 如果连接失败，请确保 Zscaler Three 帐户具有管理员权限，然后重试。

    ![测试连接](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. 在“通知电子邮件”框中输入应接收预配错误通知的人员或组的电子邮件地址。 选择“发生故障时发送电子邮件通知”：

    ![设置通知电子邮件](./media/zscaler-three-provisioning-tutorial/notification.png)

9. 选择“保存”。

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 ZscalerThree”：

    ![同步 Azure AD 用户](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Zscaler Three 的用户属性。 选为“匹配”属性的特性用于匹配 Zscaler Three 中的用户帐户以执行更新操作。 选择“保存”，提交所有更改。

    ![属性映射](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. 在“映射”部分中，选择“将 Azure Active Directory 组同步到 ZscalerThree”：

    ![同步 Azure AD 组](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. 在“属性映射”部分中，查看从 Azure AD 同步到 Zscaler Three 的组属性。 选为“匹配”属性的特性用于匹配 Zscaler Three 中的组以执行更新操作。 选择“保存”，提交所有更改。

    ![属性映射](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. 若要配置范围筛选器，请参阅[范围筛选器教程](./../active-directory-saas-scoping-filters.md)中的说明。

15. 若要为 Zscaler Three 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”：

    ![预配状态](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Zscaler Three 的用户和/或组。

    ![范围值](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. 准备好预配时，选择“保存”：

    ![选择“保存”](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

此操作会对“设置”部分的“范围”中定义的所有用户和组启动初始同步。 初始同步花费的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每 40 分钟就会进行一次同步。 可在“同步详细信息”部分中监视进度。 还可以单击预配活动报告的链接，其中描述了 Azure AD 预配服务对 Zscaler Three 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
