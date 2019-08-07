---
title: 教程：为 Azure Active Directory 的自动用户预配配置奖励网关 |Microsoft Docs
description: 了解如何配置 Azure Active Directory 以自动将用户帐户预配到奖励网关和取消其预配。
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
ms.author: zhchia
ms.openlocfilehash: 4d07fa66374908781fd1d637fd722fd60d1352b3
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737870"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>教程：为自动用户预配配置奖励网关

本教程的目的是演示要在奖励网关上执行的步骤, 并 Azure Active Directory (Azure AD) 将 Azure AD 配置为自动将用户和/或组预配到奖励网关并取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。
>
> 此连接器当前为公共预览版。 有关预览功能的一般 Microsoft Azure 使用条款的详细信息, 请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [奖励网关租户](https://www.rewardgateway.com/)。
* 具有管理员权限的奖励网关上的用户帐户。

## <a name="assigning-users-to-reward-gateway"></a>将用户分配到奖励网关 

Azure Active Directory 使用称为 "*分配*" 的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中, 只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前, 应决定 Azure AD 中哪些用户和/或组需要访问奖励网关。 确定后, 可以按照[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)中的说明将这些用户和/或组分配到奖励网关。


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>将用户分配到奖励网关的重要提示 

* 建议将单个 Azure AD 用户分配到奖励网关, 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 将用户分配到奖励网关时, 必须在分配对话框中选择任何特定于应用程序的有效角色 (如果可用)。 具有“默认访问权限”角色的用户排除在预配之外。

## <a name="setup-reward-gateway--for-provisioning"></a>设置奖励用于预配的网关
在将奖励网关配置为使用 Azure AD 进行自动用户预配之前, 需要在奖励网关上启用 SCIM 预配。

1. 登录到[奖励网关管理员控制台](https://rewardgateway.photoshelter.com/login/)。 单击“集成”。

    ![奖励网关管理控制台](media/reward-gateway-provisioning-tutorial/image00.png)

2.  选择 **"我的集成"** 。

    ![奖励网关管理控制台](media/reward-gateway-provisioning-tutorial/image001.png)

3.  复制**SCIM URL (v2)** 和**OAuth 持有者令牌**的值。 这些值将输入到 "租户 URL" 中的 "租户 URL" 和 "机密令牌" 字段中的 "设置" 选项卡上的 "Azure 门户中。

    ![奖励网关管理控制台](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>从库中添加 Reward Gateway

若要为 Azure AD 配置自动用户预配的奖励网关, 需要将 Azure AD 应用程序库中的奖励网关添加到托管的 SaaS 应用程序列表。

**若要从 Azure AD 应用程序库添加奖励网关, 请执行以下步骤:**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中, 选择 " **Azure Active Directory**"。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序, 请选择窗格顶部的 "**新建应用程序**" 按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中, 输入**奖励网关**, 在结果面板中选择 "**奖励网关**", 然后单击 "**添加**" 按钮添加该应用程序。

    ![结果列表中的“Reward Gateway”](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>配置自动用户预配到奖励网关  

本部分将指导你完成以下步骤: 配置 Azure AD 预配服务, 以便基于 Azure AD 中的用户和/或组分配在奖励网关上创建、更新和禁用用户和/或组。

> [!TIP]
> 你还可以选择根据 "[奖励网关单一登录" 教程](reward-gateway-tutorial.md)中提供的说明为奖励网关启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>若要为 Azure AD 中的奖励网关配置自动用户预配:

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择 "**企业应用程序**", 并选择 "**所有应用程序**"。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Reward Gateway”。

    ![“应用程序”列表中的“Reward Gateway”链接](common/all-applications.png)

3. 选择“预配”选项卡。

    ![设置选项卡](common/provisioning.png)

4. 将“预配模式”设置为“自动”。

    ![设置选项卡](common/provisioning-automatic.png)

5. 在 "**管理员凭据**" 部分下, 输入之前在**租户 URL**和**机密令牌**中检索的**SCIM URL (v2)** 和**OAuth 持有者令牌**值。 单击 "**测试连接**" 以确保 Azure AD 可以连接到奖励网关。 如果连接失败, 请确保你的奖励网关帐户具有管理员权限, 然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

6. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”。

    ![通知电子邮件](common/provisioning-notification-email.png)

7. 单击“保存”。

8. 在 "**映射**" 部分下, 选择 "**将 Azure Active Directory 用户同步到奖励网关**"。

    ![奖励网关管理控制台](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. 在 "**属性映射**" 部分中, 查看从 Azure AD 同步到奖励网关的用户属性。 选为 "**匹配**" 属性的属性用于匹配奖励网关中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

    ![奖励网关管理控制台](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

11. 若要为奖励网关启用 Azure AD 预配服务, 请在 "**设置**" 部分中将 "**预配状态**" 更改为 **"打开**"。

    ![设置状态切换开启](common/provisioning-toggle-on.png)

12. 通过在 "**设置**" 部分的 "**范围**" 中选择所需的值, 定义要预配到奖励网关的用户和/或组。

    ![预配范围](common/provisioning-scope.png)

13. 已准备好预配时，单击“保存”。

    ![正在保存设置配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 你可以使用 "**同步详细信息**" 部分监视进度并跟踪指向预配活动报告的链接, 该报告描述了在奖励网关上 Azure AD 预配服务执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

奖励网关当前不支持组设置。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

[了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
