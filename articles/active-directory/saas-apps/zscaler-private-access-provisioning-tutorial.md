---
title: 教程：使用 Azure Active Directory 配置 Zscaler Private Access （ZPA）以实现自动用户预配 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 Zscaler Private Access （ZPA）和取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064132"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Zscaler Private Access （ZPA）

本教程的目的是演示要在 Zscaler Private Access （ZPA）中执行的步骤，并 Azure Active Directory （Azure AD）将 Azure AD 配置为自动将用户和/或组预配到 Zscaler Private Access （ZPA）以及取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户
* [Zscaler Private Access （ZPA）租户](https://www.zscaler.com/pricing-and-plans#contact-us)
* 具有管理员权限的 Zscaler Private Access （ZPA）中的用户帐户。

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>将用户分配到 Zscaler Private Access （ZPA）

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应决定 Azure AD 中哪些用户和/或组需要访问 Zscaler Private Access （ZPA）。 确定后，可按照此处的说明将这些用户和/或组分配到 Zscaler Private Access （ZPA）：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>将用户分配到 Zscaler Private Access （ZPA）的重要提示

* 建议将单个 Azure AD 用户分配到 Zscaler Private Access （ZPA）来测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 Zscaler Private Access （ZPA）时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果可用）。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>设置用于预配的 Zscaler Private Access （ZPA）

1. 登录到[Zscaler Private Access （ZPA）管理控制台](https://admin.private.zscaler.com/)。 导航到 "**管理" > IdP 配置**"。

    ![Zscaler Private Access （ZPA）管理控制台](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  验证以确保配置了 IdP for**单一登录**。 如果未设置 IdP，则通过单击屏幕右上角的加号图标来添加一个。

    ![Zscaler Private Access （ZPA）添加 SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. 按照 "**添加 IdP 配置**向导" 添加 IdP。 将 "**单一登录**" 字段设置为 "**用户**"。 提供一个**名称**，然后从下拉列表中选择**域**。 单击 "**下一步**" 以导航到下一个窗口。

    ![Zscaler Private Access （ZPA）添加 IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. 下载**服务提供商证书**。 单击 "**下一步**" 以导航到下一个窗口。

    ![Zscaler Private Access （ZPA） SP 证书](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. 在下一个窗口中，上传先前下载的**服务提供商证书**。

    ![Zscaler Private Access （ZPA）上传证书](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  向下滚动以提供 "**单一登录 URL** " 和 " **IdP 实体 ID**"。

    ![Zscaler Private Access （ZPA） IdP ID](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  向下滚动以**启用 SCIM 同步**。单击 "**生成新令牌**" 按钮。 复制**持有者令牌**。 此值将在 Azure 门户中的 Zscaler Private Access （ZPA）应用程序的 "预配" 选项卡的 "机密令牌" 字段中输入。

    ![Zscaler Private Access （ZPA） Create Token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  若要查找**租户 URL** ，请导航到 "**管理" > IdP 配置**"。 单击页面上列出的新添加的 IdP 配置的名称。

    ![Zscaler Private Access （ZPA） Idp Name](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  向下滚动以查看页面末尾的**SCIM 服务提供程序终结点**。 复制**SCIM 服务提供程序终结点**。 此值将在 Azure 门户中的 Zscaler Private Access （ZPA）应用程序的 "预配" 选项卡的 "租户 URL" 字段中输入。

    ![Zscaler Private Access （ZPA） SCIM URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>从库中添加 Zscaler Private Access （ZPA）

在配置 Zscaler Private Access （ZPA）以使用 Azure AD 进行自动用户预配之前，需要将 Azure AD 应用程序库中的 Zscaler Private Access （ZPA）添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 Zscaler Private Access （ZPA），请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 " **Zscaler Private access （ZPA）** "，在结果面板中选择 " **Zscaler PRIVATE access （ZPA）** "，然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的 Zscaler Private Access (ZPA)](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>配置自动用户预配到 Zscaler Private Access （ZPA） 

本部分将指导你完成配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配来创建、更新和禁用用户和/或组的步骤。

> [!TIP]
> 你还可以根据[Zscaler Private access （ZPA）单一登录教程](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)中提供的说明，选择启用基于 SAML 的单一登录的 Zscaler Private ACCESS （ZPA）。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

> [!NOTE]
> 若要了解有关 Zscaler 专用访问的 SCIM 终结点的详细信息，请参阅[此](https://www.zscaler.com/partners/microsoft)。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>若要在 Azure AD 中配置 Zscaler Private Access （ZPA）的自动用户预配，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**"，并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zscaler Private Access (ZPA)”。

    ![应用程序列表中的“Zscaler Private Access (ZPA)”链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分中，输入先前在**租户 URL**中检索到的**SCIM Service 提供程序终结点**值。 输入先前在**机密令牌**中检索的**持有者令牌**值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 Zscaler Private ACCESS （ZPA）。 如果连接失败，请确保 Zscaler Private Access （ZPA）帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击 **“保存”** 。

8. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 用户同步到 Zscaler PRIVATE Access （ZPA）** "。

    ![Zscaler Private Access （ZPA）用户映射](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Zscaler Private ACCESS （ZPA）的用户属性。 选为 "**匹配**" 属性的属性用于匹配 Zscaler Private ACCESS （ZPA）中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Zscaler Private Access （ZPA）用户属性](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. 在 "**映射**" 部分下，选择 "**将 Azure Active Directory 组同步到 Zscaler PRIVATE Access （ZPA）** "。

    ![Zscaler Private Access （ZPA）组映射](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. 在 "**属性映射**" 部分中，查看从 Azure AD 同步到 Zscaler Private ACCESS （ZPA）的组属性。 选为 "**匹配**" 属性的属性用于匹配 Zscaler Private ACCESS （ZPA）中的组以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![Zscaler Private Access （ZPA）组属性](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

13. 若要为 Zscaler Private Access （ZPA）启用 Azure AD 预配服务，请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![预配状态已打开](common/provisioning-toggle-on.png)

14. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值，定义要预配到 ZSCALER Private Access （ZPA）的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

15. 已准备好预配时，单击“保存”。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务在 Zscaler Private ACCESS （ZPA）上执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)

