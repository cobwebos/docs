---
title: 教程 - 创建访问包 - Azure AD 权利管理
description: 有关如何在 Azure Active Directory 权利管理中创建第一个访问包的分步教程。
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
ms.date: 10/22/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd4feeb83acc3842874e7a2e4bbd32dacabcc00d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422655"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>教程：在 Azure AD 权利管理中创建第一个访问包

对于组织而言，管理对员工所需的所有资源（例如组、应用程序和站点）的访问是一项非常重要的功能。 应该为员工授予适当的访问权限级别以使其保持工作效率，同时，在不再需要这种权限级别时将其删除。

本教程假设你在 Woodgrove Bank 担任 IT 管理员。 该组织要求你为某个市场营销活动创建资源包，内部用户通过自助服务请求该包。 请求不需要经过审批，用户的访问权限将在 30 天后过期。 对于本教程，市场营销活动资源只是单个组中的成员身份，但也可以是组、应用程序或 SharePoint Online 站点的集合。

![方案概述](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建包含组（用作资源）的访问包
> * 允许目录中的用户请求访问
> * 演示内部用户如何请求该访问包

有关部署 Azure Active Directory 权利管理过程（包括创建第一个访问包）的分步演示，请观看以下视频：

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>必备条件

若要使用 Azure AD 权利管理，必须具有以下某个许可证：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5 许可证

有关详细信息，请参阅[许可证要求](entitlement-management-overview.md#license-requirements)。

## <a name="step-1-set-up-users-and-group"></a>步骤 1：设置用户和组

某个资源目录包含一个或多个可共享的资源。 在此步骤中，请在 Woodgrove Bank 目录中创建一个名为“市场营销资源”的组，该组是权利管理的目标资源  。 另外设置一个内部请求者。

**必备角色：** 全局管理员或用户管理员

![创建用户和组](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. 以全局管理员或用户管理员身份登录到 [Azure 门户](https://portal.azure.com)。  

1. 在左侧导航中，单击“Azure Active Directory”  。

1. 创建或配置以下两个用户。 可以使用这些名称或其他名称。 **Admin1** 可以当前用来登录的用户。

    | 名称 | 目录角色 |
    | --- | --- |
    | **Admin1** | 全局管理员<br/>-或-<br/>用户管理员 |
    | **Requestor1** | 用户 |

1. 创建一个名为“市场营销资源”的 Azure AD 安全组，其成员身份类型为“已分配”   。

    此组是权利管理的目标资源。 该组一开始应该不包含任何成员。

## <a name="step-2-create-an-access-package"></a>步骤 2：创建访问包

访问包是团队或项目所需的且受策略制约的资源捆绑包  。 在名为 *catalogs* 的容器中定义访问包。 在此步骤中，请在“常规”目录中创建一个“市场营销活动”访问包   。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理员

![创建访问包](./media/entitlement-management-access-package-first/elm-access-package.png)

1. 在 Azure 门户的左侧导航栏中，单击“Azure Active Directory”。 

1. 在左侧菜单中，单击“标识监管”。 

1. 在左侧菜单中，单击“访问包”。   如果看到“访问被拒绝”，请确认目录中是否存在 Azure AD Premium P2 许可证  。

1. 单击“新建访问包”。 

    ![Azure 门户中的权利管理](./media/entitlement-management-shared/access-packages-list.png)

1. 在“基本信息”选项卡上，键入名称“市场营销活动”访问包和说明“访问活动的资源”    。

1. 将“目录”下拉列表保留设置为“常规”。  

    ![新建访问包 -“基本信息”选项卡](./media/entitlement-management-access-package-first/basics.png)

1. 单击“下一步”打开“资源角色”选项卡。  

    在此选项卡上，选择要包含在访问包中的资源和资源角色。

1. 单击“组和团队”  。

1. 在“选择组”窗格中，找到并选择之前创建的“市场营销资源”组  。

    默认情况下，会看到“常规”目录内部和外部的组。  选择“常规”目录外部的组时，该组将添加到“常规”目录。  

    ![新建访问包 -“资源角色”选项卡](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. 单击“选择”将该组添加到列表中。 

1. 在“角色”下拉列表中，选择“成员”。  

    ![新建访问包 -“资源角色”选项卡](./media/entitlement-management-access-package-first/resource-roles.png)

1. 单击“下一步”，打开“请求”选项卡   。

    在此选项卡上，你将创建请求策略。 策略定义有关访问某个访问包的规则或准则。  请创建一个使资源目录中的特定用户能够请求此访问包的策略。

1. 在“可以请求访问的用户”部分，单击“你目录中的用户”，再单击“特定用户和组”    。

    ![新建访问包 -“请求”选项卡](./media/entitlement-management-access-package-first/requests.png)

1. 单击“添加用户和组”  。

1. 在“选择用户和组”窗格中，选择前面创建的“Requestor1”用户  。

    ![新建访问包 -“请求”选项卡 - 选择用户和组](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. 单击“选择”  。

1. 向下滚动到“批准”和“启用请求”部分   。

1. 请将“需要批准”设置为“否”   。

1. 对于“启用请求”，单击“是”，使此访问包在创建后立即获得请求   。

    ![新建访问包 -“请求”选项卡 - 批准和启用请求](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. 单击“下一步”，打开“生命周期”选项卡   。

1. 在“过期时间”部分，将“访问包分配过期时间”设置为“天数”    。

1. 请将“分配过期时间”设置为 30 天   。

    ![新建访问包 -“生命周期”选项卡](./media/entitlement-management-access-package-first/lifecycle.png)

1. 单击“下一步”打开“查看 + 创建”选项卡。  

    ![新建访问包 -“查看 + 创建”选项卡](./media/entitlement-management-access-package-first/review-create.png)

    几分钟后，应会看到一条通知指出已成功创建访问包。

1. 在“市场营销活动”访问包的左侧菜单中，单击“概述”  。

1. 复制“我的访问权限门户链接”。 

    在下一步骤中将要使用此链接。

    ![访问包概述 - 我的访问权限门户链接](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>步骤 3：请求访问权限

在此步骤中，你将**内部请求者**的身份执行步骤，并请求对访问包进行访问。 请求者使用名为“我的访问权限门户”的站点来提交其请求。 在“我的访问权限门户”中，请求者可以提交访问包的请求、查看他们已有权访问的访问包，以及查看其请求历史记录。

**必备角色：** 内部请求者

1. 从 Azure 门户注销。

1. 在新浏览器窗口中，导航到在前面步骤中复制的“我的访问权限门户链接”。

1. 以 **Requestor1** 身份登录到“我的访问权限门户”。

    你应会看到“市场营销活动”访问包  。

1. 如果需要，请在“说明”列中，单击箭头查看有关该访问包的详细信息。 

    ![我的访问权限门户 - 访问包](./media/entitlement-management-shared/my-access-access-packages.png)

1. 单击勾选标记选中该包。

1. 单击“请求访问”打开“请求访问”面板。 

    ![我的访问权限门户 -“请求访问”按钮](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. 在“业务理由”框中，键入理由“正在处理新的市场营销活动”   。

    ![我的访问权限门户 - 请求访问](./media/entitlement-management-shared/my-access-request-access.png)

1. 单击“提交”  。

1. 在左侧菜单中，单击“请求历史记录”以确认请求是否已提交。 

## <a name="step-4-validate-that-access-has-been-assigned"></a>步骤 4：验证是否已分配访问权限

在此步骤中，请确认是否为“内部请求者”分配了访问包，并且他们现在是否是“市场营销资源”组的成员   。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理员

1. 从“我的访问权限门户”注销。

1. 以 Admin1 的身份登录 [Azure 门户](https://portal.azure.com)  。

1. 依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击“访问包”。 

1. 找到并单击“市场营销活动”访问包  。

1. 在左侧菜单中，单击“请求”。 

    你应会看到 Requestor1，以及状态为“已交付”的初始策略  。

1. 单击该请求查看其详细信息。

    ![访问包 - 请求详细信息](./media/entitlement-management-access-package-first/request-details.png)

1. 在左侧导航中，单击“Azure Active Directory”  。

1. 单击“组”并打开“市场营销资源”组   。

1. 单击“成员”  。

    应会看到，**Requestor1** 已列为成员。

    ![市场营销资源成员](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>步骤 5：清理资源

在此步骤中，请删除所做的更改并删除“市场营销活动”访问包  。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 打开“市场营销活动”访问包  。

1. 单击“分配”。 

1. 对于“Requestor1”，请依次单击省略号 ( **...** )、“删除访问权限”。   在出现的消息框中单击“是”  。

    几分钟后，状态将从“已交付”更改为“已过期”。

1. 单击“资源角色”。 

1. 对于“市场营销资源”，请单击省略号 (…)，再单击“删除资源角色”    。 在出现的消息框中单击“是”  。

1. 打开访问包列表。

1. 对于“市场营销活动”，请单击省略号 (…)，再单击“删除”    。 在出现的消息框中单击“是”  。

1. 在 Azure Active Directory 中，删除创建的所有用户，例如 **Requestor1** 和 **Admin1**。

1. 删除“市场营销资源”组  。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解权利管理中常用的方案步骤。
> [!div class="nextstepaction"]
> [常见方案](entitlement-management-scenarios.md)
