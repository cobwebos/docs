---
title: "教程：使用 Azure Active Directory 为 GitHub 配置自动用户预配 | Microsoft Docs"
description: "了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 GitHub 和取消其预配。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: e06d04234f33633275d85732a321a264c777fc59
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>教程：为 GitHub 配置自动用户预配


本教程的目的是说明从 Azure AD 自动将用户帐户预配到 GitHub 和取消其预配所需在 GitHub 和 Azure 中执行的步骤。 

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定已有以下各项：

*   Azure Active Directory 租户
*   启用了[企业计划](https://help.github.com/articles/organization-billing-plans/#business-plan)或更佳计划的 GitHub 租户 
*   在 GitHub 中具有管理员权限的用户帐户 

> [!NOTE]
> Azure AD 预配集成依赖于可供 GitHub 团队在企业计划或更佳计划中使用的 [GitHub SCIM API](https://developer.github.com/v3/scim/)。

## <a name="assigning-users-to-github"></a>将用户分配到 GitHub

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。 

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 GitHub 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 GitHub 应用：

[向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>将用户分配到 GitHub 的重要提示

*   建议将单个 Azure AD 用户分配到 GitHub 以测试预配配置。 其他用户和/或组可以稍后分配。

*   如果将用户分配到 GitHub，必须在分配对话框中选择“用户”角色或其他特定于应用程序的有效角色（如果有）。 “默认访问权限”角色不可用于预配，将跳过这些用户。


## <a name="configuring-user-provisioning-to-github"></a>为 GitHub 配置用户预配 

本部分指导完成将 Azure AD 连接到 GitHub 的用户帐户预配 API 和配置预配服务，以便在 GitHub 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 GitHub 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>在 Azure AD 中为 GitHub 配置自动用户帐户预配


1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 GitHub 配置单一登录，请使用搜索字段搜索 GitHub 实例。 否则，请选择“添加”并在应用程序库中搜索“GitHub”。 从搜索结果中选择 GitHub，并将其添加到应用程序列表。

3. 选择 GitHub 实例，并选择“预配”选项卡。

4. 将“预配模式”设置为“自动”。

    ![GitHub 预配](./media/active-directory-saas-github-provisioning-tutorial/GitHub1.png)

5. 在“管理员凭据”部分下，单击“授权”。 此操作将在新的浏览器窗口中打开“GitHub 授权”对话框。 

6. 在新窗口中，使用管理员帐户登录到 GitHub。 在显示的授权对话框中，选择要启用预配的 GitHub 团队，并选择“授权”。 完成后，返回到 Azure 门户完成预配配置。

    ![授权对话框](./media/active-directory-saas-github-provisioning-tutorial/GitHub2.png)

7. 在 Azure 门户中，输入“租户 URL”并单击“测试连接”，确保 Azure AD 可以连接到 GitHub 应用。 如果连接失败，请确保 GitHub 帐户具有管理员权限，且输入的“租户 URL”正确无误，然后再次尝试“授权”步骤（可以按规则：“https://api.github.com/scim/v2/organizations/ + <Organizations_name>”来编写“租户 URL”，可在 GitHub 帐户：“设置” > “组织”下找到你的组织）。

    ![授权对话框](./media/active-directory-saas-github-provisioning-tutorial/GitHub3.png)

8. 在“通知电子邮件”字段中输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

9. 单击“ **保存**”。 

10. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 GitHub”。

11. 在“属性映射”部分，查看从 Azure AD 同步到 GitHub 的用户属性。 选为“匹配”属性的特性用于匹配 GitHub 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

12. 要为 GitHub 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

13. 单击“ **保存**”。 

此操作会对“用户和组”部分中分配到 GitHub 的任何用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)。


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](active-directory-saas-provisioning-reporting.md)
