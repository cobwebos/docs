---
title: 教程：配置 Oracle 融合 ERP 以自动使用 Azure 活动目录进行用户预配 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消向 Oracle Fusion ERP 预配用户帐户。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061170"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>教程：配置 Oracle 融合 ERP 以自动调配用户

本教程的目的是演示在 Oracle Fusion ERP 和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向 Oracle Fusion ERP 预配用户和/或组。

> [!NOTE]
>  本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前提供预览版。 有关预览功能的一般 Microsoft Azure 使用条款的详细信息，请参阅[Microsoft Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [甲骨文融合ERP租户](https://www.oracle.com/applications/erp/)。
* 具有管理员权限的 Oracle Fusion ERP 中的用户帐户。

## <a name="assign-users-to-oracle-fusion-erp"></a>将用户分配到 Oracle 融合 ERP 
Azure 活动目录使用称为分配的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应决定 Azure AD 中的哪些用户和/或组需要访问 Oracle Fusion ERP。 一旦确定，您可以按照此处的说明将这些用户和/或组分配给 Oracle Fusion ERP：
 
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>将用户分配给 Oracle 融合 ERP 的重要提示 

 * 建议将单个 Azure AD 用户分配给 Oracle Fusion ERP 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 Oracle Fusion ERP 时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有默认访问权限角色的用户从预配中排除。

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>设置 Oracle 融合 ERP 进行预配

在配置 Oracle Fusion ERP 以使用 Azure AD 进行自动用户预配之前，您需要在 Oracle Fusion ERP 上启用 SCIM 预配。

1. 登录[您的 Oracle 融合 ERP 管理控制台](https://cloud.oracle.com/sign-in)

2. 单击左上角的导航器。 在 **"工具"** 下，选择 **"安全控制台**"。

    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. 导航到**用户**。
    
    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. 保存管理员用户帐户的用户名和密码，您将用于登录到 Oracle Fusion ERP 管理控制台。 这些值需要在 Azure 门户中的 Oracle Fusion ERP 应用程序的预配选项卡中的 **"管理员用户名和密码****"字段中输入**。

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>从库中添加 Oracle 融合 ERP

要配置 Oracle Fusion ERP 以自动预配 Azure AD，您需要将 Oracle Fusion ERP 从 Azure AD 应用程序库添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加 Oracle Fusion ERP，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**Oracle 融合 ERP**，在结果面板中选择**Oracle 融合 ERP。**

    ![结果列表中的“Oracle Fusion ERP”](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>配置自动用户预配到 Oracle 融合 ERP 

本节将指导您完成将 Azure AD 预配服务配置为基于 Azure AD 中的用户和/或组分配在 Oracle Fusion ERP 中创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 您也可以按照[Oracle Fusion ERP 单一登录教程](oracle-fusion-erp-tutorial.md)中提供的说明，选择启用基于 SAML 的单一登录，以实现 Oracle Fusion ERP。 单一登录可以独立于自动用户预配进行配置，尽管这两个功能相辅相成。

> [!NOTE]
> 要了解有关 Oracle Fusion ERP SCIM 终结点的更多信息，请参阅[REST API 了解 Oracle 应用程序云中的通用功能](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)。

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>要在 Azure AD 中配置 Fuze 的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在“应用程序”列表中，选择“Oracle Fusion ERP”。****

    ![“应用程序”列表中的“Oracle Fusion ERP”链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下`https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/`，在**租户 URL**中输入 。 在 **"管理员用户名和密码"****字段中输入**较早检索的管理员用户名和密码。 单击 Azure AD 和 Oracle 融合 ERP 之间的**测试连接**。 

    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射"** 部分下，选择**将 Azure 活动目录用户同步到 Oracle 融合 ERP**。

    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到 Oracle 融合 ERP 的用户属性。 选择为**匹配**属性的属性用于匹配 Oracle Fusion ERP 中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![甲骨文融合ERP添加SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. 在 **"映射"** 部分下，选择**将 Azure 活动目录组同步到 Oracle 融合 ERP**。

    ![甲骨文融合ERP集团映射](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. 在**属性映射**部分中查看从 Azure AD 同步到 Oracle 融合 ERP 的组属性。 选择为**匹配**属性的属性用于匹配 Oracle Fusion ERP 中的组以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![甲骨文融合ERP集团属性](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 要为 Oracle Fusion ERP 启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 **"设置"** 部分中选择"**范围"** 中所需的值，定义要预配到 Oracle Fusion ERP 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

    此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 您可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 Oracle Fusion ERP 上执行的所有操作。

    若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Oracle 融合 ERP 仅支持其 SCIM 终结点的基本身份验证。
* Oracle 融合 ERP 不支持组预配。
* Oracle 融合 ERP 中的角色映射到 Azure AD 中的组。 要从 Azure AD 向 Oracle Fusion ERP 中的用户分配角色，您需要将用户分配给以 Oracle Fusion ERP 中的角色命名的所需的 Azure AD 组。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
