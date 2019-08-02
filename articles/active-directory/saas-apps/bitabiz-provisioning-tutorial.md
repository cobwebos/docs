---
title: 教程：为 BitaBIZ 配置自动用户预配 Azure Active Directory |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到 BitaBIZ 以及取消其预配。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d0d38abe-c041-482a-9d3f-ca340678c226
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: a143164f43f999dcb0740cfc25951882000eafbe
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641638"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 BitaBIZ

本教程的目的是演示要在 BitaBIZ 和 Azure Active Directory (Azure AD) 中执行的步骤, 以配置 Azure AD 自动将用户和/或组预配到 BitaBIZ 以及取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 有关预览功能的一般 Microsoft Azure 使用条款的详细信息, 请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [BitaBIZ 租户](https://bitabiz.dk/en/price/)。
* BitaBIZ 中具有管理员权限的用户帐户。

## <a name="assigning-users-to-bitabiz"></a>将用户分配到 BitaBIZ

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中, 只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前, 应确定 Azure AD 中哪些用户和/或组需要访问 BitaBIZ。 确定后, 可按照此处的说明将这些用户和/或组分配到 BitaBIZ:
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>将用户分配到 BitaBIZ 的重要提示

* 建议将单个 Azure AD 用户分配到 BitaBIZ 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 BitaBIZ 时, 必须在分配对话框中选择任何特定于应用程序的有效角色 (如果可用)。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="setup-bitabiz-for-provisioning"></a>设置 BitaBIZ 以进行预配

将 BitaBIZ 配置为使用 Azure AD 进行自动用户预配之前, 需要在 BitaBIZ 上启用 SCIM 设置。

1. 登录到[BitaBIZ 管理控制台](https://www.bitabiz.com/login?lang=en)。 单击“设置管理员”。

    ![BitaBIZ 管理控制台](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  导航到 "**集成**"。

    ![BitaBIZ 管理控制台](media/bitabiz-provisioning-tutorial/integration.png)

2.  导航到**Microsoft Azure AD 设置**。  选择 **"** 自动用户预配"。 复制**SCIM 预配终结点 URL**和**持有者令牌**的值。 这些值将在 Azure 门户的 BitaBIZ 应用程序的 "设置" 选项卡的 "租户 URL" 和 "机密令牌" 字段中输入。

    ![BitaBIZ 添加 SCIM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>从库中添加 BitaBIZ

若要为 BitaBIZ 配置自动用户预 Azure AD 配, 需要将 Azure AD 应用程序库中的 BitaBIZ 添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库中添加 BitaBIZ, 请执行以下步骤:**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中, 选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序, 请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中, 输入 " **BitaBIZ**", 在结果面板中选择 " **BitaBIZ** ", 然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的 BitaBIZ](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>配置 BitaBIZ 的自动用户预配 

本部分将指导你完成以下步骤: 配置 Azure AD 预配服务, 以便基于 Azure AD 中的用户和/或组分配在 BitaBIZ 中创建、更新和禁用用户和/或组。

> [!TIP]
> 你还可以选择按照[BitaBIZ 单一登录教程](BitaBIZ-tutorial.md)中提供的说明为 BitaBIZ 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录, 尽管这两个功能互相补充

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>若要在 Azure AD 中配置 BitaBIZ 的自动用户预配:

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**", 并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“BitaBIZ”。

    ![应用程序列表中的 BitaBIZ 链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "管理员凭据" 部分下, 输入**SCIM 预配终结点 URL** , 并分别在 "租户 URL" 和 "机密令牌" 中输入证书**持有者令牌**值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到 BitaBIZ。 如果连接失败, 请确保 BitaBIZ 帐户具有管理员权限, 然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。

8. 在 "**映射**" 部分下, 选择 "**将 Azure Active Directory 用户同步到 BitaBIZ**"。

    ![BitaBIZ 用户映射](media/bitabiz-provisioning-tutorial/usermapping.png)

9. 在 "**属性映射**" 部分中, 查看从 Azure AD 同步到 BitaBIZ 的用户属性。 选为 "**匹配**" 属性的特性用于匹配 BitaBIZ 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![BitaBIZ 用户属性](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为 BitaBIZ 启用 Azure AD 预配服务, 请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![设置状态切换开启](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值, 定义要预配到 BitaBIZ 的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![正在保存设置配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接, 该报告描述了 Azure AD 预配服务对 BitaBIZ 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* BitaBIZ 要求**用户名**、**电子邮件**、 **firstName**和**lastName**作为必需的属性。 
* BitaBIZ 目前不支持硬删除。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户设置](../manage-apps/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报表](../manage-apps/check-status-user-account-provisioning.md)。
