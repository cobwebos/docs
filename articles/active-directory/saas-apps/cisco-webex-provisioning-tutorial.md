---
title: 教程：使用 Azure Active Directory 为 Cisco 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 来自动将用户帐户预配到 Cisco Webex 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: fdaf77e3d8a1858372298fb0d67ca05c2717adf6
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36324162"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>教程：为 Cisco Webex 配置自动用户预配


本教程的目的是演示要将 Azure AD 配置为自动将用户预配到 Cisco Webex 以及取消其预配需在 Cisco Webex 和 Azure Active Directory (Azure AD) 中执行的步骤。


> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

*   Azure AD 租户
*   Cisco Webex 租户
*   Cisco Webex 中具有管理员权限的用户帐户


> [!NOTE]
> Azure AD 预配集成依赖于供 Cisco Webex 团队使用的 [Cisco Webex Webservice](https://developer.webex.com/getting-started.html)。

## <a name="adding-cisco-webex-from-the-gallery"></a>从库中添加 Cisco Webex
在使用 Azure AD 为 Cisco Webex 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Cisco Webex 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Cisco Webex，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”部分][2]

3. 若要添加 Cisco Webex，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Cisco Webex”。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. 在结果面板中，选择“Cisco Webex”，然后单击“添加”按钮将 Cisco Webex 添加到 SaaS 应用程序列表。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>将用户分配到 Cisco Webex

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户需要访问 Cisco Webex。 确定后，可以按照此处的说明将这些用户分配到 Cisco Webex：

*   [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>有关将用户分配到 Cisco Webex 的重要提示

*   建议将单个 Azure AD 用户分配到 Cisco Webex 来测试自动用户预配配置。 其他用户可以稍后分配。

*   如果将用户分配到 Cisco Webex，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>配置 Cisco Webex 的自动用户预配

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户分配在 Cisco Webex 中创建、更新和禁用用户。


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>若要在 Azure AD 中为 Cisco Webex 配置自动用户预配，请执行以下操作：


1. 登录 [Azure 门户](https://portal.azure.com)，并浏览到“Azure Active Directory”>“企业应用程序”>“所有应用程序”。

2. 从 SaaS 应用程序列表中选择 Cisco Webex。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. 选择“预配”选项卡。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. 将“预配模式”设置为“自动”。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. 在“管理员凭据”部分下，输入你的 Cisco Webex 帐户的**租户 URL** 和**机密令牌**。

    *   在“租户 URL”字段中，填充租户的 Cisco Webex SCIM API URL（采用 `https://api.webex.com/v1/scim/[Tenant ID]/` 形式），其中 `[Tenant ID]` 是步骤 6 中所述的字母数字字符串。

    *   在“机密令牌”字段中，填充步骤 6 中所述的机密令牌。

1. 可以通过使用管理员帐户登录 [Cisco Webex 开发人员网站](https://developer.webex.com/)，找到 Cisco Webex 帐户的**租户 ID** 和**机密令牌**。 登录后 -
    * 转到[入门页](https://developer.webex.com/getting-started.html)
    * 向下滚动到[“身份验证”部分](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Webex 身份验证令牌](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * 框中的字母数字字符串是**机密令牌**。 将此令牌复制到剪贴板
    * 转到[“获取我的详细信息”页](https://developer.webex.com/endpoint-people-me-get.html)
        * 请确保“测试模式”为“开”
        * 键入“Bearer”一词，在该词后添加一个空格，然后将机密令牌粘贴到授权字段 ![Cisco Webex 身份验证令牌](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
        * 单击“运行”
    * 在右侧的响应文本中，**租户 ID** 显示为“orgId”：

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. 填入步骤 5 中所示的字段后，单击“测试连接”确保 Azure AD 可以连接到 Cisco Webex。 如果连接失败，请确保 Cisco Webex 帐户具有管理员权限，然后重试。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. 单击“ **保存**”。

10. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 Cisco Webex”。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Cisco Webex 的用户属性。 选为“匹配”属性的特性用于匹配 Cisco Webex 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../active-directory-saas-scoping-filters.md)中提供的以下说明。

13. 若要为 Cisco Webex 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Cisco Webex 的用户和/或组。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. 已准备好预配时，单击“保存”。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/Save.png)


此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Cisco Webex 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png