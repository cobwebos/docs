---
title: 教程 - 在 Azure AD 权利管理（预览版）中创建第一个访问包- Azure Active Directory
description: 有关如何在 Azure Active Directory 权利管理（预览版）中创建第一个访问包的分步教程。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489167"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>教程：在 Azure AD 权利管理（预览版）中创建第一个访问包

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

对于组织而言，管理对员工所需的所有资源（例如组、应用程序和站点）的访问是一项非常重要的功能。 应该为员工授予适当的访问权限级别以使其保持工作效率，同时，在不再需要这种权限级别时将其删除。

本教程假设你在 Woodgrove Bank 担任 IT 管理员。 该组织要求你为某个 Web 项目创建一个资源包，内部用户可以通过自助服务请求该包。 请求需要经过审批，用户的访问权限将在 30 天后过期。 在本教程中，Web 项目资源只是单个组中的成员身份，但它可以是组、应用程序或 SharePoint Online 站点的集合。

![方案概述](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建包含组（用作资源）的访问包
> * 指定审批者
> * 演示内部用户如何请求该访问包
> * 审批访问请求

如果你没有 Azure AD Premium P2 或企业移动性 + 安全性 E5 许可证，请创建一个免费的[企业移动性 + 安全性 E5 试用版](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)。

## <a name="prerequisites"></a>先决条件

若要使用 Azure AD 权利管理（预览版），必须有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5 许可证

## <a name="step-1-set-up-users-and-group"></a>步骤 1：设置用户和组

某个资源目录包含一个或多个可共享的资源。 此步骤在 Woodgrove Bank 目录中创建一个名为“工程组”的组，该组是权利管理的目标资源。  另外设置一个内部请求者。

**必备角色：** 全局管理员或用户管理员

![创建用户和组](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. 以全局管理员或用户管理员身份登录到 [Azure 门户](https://portal.azure.com)。  

1. 在左侧导航中，单击“Azure Active Directory”  。

1. 创建或配置以下两个用户。 可以使用这些名称或其他名称。 **Admin1** 可以当前用来登录的用户。

    | Name | 目录角色 | 说明 |
    | --- | --- | --- |
    | **Admin1** | 全局管理员<br/>-或-<br/>受限管理员（用户管理员） | 管理员和审批者 |
    | **Requestor1** | 用户 | 内部请求者 |

    在本教程中，管理员和审批者是同一个人，但你通常会指定一人或多人充当审批者。

1. 使用成员身份类型“已分配”创建名为“工程组”的 Azure AD 安全组。  

    此组是权利管理的目标资源。 该组一开始应该不包含任何成员。

## <a name="step-2-create-an-access-package"></a>步骤 2：创建访问包

访问包是用户在处理项目或执行其作业时所需的所有资源的捆绑包。  在名为 *catalogs* 的容器中定义访问包。 此步骤在“常规”目录中创建一个 **Web 项目访问包**。 

**必备角色：** 全局管理员或用户管理员

![创建访问包](./media/entitlement-management-access-package-first/elm-access-package.png)

1. 在 Azure 门户的左侧导航栏中，单击“Azure Active Directory”。 

1. 在左侧菜单中，单击“标识监管”。 

1. 在左侧菜单中，单击“访问包”。   如果看到“访问被拒绝”，请确保该目录中存在 Azure AD Premium P2 许可证。 

1. 单击“新建访问包”。 

    ![Azure 门户中的权利管理](./media/entitlement-management-access-package-first/access-packages-list.png)

1. 在“基本信息”选项卡上，键入名称“Web 项目访问包”和说明“工程 Web 项目的访问包”。   

1. 将“目录”下拉列表保留设置为“常规”。  

    ![新建访问包 -“基本信息”选项卡](./media/entitlement-management-access-package-first/basics.png)

1. 单击“下一步”打开“资源角色”选项卡。  

    在此选项卡上，选择要包含在访问包中的权限。

1. 单击“组”。 

1. 在“选择组”窗格中，找到并选择前面创建的“工程组”组。 

    默认情况下，会看到“常规”目录内部和外部的组。  选择“常规”目录外部的组时，该组将添加到“常规”目录。  

    ![新建访问包 -“资源角色”选项卡](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. 单击“选择”将该组添加到列表中。 

1. 在“角色”下拉列表中，选择“成员”。  

    ![新建访问包 -“资源角色”选项卡](./media/entitlement-management-access-package-first/resource-roles.png)

1. 单击“下一步”打开“策略”选项卡。  

1. 将“创建第一个策略”切换开关设置为“稍后”。  

    将在下一部分创建该策略。

    ![新建访问包 -“策略”选项卡](./media/entitlement-management-access-package-first/policy.png)

1. 单击“下一步”打开“查看 + 创建”选项卡。  

    ![新建访问包 -“查看 + 创建”选项卡](./media/entitlement-management-access-package-first/review-create.png)

1. 检查访问包设置，然后单击“创建”。 

    可能会有一条消息指出，由于尚未启用该目录，该访问包对用户将不可见。

    ![新建访问包 -“不可见”消息](./media/entitlement-management-access-package-first/not-visible.png)

1. 单击“确定”。 

    几分钟后，应会看到一条通知指出已成功创建访问包。

## <a name="step-3-create-a-policy"></a>步骤 3：创建策略

策略定义有关访问某个访问包的规则或准则。  此步骤将创建一个策略，以允许资源目录中的特定用户请求访问包。 另外，指定该请求必须经过审批，以及谁是审批者。

![创建访问包策略](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**必备角色：** 全局管理员或用户管理员

1. 在“Web 项目访问包”的左侧菜单中，单击“策略”。  

    ![访问包策略列表](./media/entitlement-management-access-package-first/policies-list.png)

1. 单击“添加策略”打开“创建策略”。 

1. 键入名称“内部请求者策略”和说明“允许此目录中的用户请求访问 Web 项目资源”。  

1. 在“可以请求访问的用户”部分，单击“你目录中的用户”。  

    ![创建策略](./media/entitlement-management-access-package-first/policy-create.png)

1. 向下滚动到“选择用户和组”部分，然后单击“添加用户和组”。  

1. 在“选择用户和组”窗格中，选择前面创建的 **Requestor1** 用户，然后单击“选择”。 

1. 在“请求”部分，将“需要审批”设置为“是”。   

1. 在“选择审批者”部分，单击“添加审批者”。  

1. 在“选择审批者”窗格中，选择前面创建的 **Admin1**，然后单击“选择”。 

    在本教程中，管理员和审批者是同一个人，但你可以指定另一个人充当审批者。

1. 在“过期”部分，将“访问包过期时间”设置为“天数”。   

1. 将“访问权限过期时间”设置为 **30** 天。 

1. 对于“启用策略”，请单击“是”。  

    ![创建策略设置](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. 单击“创建”以创建“内部请求者策略”。  

1. 在 Web 项目访问包的左侧菜单中，单击“概述”。 

1. 复制“我的访问权限门户链接”。 

    在下一步骤中将要使用此链接。

    ![访问包概述 - 我的访问权限门户链接](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>步骤 4：请求访问权限

在此步骤中，你将**内部请求者**的身份执行步骤，并请求对访问包进行访问。 请求者使用名为“我的访问权限门户”的站点来提交其请求。 在“我的访问权限门户”中，请求者可以提交访问包的请求、查看他们已有权访问的访问包，以及查看其请求历史记录。

**必备角色：** 内部请求者

1. 从 Azure 门户注销。

1. 在新浏览器窗口中，导航到在前面步骤中复制的“我的访问权限门户链接”。

1. 以 **Requestor1** 身份登录到“我的访问权限门户”。

    应会看到“Web 项目访问包”。 

1. 如果需要，请在“说明”列中，单击箭头查看有关该访问包的详细信息。 

    ![我的访问权限门户 - 访问包](./media/entitlement-management-shared/my-access-access-packages.png)

1. 单击勾选标记选中该包。

1. 单击“请求访问”打开“请求访问”面板。 

1. 在“业务理由”框中，键入理由“处理 Web 项目”。  

1. 将“请求在特定的时间段访问”切换开关设置为“是”。  

1. 将“开始日期”设置为当天，将“结束日期”设置为下一日。  

    ![我的访问权限门户 - 请求访问](./media/entitlement-management-shared/my-access-request-access.png)

1. 单击“提交”  。

1. 在左侧菜单中，单击“请求历史记录”以确认请求是否已提交。 

## <a name="step-5-approve-access-request"></a>步骤 5：审批访问请求

在此步骤中，你将以**审批者**用户的身份登录，然后审批内部请求者的访问请求。 审批者使用请求者在提交请求时所用的同一个“我的访问权限门户”。 审批者可以使用“我的访问权限门户”查看等待处理的审批，以及批准或拒绝请求。

**必备角色：** 审批者

1. 从“我的访问权限门户”注销。

1. 以 **Admin1** 身份登录到[我的访问权限门户](https://myaccess.microsoft.com)。

1. 在左侧菜单中，单击“审批”。 

1. 在“等待处理”选项卡上找到“Requestor1”。  

    如果未看到来自 Requestor1 的请求，请等待几分钟，然后重试。

1. 单击“查看”链接打开“访问请求”窗格。 

1. 单击“批准”  。

1. 在“原因”框中，键入原因“已批准对 Web 项目的访问”。  

    ![我的访问权限门户 - 访问请求](./media/entitlement-management-shared/my-access-approve-request.png)

1. 单击“提交”以提交所做的决定。 

    此时应有一条消息指出审批成功。

## <a name="step-6-validate-that-access-has-been-assigned"></a>步骤 6：验证是否已分配访问权限

批准访问请求后，请通过此步骤确认是否为**内部请求者**分配了该访问包，并且他们现在是否为“工程组”组的成员。 

**必备角色：** 全局管理员或用户管理员

1. 从“我的访问权限门户”注销。

1. 以 **Admin1** 的身份登录到 [Azure 门户](https://portal.azure.com)。

1. 依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击“访问包”。 

1. 找到并单击“Web 项目访问包”。 

1. 在左侧菜单中，单击“请求”。 

    应会看到 Requestor1，以及状态为“已传送”的内部请求者策略。 

1. 单击该请求查看其详细信息。

    ![访问包 - 请求详细信息](./media/entitlement-management-access-package-first/request-details.png)

1. 在左侧导航中，单击“Azure Active Directory”  。

1. 单击“组”并打开“工程组”组。  

1. 单击“成员”  。

    应会看到，**Requestor1** 已列为成员。

    ![工程组成员](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>步骤 7：清理资源

在此步骤中，你将删除所做的更改并删除“Web 项目访问包”访问包。 

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 打开“Web 项目访问包”。 

1. 单击“分配”。 

1. 对于“Requestor1”，请依次单击省略号 ( **...** )、“删除访问权限”。  

    状态将从“已传送”更改为“已过期”。

1. 单击“策略”。 

1. 对于“内部请求者策略”，请依次单击省略号 ( **...** )、“删除”。  

1. 单击“资源角色”。 

1. 对于“工程组”，请依次单击省略号 ( **...** )、“删除资源角色”。  

1. 打开访问包列表。

1. 对于“Web 项目访问项目”，请依次单击省略号 ( **...** )、“删除”。  

1. 在 Azure Active Directory 中，删除创建的所有用户，例如 **Requestor1** 和 **Admin1**。

1. 删除“工程组”组。 

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解权利管理中常用的方案步骤。
> [!div class="nextstepaction"]
> [常见方案](entitlement-management-scenarios.md)
