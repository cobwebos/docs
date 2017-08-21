---
title: "教程：Azure Active Directory 与 Jive 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Jive 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 957b152fdd40d08a867e788b0cb9f7d57ed481e4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# <a name="tutorial-configuring-jive-for-user-provisioning"></a>教程：为 Jive 配置用户预配

本教程旨在介绍为了从 Azure AD 自动将用户帐户预配到 Jive 和取消其预配而需要在 Jive 和 Azure 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

*   Azure Active Directory 租户。
*   已启用 Jive 单一登录的订阅。
*   具有团队管理员权限的 Jive 用户帐户。

## <a name="assigning-users-to-jive"></a>将用户分配到 Jive

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 Jive 应用的用户。 确定后，可按照此处的说明将这些用户分配到 Jive 应用：

[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>将用户分配到 Jive 的重要提示

*   建议将单个 Azure AD 用户分配到 Jive 以测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 Jive 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-user-provisioning"></a>启用用户预配

本部分将指导用户完成以下操作：将 Azure AD 连接到 Jive 的用户帐户预配 API，配置预配服务以便基于 Azure AD 中的用户和组分配在 Jive 中创建、更新和禁用所分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Jive 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-user-account-provisioning"></a>配置用户帐户预配：

本部分的目的是概述如何对 Jive 启用 Active Directory 用户帐户的用户预配。
在此过程中，你将需要提供从 Jive.com 发出请求所需的用户安全令牌。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 Jive 配置单一登录，请使用搜索字段搜索 Jive 实例。 否则，请选择“添加”并在应用程序库中搜索“Jive”。 从搜索结果中选择 Jive，并将其添加到应用程序列表。

3. 选择 Jive 实例，并选择“预配”选项卡。

4. 将“预配模式”设置为“自动”。 

    ![预配](./media/active-directory-saas-jive-provisioning-tutorial/provisioning.png)

5. 在“管理员凭据”部分中，提供以下配置设置：
   
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“Jive 管理员用户名”文本框中，键入在 Jive.com 中已分配**系统管理员**配置文件的 Jive 帐户名称。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“Jive 管理员密码”文本框中，键入此帐户的密码。
   
    c. 在“Jive 租户 URL”文本框中，键入 Jive 租户 URL。
      
      > [!NOTE]
      > Jive 租户 URL 是组织用于登录到 Jive 的 URL。  
      > 通常，该 URL 具有以下格式：**www.\<organization\>.jive.com**。          

6. 在 Azure 门户中，单击“测试连接”确保 Azure AD 可以连接到 Jive 应用。

7. 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中下面的复选框。

8. 单击“保存”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Jive”。

10. 在“属性映射”部分，查看从 Azure AD 同步到 Jive 的用户属性。 选为“匹配”属性的特性用于匹配 Jive 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

11. 要为 Jive 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

12. 单击“保存”。

这会开始将“用户和组”部分中分配的任何用户和/或组初始同步到 Jive。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务对 Jive 应用执行的所有操作。

现可创建测试帐户。 验证该帐户是否已同步到 Jive 最多需等待 20 分钟时间。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置单一登录](active-directory-saas-jive-tutorial.md)
