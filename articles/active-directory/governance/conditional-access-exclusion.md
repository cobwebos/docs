---
title: 管理从条件访问策略中排除的用户-Azure AD
description: 了解如何使用 Azure Active Directory （Azure AD）访问评审来管理已从条件访问策略中排除的用户
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422708"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>使用 Azure AD 访问评审来管理从条件访问策略中排除的用户

在理想情况下，所有用户都会遵循访问策略来保护对组织资源的访问。 但是，有时，某些业务案例要求例外处理。 本文介绍可能需要创建排除项的某些示例，以及 IT 管理员如何管理此任务，以避免监督策略例外项，并向审核员证明已使用 Azure Active Directory (Azure AD) 定期评审这些例外项。

> [!NOTE]
> 使用 Azure AD 访问评审需要有效的 Azure AD Premium P2、企业移动性 + 安全性 E5 付费版或试用版许可证。 有关详细信息，请参阅 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="why-would-you-exclude-users-from-policies"></a>为何要从策略中排除用户？

作为 IT 管理员，你可以使用[Azure AD 条件性访问](../conditional-access/overview.md)，要求用户使用多重身份验证（MFA）进行身份验证，或者从受信任的网络或设备登录。 在部署规划期间，你发现并非所有用户都能满足其中的某些要求。 例如，在远程办公室工作的用户不在你的内部网络中，或者主管使用不受支持的旧式手机。 企业要求这些用户登录并执行其作业，因此从条件访问策略中排除了这些用户。

作为另一个示例，你可以使用条件性访问中的[命名位置](../conditional-access/location-condition.md)来配置一组不希望允许用户访问其租户的一组县和区域。

![条件访问中的命名位置](./media/conditional-access-exclusion/named-locations.png)

但是，在某些情况下，用户可能会出于合法原因而从这些被阻止的国家/地区进行登录。 例如，用户可能因公或因私外出旅行。 在此示例中，用于阻止这些国家/地区的条件性访问策略可以为从策略中排除的用户提供专用的云安全组。 在旅行期间需要进行访问的用户可以使用 [Azure AD 自助服务组管理](../users-groups-roles/groups-self-service-management.md)将自己添加到该组。

另一个示例是，你有一个条件性访问策略，该策略会[阻止大多数用户使用旧身份验证](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)。 Microsoft 强烈建议在租户中阻止使用旧式协议，以提高安全状态。 但是，如果某些用户绝对需要使用旧式身份验证方法通过 Office 2010 或基于 IMAP/SMTP/POP 的客户端来访问你的资源，则你可以从阻止旧式身份验证方法的策略中排除这些用户。

## <a name="why-are-exclusions-challenging"></a>为何排除项会带来挑战？

在 Azure AD 中，可以将条件性访问策略的范围限定为一组用户。 你还可以通过选择 Azure AD 角色、单个用户或用户的来宾来排除其中一些用户。 请务必记住，配置这些排除项时，不能针对这些用户强制实施策略意图。 如果将这些排除项配置为包含各个用户的列表，或通过旧式本地安全组进行配置，则此策略会限制此排除列表的可见性（用户可能不知道它的存在），以及 IT 管理员对它的控制力（用户可能会加入安全组以绕过策略）。 此外，在某个时间符合排除条件的用户可能不再需要它或者后来不符合条件。

排除项的开头包含跳过策略的用户的简短列表。 随着排除的用户越来越多，该列表会不断增大。 在某些时候，需要审查该列表，并确认是否仍然应该排除其中的每个用户。 从技术角度讲，管理列表可能相对容易，但谁是业务决策人，以及如何确保该列表完全可审核？

但是，如果使用 Azure AD 组将排除配置为条件访问策略，则可以使用访问评审作为补偿控制来驱动可见性，并减少出现异常的用户的数量。

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>如何在条件性访问策略中创建排除组

按照以下步骤创建新的 Azure AD 组和不应用于该组的条件性访问策略。

### <a name="create-an-exclusion-group"></a>创建排除组

1. 登录到 Azure 门户。

1. 在左侧导航栏中，依次单击“Azure Active Directory”、“组”。

1. 在顶部菜单中，单击“新建组”打开“组”窗格。

1. 在“组类型”列表中，选择“安全性”。 指定名称和说明。

1. 请务必将“成员身份”类型设置为“已分配”。

1. 选择应包含在此排除组中的用户，然后单击“创建”。

    ![Azure Active Directory 中的新组窗格](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>创建排除组的条件性访问策略

现在可以创建使用此排除组的条件性访问策略。

1. 在左侧导航栏中，依次单击 " **Azure Active Directory** " 和 "**条件访问**"，打开 "**策略**" 边栏选项卡。

1. 单击“新建策略”打开“新建”窗格。

1. 指定名称。

1. 在“分配”下，单击“用户和组”。

1. 在“包括”选项卡上，选择“所有用户”。

1. 在“排除”选项卡上，勾选“用户和组”，然后单击“选择排除的用户”。

1. 选择创建的排除组。

    > [!NOTE]
    > 作为最佳做法，我们建议在测试时至少从策略中排除一个管理员帐户，以确保不会将你锁在租户之外。

1. 继续根据组织要求设置条件性访问策略。

    ![在条件访问中选择 "排除的用户" 窗格](./media/conditional-access-exclusion/select-excluded-users.png)

我们介绍两个示例，可在其中使用访问评审来管理条件访问策略中的排除项。

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>示例1：访问被阻止的国家/地区的用户的访问评审

假设你有一个条件性访问策略，该策略阻止了某些国家/地区的访问。 该策略排除了某个组。 下面是评审该组成员的建议访问评审方法。

> [!NOTE]
> 需要 "全局管理员" 或 "用户管理员" 角色才能创建访问评审。

1. 评审每隔一周重复进行。

2. 评审永远不会结束，以确保将此排除组保持最新状态。

3. 此组的所有成员在评审范围内。

4. 每个用户都必须对这些被阻止的国家/地区拥有访问权限，因此他们仍需要成为组的成员。

5. 如果用户未响应审核请求，则会自动从组中删除这些请求，因此，在旅行到这些国家/地区时，不能再访问该租户。

6. 启用邮件通知，让用户知道访问评审的开始和完成时间。

    ![创建访问评审窗格（例如1）](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>示例 2：对使用旧式身份验证进行访问的用户进行访问评审

假设你有一个条件性访问策略，该策略阻止用户使用旧身份验证和较旧的客户端版本进行访问。 该策略排除了某个组。 下面是评审该组成员的建议访问评审方法。

1. 此项评审需是定期评审。

2. 该组中的每个人都需要接受评审。

3. 评审可配置为将业务部门主管列作选定的评审者。

4. 自动应用结果并删除未经批准的用户，让他们继续使用旧式身份验证方法。

5. 启用建议可以帮助大型组的评审者轻松做出决策。

6. 启用邮件通知，让用户知道访问评审的开始和完成时间。

    ![创建访问评审窗格，例如2](./media/conditional-access-exclusion/create-access-review-2.png)

**专业提示**：如果你有许多的排除组，因此需要创建多个访问评审，现在可以使用 Microsoft Graph 测试版终结点中的某个 API 以编程方式创建和管理访问评审。 若要开始，请参阅 [Azure AD 访问评审 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root)和[通过 Microsoft Graph 检索 Azure AD 访问评审的示例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)。

## <a name="access-review-results-and-audit-logs"></a>访问评审结果和审核日志

现在，你已准备好所有内容、组、条件性访问策略和访问评审，接下来可以监视和跟踪这些评审的结果。

1. 在 Azure 门户中，打开“访问评审”边栏选项卡。

1. 打开创建用于管理排除组的控制措施和程序。

1. 单击“结果”查看已批准哪些人保留在该列表中，以及删除了哪些人。

    ![访问评审结果显示谁已获批准](./media/conditional-access-exclusion/access-reviews-results.png)

1. 然后单击“审核日志”查看评审期间执行的操作。

    ![访问评审审核日志列表操作](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

IT 管理员知道，管理策略的排除组有时不可避免。 但是，如果使用 Azure AD 访问评审，则业务主管或用户自己可以更轻松维护这些组、定期评审这些组以及审核所做的更改。

## <a name="next-steps"></a>后续步骤

- [创建组或应用程序的访问评审](create-access-review.md)
- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)
