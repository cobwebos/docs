---
title: 教程：使用 Azure Active Directory 为 Cisco Webex 配置自动用户预配 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 来自动将用户帐户预配到 Cisco Webex 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: f4089d690602aa761b1942bd3d538e2ef305aa19
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73570335"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>教程：为 Cisco Webex 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户预配到 Cisco Webex 以及取消其预配需在 Cisco Webex 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。
>
> 此连接器目前提供预览版。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [Cisco Webex 租户](https://www.webex.com/pricing/index.html)。
* Cisco Webex 中具有管理员权限的用户帐户。

## <a name="adding-cisco-webex-from-the-gallery"></a>从库中添加 Cisco Webex

在使用 Azure AD 为 Cisco Webex 配置自动用户预配之前，需要从 Azure AD 应用程序库将 Cisco Webex 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Cisco Webex，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Cisco Webex”，在结果面板中选择“Cisco Webex”，然后单击“添加”按钮以添加该应用程序。   

    ![结果列表中的 Cisco Webex](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>将用户分配到 Cisco Webex

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户需要访问 Cisco Webex。 确定后，可以按照此处的说明将这些用户分配到 Cisco Webex：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>有关将用户分配到 Cisco Webex 的重要提示

* 建议将单个 Azure AD 用户分配到 Cisco Webex 来测试自动用户预配配置。 其他用户可以稍后分配。

* 如果将用户分配到 Cisco Webex，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>配置 Cisco Webex 的自动用户预配

本部分介绍了如何配置 Azure AD 预配服务以基于 Azure AD 中的用户分配在 Cisco Webex 中创建、更新和禁用用户。

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>若要在 Azure AD 中为 Cisco Webex 配置自动用户预配，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，依次选择“企业应用程序”  、“所有应用程序”  和“Cisco Webex”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Cisco Webex”  。

    ![应用程序列表中的 Cisco Webex 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![Cisco Webex 预配](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![Cisco Webex 预配](common/provisioning-automatic.png)

5. 在“管理员凭据”  部分下，输入你的 Cisco Webex 帐户的“租户 URL”  和“机密令牌”  。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  在“租户 URL”  字段中，以 `https://api.ciscoweb.com/v1/scim/[OrgId]` 的形式输入值。 若要获取 `[OrgId]`，请登录到 [Cisco Webex 控制中心](https://admin.webex.com/login)。 单击左下角的组织名称，并复制“组织 ID”中的值  。 

    * 若要获取“机密令牌”  的值，请导航到此 [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose)。 在出现的 webex 登录页面中，使用组织的完整 Cisco Webex 管理员帐户登录。 出现错误页面，指出无法访问站点，但这是正常情况。

        ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * 复制 URL 中生成的持有者令牌的值，如下所示。 此令牌的有效期为 365 天。
        
        ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/test1.png)

7. 填入步骤 5 中所示的字段后，单击“测试连接”确保 Azure AD 可以连接到 Cisco Webex  。 如果连接失败，请确保 Cisco Webex 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)
   
8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击“ **保存**”。

10. 在“映射”部分中，选择“将 Azure Active Directory 用户同步到 Cisco Webex”   。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. 在“属性映射”部分中，查看从 Azure AD 同步到 Cisco Webex 的用户属性  。 选为“匹配”属性的特性用于匹配 Cisco Webex 中的用户帐户以执行更新操作  。 选择“保存”按钮以提交任何更改  。

    ![Cisco Webex 预配](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Cisco Webex 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”    。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 Cisco Webex 的用户和/或组   。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 Cisco Webex 执行的所有操作  。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Cisco Webex 当前处于 Cisco 的早期现场测试 (EFT) 阶段。 有关详细信息，请联系 [Cisco 的支持团队](https://www.webex.co.in/support/support-overview.html)。 
* 有关 Cisco Webex 配置的详细信息，请参阅[此处](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)的 Cisco 文档。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)