---
title: 教程：为 Oracle 合成 ERP 配置 Azure Active Directory 的自动用户预配 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Oracle 合成 ERP 并取消其预配。
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061170"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>教程：配置 Oracle 合成 ERP 以实现自动用户预配

本教程的目的是演示要在 Oracle 合成 ERP 和 Azure Active Directory （Azure AD）中执行的步骤，以将 Azure AD 自动预配和取消预配到 Oracle 合成 ERP。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关预览功能的一般 Microsoft Azure 使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Oracle 合成 ERP 租户](https://www.oracle.com/applications/erp/)。
* Oracle 合成 ERP 中具有管理员权限的用户帐户。

## <a name="assign-users-to-oracle-fusion-erp"></a>将用户分配到 Oracle 合成 ERP 
Azure Active Directory 使用称为 "分配" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应决定 Azure AD 中哪些用户和/或组需要访问 Oracle 合成 ERP。 确定后，可按照此处的说明将这些用户和/或组分配到 Oracle 合成 ERP：
 
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>将用户分配到 Oracle 合成 ERP 的重要提示 

 * 建议将单个 Azure AD 用户分配到 Oracle 合成 ERP 来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 Oracle 合成 ERP 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 将从设置中排除具有默认访问角色的用户。

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>设置 Oracle 合成 ERP 用于预配

在将 Oracle 合成 ERP 配置为使用 Azure AD 进行自动用户预配之前，需要在 Oracle 合成 ERP 上启用 SCIM 预配。

1. 登录到你的[Oracle 合成 ERP 管理控制台](https://cloud.oracle.com/sign-in)

2. 单击左上角的 "导航器"。 在 "**工具**" 下，选择 "**安全控制台**"。

    ![Oracle 合成 ERP 添加 SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. 导航到 "**用户**"。
    
    ![Oracle 合成 ERP 添加 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. 保存将用于登录到 Oracle 合成 ERP 管理控制台的管理员用户帐户的用户名和密码。 需要在 Azure 门户中 Oracle 合成 ERP 应用程序的 "设置" 选项卡的 "**管理员用户名**" 和 "**密码**" 字段中输入这些值。

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>从库中添加 Oracle 合成 ERP

若要为 Oracle 合成 ERP 配置 Azure AD 的自动用户预配，需要从 Azure AD 应用程序库中将 Oracle 合成 ERP 添加到托管 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库添加 Oracle 合成 ERP，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Oracle 合成 erp**，在结果面板中选择 " **oracle 合成 erp** "。

    ![结果列表中的“Oracle Fusion ERP”](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>配置 Oracle 合成 ERP 的自动用户预配 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在 Oracle 合成 ERP 中创建、更新和禁用用户和/或组。

> [!TIP]
> 你还可以根据[Oracle 合成 Erp 单一登录教程](oracle-fusion-erp-tutorial.md)中提供的说明，选择为 ORACLE 合成 erp 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

> [!NOTE]
> 若要了解有关 Oracle 合成 ERP 的 SCIM 终结点的详细信息，请参阅[Oracle 应用程序云中常见功能 REST API](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)。

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>若要在 Azure AD 中配置 Fuze 的自动用户预配：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，选择“Oracle Fusion ERP”。

    ![“应用程序”列表中的“Oracle Fusion ERP”链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/`。 在 "**管理员用户名**" 和 "**密码**" 字段中输入之前检索到的管理员用户名和密码。 单击 Azure AD 和 Oracle 合成 ERP 之间的 "**测试连接**"。 

    ![Oracle 合成 ERP 添加 SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击 **“保存”** 。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Oracle 合成 ERP**"。

    ![Oracle 合成 ERP 添加 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 ORACLE 合成 ERP 的用户属性。 选为 "**匹配**" 属性的属性用于匹配 ORACLE 合成 ERP 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Oracle 合成 ERP 添加 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到 Oracle 合成 ERP**"。

    ![Oracle 合成 ERP 组映射](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 ORACLE 合成 ERP 的组属性。 选为 "**匹配**" 属性的属性用于匹配 ORACLE 合成 ERP 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Oracle 合成 ERP 组属性](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Oracle 合成 ERP 启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 Oracle 合成 ERP 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

    此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了在 ORACLE 合成 ERP 上 Azure AD 预配服务执行的所有操作。

    若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Oracle 合成 ERP 仅支持其 SCIM 终结点的基本身份验证。
* Oracle 合成 ERP 不支持组设置。
* Oracle 合成 ERP 中的角色映射到 Azure AD 中的组。 要从 Azure AD 向 Oracle 合成 ERP 中的用户分配角色，你需要将用户分配到在 Oracle 合成 ERP 中指定了 "角色" 的所需 Azure AD 组。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
