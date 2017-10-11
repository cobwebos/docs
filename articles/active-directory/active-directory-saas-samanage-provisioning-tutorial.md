---
title: "教程：使用 Azure Active Directory 为自动用户预配配置 Samanage | Microsoft Docs"
description: "了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Samanage 和取消其预配。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: 278ebf464fbe815568fbe332f80d5ea6b29e1811
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2017
---
# <a name="tutorial-configuring-samanage-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Samanage


本教程的目的是说明从 Azure AD 自动将用户帐户预配到 Samanage 和取消其预配而需要在 Samanage 和 Azure 中执行的步骤。 

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定已有以下各项：

*   Azure Active Directory 租户
*   已启用[专业计划](https://www.samanage.com/pricing/)或更高版本的 Samanage 租户 
*   在 Samanage 中具有管理员权限的用户帐户 

> [!NOTE]
> Azure AD 预配集成依赖于 [ REST API](https://www.samanage.com/api/)，它在专业计划或更高版本中向 Samanage 团队提供。

## <a name="assigning-users-to-samanage"></a>将用户分配到 Samanage

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。 

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 Samanage 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 Samanage 应用：

[向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-samanage"></a>将用户分配到 Samanage 的重要提示

*   建议将单个 Azure AD 用户分配到 Samanage 以测试预配配置。 其他用户和/或组可以稍后分配。

*   如果将用户分配到 Samanage，必须在分配对话框中选择用户角色或其他特定于应用程序的有效角色（如果有）。 “默认访问权限”角色不可用于预配，将跳过这些用户。

> [!NOTE]
> 作为新增功能，预配服务将读取 Samanage 中定于的所有自定义角色，并导入 Azure AD，可在其中的“选择角色”对话框中对其进行选择。 启用预配服务并完成一个同步周期后，这些角色将在 Azure 门户中可见。

## <a name="configuring-user-provisioning-to-samanage"></a>向 Samanage 配置用户预配 

本部分指导完成将 Azure AD 连接到 Samanage 的用户帐户预配 API 和配置预配服务，以便在 Samanage 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Samanage 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。


### <a name="configure-automatic-user-account-provisioning-to-samanage-in-azure-ad"></a>若要在 Azure AD 中为 Samanage 配置自动用户帐户预配，请执行以下操作：


1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 Samanage 配置单一登录，请使用搜索字段搜索 Samanage 实例。 否则，请选择“添加”并在应用程序库中搜索“Samanage”。 从搜索结果中选择 Samanage，并将其添加到应用程序列表。

3. 选择 Samanage 实例，然后选择“预配”选项卡。

4. 将“预配模式”设置为“自动”。

    ![Samanage 预配](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage1.png)

5. 在“管理员凭据”分区下，输入 Samanage 帐户的“管理员用户名和管理员密码”。 

6. 在 Azure 门户中，单击“测试连接”以确保 Azure AD 可以连接到 Samanage 应用。 如果连接失败，请确保 Samanage 帐户具有管理员权限，然后重试步骤 5。

7. 在“通知电子邮件”字段中输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

8. 单击“保存” 。 

9. 在“映射”分区下，选择“将 Azure Active Directory 用户同步到 Samanage”。

10. 在“属性映射”分区中，查看从 Azure AD 同步到 Samanage 的用户属性。 选为“匹配”属性的特性用于匹配 Samanage 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

11. 若要为 Samanage 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

12. 单击“保存” 。 

此操作会开始将“用户和组”分区中分配的任何用户和/或组初始同步到 Samanage。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting)。


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](active-directory-saas-provisioning-reporting.md)
