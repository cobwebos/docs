---
title: 教程：配置自动用户预配使用 Azure Active Directory 联合目录 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 自动预配和取消其预配用户帐户添加到联合目录。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 19f5690a6852161abce2565a8c4a52ce86ff5187
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840584"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>教程：将联合目录配置自动用户预配

本教程的目的是为了演示在联合目录和 Azure Active Directory (Azure AD) 若要配置 Azure AD 自动预配和取消其预配的用户和/或组到联合目录中执行的步骤。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 有关常规 Microsoft Azure 的使用条款预览版功能的详细信息，请参阅[补充使用条款的 Microsoft Azure 预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [一个联合目录](https://www.federated.directory/pricing)。
* 联合目录中具有管理员权限的用户帐户。

## <a name="assign-users-to-federated-directory"></a>将用户分配到联合目录
Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问。 在自动用户预配的上下文中，同步仅用户和/或组已分配给 Azure AD 中的应用程序。

在配置和启用自动用户预配前, 应确定哪些用户和/或 Azure AD 中的组需要访问联合目录。 确定后，您可以将这些用户和/或组到联合目录分配按照此处的说明：

 * [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>将用户分配到联合目录的重要提示
 * 建议将单个 Azure AD 用户分配到联合目录，以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到联合目录，您必须在分配对话框中选择任何有效的特定于应用程序角色 （如果可用）。 具有默认访问权限角色的用户不在预配。
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>设置联合目录预配

在配置之前联合目录与 Azure AD 自动用户预配，需要启用 SCIM 预配联合目录。

1. 登录到你[联合目录管理员控制台](https://federated.directory/of)

    ![联合的目录教程](media/federated-directory-provisioning-tutorial/companyname.png)

2. 导航到**目录 > 用户目录**，然后选择你的租户。 

    ![联合的目录](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  若要生成的永久持有者令牌，请导航到**目录密钥 > 创建新的密钥。** 

    ![联合的目录](media/federated-directory-provisioning-tutorial/federated01.png)

4. 创建目录项。 

    ![联合的目录](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. 复制“访问令牌”  值。 此值将被放入**机密令牌**联合目录应用程序在 Azure 门户中的预配选项卡中的字段。 

    ![联合的目录](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>从库中添加联合目录

若要配置自动用户预配与 Azure AD 联合目录，你需要将 Azure AD 应用程序库中的联合目录添加到托管 SaaS 应用程序的列表。

**若要从 Azure AD 应用程序库中添加联合目录，请执行以下步骤：**

1. 在中 **[Azure 门户](https://portal.azure.com)** ，在左侧的导航窗格中，选择**Azure Active Directory**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。  

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新的应用程序，请选择**新的应用程序**窗格顶部的按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**联合目录**，选择**联合目录**在结果面板中。

    ![结果列表中的联合的目录](common/search-new-app.png)

5. 导航到**URL**以下在单独的浏览器中突出显示部分。 

    ![联合的目录](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. 单击**登录**。

    ![联合的目录](media/federated-directory-provisioning-tutorial/federated04.png)

7.  联合目录是 OpenIDConnect 应用，选择将登录到联合目录中使用你的 Microsoft 工作帐户。
    
    ![联合的目录](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. 身份验证成功后，接受同意页的许可提示。 然后将会应用程序自动添加到你的租户，你将重定向到联合目录帐户。

    ![联合的目录中添加 SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>配置自动用户预配到联合目录 

本部分将指导您完成配置 Azure AD 预配服务以创建、 更新和禁用用户和/或基于用户和/或组分配 Azure AD 中的联合目录中的组的步骤。

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>若要配置自动用户在 Azure AD 中预配为联合目录：

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**的所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择**Federated Directory**。

    ![应用程序列表中的联合目录链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![预配选项卡](common/provisioning-automatic.png)

5. 下**管理员凭据**部分中，输入`https://api.federated.directory/v2/`租户 url。 输入用于检索和从联合目录中以前保存的值**机密令牌**。 单击**测试连接**以确保 Azure AD 可以连接到联合目录。 如果连接失败，请确保你的联合目录帐户具有管理员权限，然后重试。

    ![租户 URL 和令牌](common/provisioning-testconnection-tenanturltoken.png)

8. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

9. 单击“保存”  。

10. 下**映射**部分中，选择**Azure Active Directory 用户同步到联合目录**。

    ![联合的目录教程](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. 查看从 Azure AD 同步到联合目录中的用户属性**属性映射**部分。 为所选的属性**匹配**属性用于匹配联合目录中的用户帐户，以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![联合的目录教程](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要启用 Azure AD 预配服务联合目录，更改**预配状态**到**上**中**设置**部分。

    ![预配状态切换](common/provisioning-toggle-on.png)

14. 选择所需的值中预配到联合目录中定义的用户和/或组你想**作用域**中**设置**部分。

    ![预配作用域](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”  。

    ![正在保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用**同步详细信息**部分监视进度并跟踪指向预配活动报告，其中描述了由 Azure AD 预配服务对联合目录执行所有操作。

有关如何读取 Azure AD 预配日志的详细信息，请参阅[有关自动用户帐户预配报告](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)