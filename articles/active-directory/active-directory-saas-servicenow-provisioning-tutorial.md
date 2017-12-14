---
title: "教程：使用 Azure Active Directory 配置 ServiceNow 实现自动用户预配 | Microsoft Docs"
description: "了解如何将用户帐户从 Azure AD 自动预配到 ServiceNow 及如何取消预配。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 06772c9e21936d224f721413ccdc1c859b07b940
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>教程：使用 Azure Active Directory 配置 ServiceNow 实现自动用户预配

本教程的目的是说明从 Azure AD 自动将用户帐户预配到 ServiceNow 和取消其预配所需在 ServiceNow 和 Azure 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定已有以下各项：

*   Azure Active Directory 租户。
*   必须拥有 ServiceNow for Work 或 ServiceNow for Education 的有效租户。 免费试用帐户可用于任一服务。
*   在 ServiceNow 中具有团队管理员权限的用户帐户。

## <a name="assigning-users-to-servicenow"></a>将用户分配到 ServiceNow

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 ServiceNow 应用的用户。 确定后，可按照此处的说明将这些用户分配到 ServiceNow 应用：[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   建议将单个 Azure AD 用户分配到 ServiceNow 以测试预配配置。 其他用户和/或组可以稍后分配。
>*   将用户分配到 ServiceNow 时，必须选择有效用户角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-automated-user-provisioning"></a>启用自动化用户预配

本部分指导完成将 Azure AD 连接到 ServiceNow 的用户帐户预配 API 并配置预配服务，以便在 ServiceNow 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

> [!TIP]
>还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 ServiceNow 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-account-provisioning"></a>配置用户帐户自动预配

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 ServiceNow 配置单一登录，请使用搜索字段搜索 ServiceNow 实例。 否则，请选择“添加”并在应用程序库中搜索“ServiceNow”。 从搜索结果中选择 ServiceNow，并将其添加到应用程序列表。

3. 选择 ServiceNow 实例，然后选择“预配”选项卡。

4. 将预配模式设置为“自动”。 

    ![预配](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. 在“管理员凭据”部分中，执行以下步骤：
   
    a. 在“ServiceNow 实例名称”文本框中，键入 ServiceNow 实例名称。

    b. 在“ServiceNow 管理员用户名”文本框中，键入管理员的用户名。

    c. 在“ServiceNow 管理员密码”文本框中，键入管理员的密码。

6. 在 Azure 门户中，单击“测试连接”以确保 Azure AD 可以连接到 ServiceNow 应用。 如果连接失败，请确保 ServiceNow 帐户具有团队管理员权限，并重试“管理员凭据”步骤。

7. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址，并选中复选框。

8. 单击“保存”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 ServiceNow”。

10. 在“属性映射”部分中，查看从 Azure AD 同步到 ServiceNow 的用户属性。 选为“匹配”属性的特性用于匹配 ServiceNow 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

11. 若要为 ServiceNow 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

12. 单击“保存”。

此操作会对“用户和组”部分中分配到 ServiceNow 的任何用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务在 ServiceNow 应用上执行的所有操作。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置单一登录](active-directory-saas-servicenow-tutorial.md)


