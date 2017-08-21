---
title: "教程：Azure Active Directory 与 Dropbox for Business 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Dropbox for Business 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 6f7616e47322242f01a13d763f71c93d4ac06a92
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# <a name="tutorial-configuring-dropbox-for-business-for-automatic-user-provisioning"></a>教程：配置 Dropbox for Business 以进行自动用户预配

本教程旨在说明从 Azure AD 自动将用户帐户预配到 Dropbox for Business 和取消其预配需要在 Dropbox for Business 和 Azure 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

*   Azure Active Directory 租户。
*   已启用 Dropbox for Business 单一登录的订阅。
*   Dropbox for Business 中具有团队管理员权限的用户帐户。

## <a name="assigning-users-to-dropbox-for-business"></a>将用户分配到 Dropbox for Business

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务之前，需要确定 Azure AD 中的哪些用户和/或组表示需要访问 Dropbox for Business 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 Dropbox for Business 应用：

[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>将用户分配到 Dropbox for Business 的重要提示

*   建议将单个 Azure AD 用户分配到 Dropbox for Business 以测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 Dropbox for Business 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-automated-user-provisioning"></a>启用自动化用户预配

本部分指导完成将 Azure AD 连接到 Dropbox for Business 的用户帐户预配 API 并配置预配服务，以便在 Dropbox for Business 中根据 Azure AD 中的用户和组分配来创建、更新和禁用分配的用户帐户。

>[!Tip]
>还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Dropbox for Business 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-account-provisioning"></a>配置用户帐户自动预配：

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2. 如果已为 Dropbox for Business 配置单一登录，请使用搜索字段搜索 Dropbox for Business 实例。 否则，请选择“添加”并在应用程序库中搜索“Dropbox for Business”。 从搜索结果中选择 Dropbox for Business，并将其添加到应用程序列表。

3. 选择 Dropbox for Business 实例，然后选择“预配”选项卡。

4. 将“预配模式”设置为“自动”。 

    ![预配](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. 在“管理员凭据”部分下，单击“授权”。 随即将在新的浏览器窗口中打开 Dropbox for Business 登录对话框。

6. 在“登录 Dropbox 以链接 Azure AD”对话框中，登录到 Dropbox for Business 租户。

     ![用户设置](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769518.png "用户设置")

7. 确认是否想要授予 Azure Active Directory 对 Dropbox for Business 租户进行更改的权限。 单击“允许”。
    
      ![用户设置](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769519.png "用户设置")

8. 在 Azure 门户中，单击“测试连接”确保 Azure AD 可以连接到 Dropbox for Business 应用。 如果连接失败，请确保 Dropbox for Business 帐户具有团队管理员权限，并重试“授权”步骤。

9. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址，并选中复选框。

10. 单击“保存”。

11. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Dropbox for Business”。

12. 在“属性映射”部分中，查看从 Azure AD 同步到 Dropbox for Business 的用户属性。 选为“匹配”属性的特性用于匹配 Dropbox for Business 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

13. 若要为 Dropbox for Business 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

14. 单击“保存”。

这会开始“用户和组”部分中分配给 Dropbox for Business 的任何用户和/或组的初始同步。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务对 Dropbox for Business 应用执行的所有操作。

现可创建测试帐户。 最多等待 20 分钟验证帐户是否已同步到 Dropbox for Business。

会出现相关状态提示已成功完成用户预配周期。

![分配用户](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/IC769523.png "分配用户")


## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置单一登录](active-directory-saas-dropboxforbusiness-tutorial.md)
