---
title: 教程：配置 SAP 云平台标识身份验证，以便使用 Azure 活动目录进行自动用户预配 |微软文档
description: 了解如何将 Azure 活动目录配置为自动预配和取消向 SAP 云平台标识身份验证预配和取消预配用户帐户。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060394"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 SAP 云平台标识身份验证

本教程的目的是演示在 SAP 云平台标识身份验证和 Azure 活动目录 （Azure AD） 中执行的步骤，以将 Azure AD 配置为自动预配和取消向 SAP 云平台标识身份验证预配和取消预配用户和/或组。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [SAP 云平台标识身份验证租户](https://cloudplatform.sap.com/pricing.html)
* 具有管理员权限的 SAP 云平台标识身份验证中的用户帐户。

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>将用户分配给 SAP 云平台标识身份验证

Azure 活动目录使用称为*分配*的概念来确定哪些用户应接收对选定应用的访问权限。 在自动用户预配的上下文中，只有分配给 Azure AD 中应用程序的用户和/或组才会同步。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 SAP 云平台标识身份验证。 决定后，您可以按照此处的说明将这些用户和/或组分配给 SAP 云平台标识身份验证：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>将用户分配给 SAP 云平台标识身份验证的重要提示

* 建议将单个 Azure AD 用户分配给 SAP 云平台标识身份验证以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配给 SAP 云平台标识身份验证时，必须在分配对话框中选择任何有效的特定于应用程序的角色（如果可用）。 具有**默认访问权限**角色的用户从预配中排除。

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>设置用于预配的 SAP 云平台标识身份验证

1. 登录到您的[SAP 云平台身份身份验证管理控制台](https://sapmsftintegration.accounts.ondemand.com/admin)。 导航到**用户&授权>管理员**。

    ![SAP 云平台身份认证管理控制台](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  创建管理员用户并选择该用户。  

3.  在"配置授权"下，打开针对 **"管理用户**"和管理**组**的切换按钮。

    ![SAP 云平台标识身份验证添加 SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. 您将收到一封电子邮件以激活您的帐户，并为**SAP 云平台身份认证服务**设置密码。

4.  复制**用户 ID**和**密码**。 这些值将分别在 Azure 门户中的 SAP 云平台标识身份验证应用程序的预配选项卡中输入。

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>从库添加 SAP Cloud Platform Identity Authentication

在配置 SAP 云平台标识身份验证以使用 Azure AD 进行自动用户预配之前，需要将 AZURE AD 应用程序库中的 SAP 云平台标识身份验证添加到托管 SaaS 应用程序列表中。

**要从 Azure AD 应用程序库添加 SAP 云平台标识身份验证，请执行以下步骤：**

1. 在**[Azure 门户](https://portal.azure.com)** 中，在左侧导航面板中，选择**Azure 活动目录**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。********

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的 **"新建应用程序**"按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**SAP 云平台标识身份验证**，在结果面板中选择**SAP 云平台标识身份验证**，然后单击 **"添加**"按钮以添加应用程序。

    ![结果列表中的 SAP Cloud Platform Identity Authentication](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>配置自动用户预配到 SAP 云平台标识身份验证 

本节将指导您完成配置 Azure AD 预配服务的步骤，以便根据 Azure AD 中的用户和/或组分配在 SAP 云平台标识身份验证中创建、更新和禁用用户和/或组。

> [!TIP]
> 您也可以根据[SAP 云平台身份身份验证单一登录教程](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)中提供的说明，选择启用基于 SAML 的单一登录，以实现 SAP 云平台标识身份验证。 单一登录可以独立于自动用户预配进行配置，尽管这两个功能相互补充

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>要在 Azure AD 中配置 SAP 云平台标识身份验证的自动用户预配：

1. 登录到 Azure[门户](https://portal.azure.com)。 选择**企业应用程序**，然后选择**所有应用程序**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SAP Cloud Platform Identity Authentication”****。

    ![应用程序列表中的“SAP Cloud Platform Identity Authentication”链接](common/all-applications.png)

3. 选择“预配”**** 选项卡。

    ![预配选项卡](common/provisioning.png)

4. 将**预配模式**设置为 **"自动**"。

    ![预配选项卡](common/provisioning-automatic.png)

5. 在 **"管理凭据"** 部分下`https://<tenantID>.accounts.ondemand.com/service/scim `，在**租户 URL**中输入 。 分别输入**管理员用户名**和**管理员密码**中较早检索**的用户 ID**和**密码**值。 单击 **"测试连接**"以确保 Azure AD 可以连接到 SAP 云平台标识身份验证。 如果连接失败，请确保您的 SAP 云平台标识身份验证帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”********。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。****

8. 在 **"映射**"部分下，选择**将 Azure 活动目录用户同步到 SAP 云平台标识身份验证**。

    ![SAP 云平台标识身份验证用户映射](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. 在**属性映射**部分中查看从 Azure AD 同步到 SAP 云平台标识身份验证的用户属性。 选择为**匹配**属性的属性用于匹配 SAP 云平台标识身份验证中的用户帐户以进行更新操作。 选择“保存”按钮以提交任何更改****。

    ![SAP 云平台身份身份验证用户属性](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 要为 SAP 云平台标识身份验证启用 Azure AD 预配服务，在 **"设置"** 部分将**预配状态**更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

12. 通过在 **"设置"** 部分中的 **"范围"** 中选择所需的值，定义要预配到 SAP 云平台标识身份验证的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”****。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步********。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用 **"同步详细信息"** 部分监视进度并关注指向预配活动报告的链接，该报表描述 Azure AD 预配服务在 SAP 云平台标识身份验证上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* SAP 云平台标识身份验证的 SCIM 终结点要求某些属性具有特定格式。 你可以[在这里](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)更多地了解这些属性及其特定的格式。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure 活动目录的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

