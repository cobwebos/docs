---
title: 教程：配置元网络连接器以通过 Azure Active Directory 进行自动用户预配 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到元网络连接器和取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 40927597031205b5fb1bcc5869922bcc4f3f265c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518728"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>教程：为自动用户预配配置元网络连接器

本教程的目的是演示要在元网络连接器和 Azure Active Directory （Azure AD）中执行的步骤，以将 Azure AD 自动预配和取消预配到元网络连接器。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 有关预览功能的一般 Microsoft Azure 使用条款的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备组件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [元网络连接器租户](https://www.metanetworks.com/)
* 具有管理员权限的元网络连接器中的用户帐户。

## <a name="assigning-users-to-meta-networks-connector"></a>将用户分配到元网络连接器

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应决定 Azure AD 中哪些用户和/或组需要访问元网络连接器。 确定后，可按照此处的说明将这些用户和/或组分配到元网络连接器：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>将用户分配到元网络连接器的重要提示

* 建议将单个 Azure AD 用户分配到元网络连接器，以便测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到元网络连接器时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="setup-meta-networks-connector-for-provisioning"></a>设置元网络连接器以进行预配

1. 使用组织名称登录到[元网络连接器管理控制台](https://login.metanetworks.com/login/)。 导航到 "**管理" > API 密钥**"。

    ![元网络连接器管理控制台](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  单击屏幕右上角的加号以创建新的**API 密钥**。

    ![元网络连接器加号图标](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  设置**Api 密钥名称**和**api 密钥说明**。

    ![元网络连接器创建令牌](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  启用**组**和**用户**的**写入**权限。

    ![元网络连接器特权](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  单击“添加”。 复制并保存**机密**，因为这只是你可以查看它的唯一时间。 此值将在 Azure 门户的元网络连接器应用程序的 "预配" 选项卡的 "机密令牌" 字段中输入。

    ![元网络连接器创建令牌](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  通过导航到 "管理" **> 设置 > IdP >** 新建 "来添加 IdP。

    ![元网络连接器添加 IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  在**IdP 配置**页中，可以**命名**IdP 配置并选择一个**图标**。

    ![元网络连接器 IdP 名称](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![元网络连接器 IdP 图标](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  在 "**配置 SCIM** " 下，选择在前面的步骤中创建的 API 密钥名称。 单击“保存”。

    ![元网络连接器配置 SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  导航到 **"管理" > 设置 "> IdP" 选项卡**。单击在前面的步骤中创建的 IdP 配置的名称，以查看**IDP ID**。 在 Azure 门户的元网络连接器应用程序的 "预配" 选项卡中输入 "**租户 url** " 字段中的值时，会将此**ID**添加到**租户 url**的末尾。

    ![元网络连接器 IdP ID](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>从库中添加元网络连接器

在为 Azure AD 的自动用户预配配置元网络连接器之前，需要将 Azure AD 应用程序库中的元网络连接器添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库添加元网络连接器，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 "**元网络连接器**"，在结果面板中选择 "**元网络连接器**"，然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的 Meta Networks 连接器](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>配置自动用户预配到元网络连接器 

本部分将指导你完成以下步骤：配置 Azure AD 预配服务，以便基于 Azure AD 中的用户和/或组分配在元网络连接器中创建、更新和禁用用户和/或组。

> [!TIP]
> 你还可以选择为元网络连接器启用基于 SAML 的单一登录，按照[元网络连接器单一登录教程](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)中提供的说明进行操作。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>若要为 Azure AD 中的元网络连接器配置自动用户预配：

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Meta Networks 连接器”。

    ![应用程序列表中的 Meta Networks 连接器链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下的 "**租户 URL**" 中输入 `https://api.metanetworks.com/v1/scim/<IdP ID>`。 输入先前在**机密令牌**中检索到的**SCIM Authentication 令牌**值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到元网络连接器。 如果连接失败，请确保元网络连接器帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到元网络连接器**"。

    ![元网络连接器用户映射](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到元网络连接器的用户属性。 选为 "**匹配**" 属性的属性用于匹配 "元网络连接器" 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![元网络连接器用户属性](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到元网络连接器**"。

    ![元网络连接器组映射](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到元网络连接器的组属性。 选为 "**匹配**" 属性的属性用于匹配 "元网络连接器" 中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![元网络连接器组属性](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为元网络连接器启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![设置状态切换开启](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到元网络连接器的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![正在保存设置配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务在元网络连接器上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)

