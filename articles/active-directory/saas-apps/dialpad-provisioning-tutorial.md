---
title: 教程：配置自动用户预配使用 Azure Active Directory 的拨号盘 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消其预配用户帐户添加到拨号盘。
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611783"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>教程：配置自动用户预配拨号盘

本教程的目的是为了演示在拨号盘和 Azure Active Directory (Azure AD) 若要配置 Azure AD 自动预配和取消其预配的用户和/或组到拨号盘中执行的步骤。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。

> 此连接器目前处于预览状态。 有关常规 Microsoft Azure 的使用条款预览版功能的详细信息，请参阅[补充使用条款的 Microsoft Azure 预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [拨号盘租户](https://www.dialpad.com/pricing/)。
* 拨号盘中具有管理员权限的用户帐户。

## <a name="assign-users-to-dialpad"></a>将用户分配到拨号盘
Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问。 在自动用户预配的上下文中，同步仅用户和/或组已分配给 Azure AD 中的应用程序。

在配置和启用自动用户预配前, 应确定哪些用户和/或 Azure AD 中的组需要访问拨号盘。 确定后，您可以将这些用户和/或组到拨号盘分配按照此处的说明：
 
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>将用户分配到拨号盘的重要提示

 * 建议将单个 Azure AD 用户分配到拨号盘以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到拨号盘，必须在分配对话框中选择任何有效的特定于应用程序角色 （如果可用）。 具有默认访问权限角色的用户不在预配。

## <a name="setup-dialpad-for-provisioning"></a>安装程序拨号盘预配

配置自动用户预配与 Azure AD 拨号盘之前, 将需要从拨号盘检索某些设置的信息。

1. 登录到您[拨号盘管理控制台](https://dialpadbeta.com/login)，然后选择**管理员设置**。 絋粄**My Company**从下拉列表中选择。 导航到**身份验证 > API 密钥**。

    ![拨号盘添加 SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. 通过单击生成新密钥**添加的键**和配置的机密令牌属性。

    ![拨号盘添加 SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![拨号盘添加 SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. 单击**单击此项可显示值**按钮为你最近创建的 API 密钥，然后复制显示的值。 此值将被放入**机密令牌**拨号盘应用程序在 Azure 门户中的预配选项卡中的字段。 

    ![拨号盘创建令牌](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>从库中添加拨号盘

若要配置自动用户预配与 Azure AD 的拨号盘，需要将拨号盘从 Azure AD 应用程序库添加到托管 SaaS 应用程序的列表。

**若要从 Azure AD 应用程序库中添加拨号盘，请执行以下步骤：**

1. 在中 **[Azure 门户](https://portal.azure.com)** ，在左侧的导航窗格中，选择**Azure Active Directory**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。  

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新的应用程序，请选择**新的应用程序**窗格顶部的按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**拨号盘**，选择**拨号盘**在结果面板中。
    ![结果列表中的拨号盘](common/search-new-app.png)

5. 导航到**URL**以下在单独的浏览器中突出显示部分。 

    ![拨号盘添加 SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. 在右上角，选择**日志中 > 联机使用拨号盘**。

    ![拨号盘添加 SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. 拨号盘是 OpenIDConnect 应用，选择将登录到拨号盘使用 Microsoft 工作帐户。

    ![拨号盘添加 SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. 身份验证成功后，接受同意页的许可提示。 然后将会应用程序自动添加到你的租户，你将重定向到拨号盘帐户。

    ![拨号盘添加 SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>配置自动用户预配到拨号盘

本部分将指导你完成步骤以配置 Azure AD 预配服务以创建、 更新和禁用用户和/或在 Azure AD 中基于用户和/或组分配拨号盘中的组。

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>若要配置自动用户在 Azure AD 中预配的拨号盘：

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**的所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择**拨号盘**。

    ![应用程序列表中的拨号盘链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![预配选项卡](common/provisioning-automatic.png)

5. 下**管理员凭据**部分中，输入`https://dialpad.com/scim`中**租户 URL**。 输入用于检索和从拨号盘中以前保存的值**机密令牌**。 单击**测试连接**以确保 Azure AD 可以连接到拨号盘。 如果连接失败，请确保拨号盘帐户具有管理员权限，然后重试。

    ![租户 URL 和令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”  。

8. 下**映射**部分中，选择**Azure Active Directory 用户同步到拨号盘**。

    ![拨号盘用户映射](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. 查看从 Azure AD 同步到拨号盘中的用户属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配拨号盘中以执行更新操作的用户帐户。 选择“保存”按钮以提交任何更改  。

    ![拨号盘用户属性](media/dialpad-provisioning-tutorial/dialpad07.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要启用 Azure AD 预配服务的拨号盘，更改**预配状态**到**上**中**设置**部分。

    ![预配状态切换](common/provisioning-toggle-on.png)

12. 选择所需的值中预配到拨号盘中定义的用户和/或组你想**作用域**中**设置**部分。

    ![预配作用域](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”  。

    ![正在保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告，其中描述了由 Azure AD 预配服务拨号盘上的执行所有操作。

有关如何读取 Azure AD 预配日志的详细信息，请参阅[有关自动用户帐户预配报告](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>连接器限制
* 拨号盘目前不支持组重命名。 这意味着，对任何更改**displayName**组在 Azure 中的 AD 不会更新并反映在拨号盘。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
