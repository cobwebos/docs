---
title: 管理从条件访问策略中排除的用户-Azure AD
description: 了解如何使用 Azure Active Directory (Azure AD) 访问评审管理已从条件访问策略中排除的用户
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91bb5a342eea079b6e9abcf109ad472151d3c13d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144503"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>使用 Azure AD 访问评审管理从条件访问策略中排除的用户

在理想情况下，所有用户都遵循访问策略来确保对组织资源的访问权限。 但是，有时，某些业务案例要求例外处理。 本文介绍了一些可能需要排除的情况的示例。 作为 IT 管理员，你可以管理此任务，避免发生策略异常，并向审计员提供证明这些例外是使用 Azure Active Directory （Azure AD）访问评审定期检查的。

>[!NOTE]
> 使用 Azure AD 访问评审需要有效的 Azure AD Premium P2、企业移动性 + 安全性 E5 付费版或试用版许可证。 有关详细信息，请参阅[Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="why-would-you-exclude-users-from-policies"></a>为何要从策略中排除用户？

假设你作为管理员，你决定使用[Azure AD 条件访问](../conditional-access/overview.md)来要求执行多重身份验证（MFA），并将身份验证请求限制为特定的网络或设备。 在部署规划期间，你认识到并非所有用户都能满足这些要求。 例如，你可能有使用远程办公室的用户，而不是内部网络的一部分。 还可能需要在等待设备被替换时，使用不受支持的设备进行连接的用户。 简而言之，企业需要这些用户登录并执行其作业，以便将其从条件访问策略中排除。

作为另一个示例，你可能在条件访问中使用[命名位置](../conditional-access/location-condition.md)来指定你不希望允许用户访问其租户的一组国家和地区。

![条件访问中的命名位置](./media/conditional-access-exclusion/named-locations.png)

遗憾的是，某些用户可能仍有从这些被阻止的国家/地区登录的有效理由。 例如，用户可以出差工作，并需要访问公司资源。 在这种情况下，用于阻止这些国家/地区的条件性访问策略可以为策略中的已排除用户使用云安全组。 在旅行期间需要进行访问的用户可以使用 [Azure AD 自助服务组管理](../users-groups-roles/groups-self-service-management.md)将自己添加到该组。

另一个示例是，你有一个条件性访问策略，该策略会[阻止大多数用户使用旧身份验证](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)。 但是，如果您有一些用户需要使用旧身份验证方法通过 Office 2010 或 IMAP/SMTP/基于 POP 的客户端访问资源，则您可以将这些用户从阻止旧身份验证方法的策略中排除。

>[!NOTE]
>Microsoft 强烈建议在租户中阻止使用旧式协议，以提高安全状态。

## <a name="why-are-exclusions-challenging"></a>为何排除项会带来挑战？

在 Azure AD 中，可将条件访问策略限定为一组用户。 还可以通过选择 "Azure AD 角色"、"单个用户" 或 "来宾" 来配置排除项。 请记住，在配置排除项后，不能在排除的用户上强制执行策略意向。 如果使用用户列表或旧的本地安全组来配置排除项，则不会看到排除项。 因此：

- 用户可能不知道它们已被排除。

- 用户可以加入安全组来绕过策略。

- 排除的用户可能在之前进行了排除，但可能无法再对其进行限定。

通常，在首次配置排除时，会有搜索的用户绕过此策略。 随着时间的推移，越来越多的用户将添加到排除项中，列表也会增长。 在某些时候，你需要查看列表，并确认其中每个用户仍有资格进行排除。 从技术角度来管理排除列表可能比较简单，但谁做出了业务决策，以及如何确保所有人都可以审核？ 但是，如果使用 Azure AD 组配置排除，则可以使用访问评审作为补偿控制来驱动可见性，并减少已排除的用户的数量。

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>如何在条件访问策略中创建排除组

请遵循以下步骤创建新的 Azure AD 组，以及不会应用到该组的条件访问策略。

### <a name="create-an-exclusion-group"></a>创建排除组

1. 登录到 Azure 门户。

2. 在左侧导航栏中，依次单击“Azure Active Directory”、“组”********。

3. 在顶部菜单中，单击“新建组”打开“组”窗格。****

4. 在“组类型”列表中，选择“安全性”。******** 指定名称和说明。

5. 请务必将“成员身份”类型设置为“已分配”。********

6. 选择应包含在此排除组中的用户，然后单击“创建”。****

![Azure Active Directory 中的“新建组”窗格](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>创建排除该组的条件访问策略

现在，可以创建使用此排除组的条件访问策略。

1. 在左侧导航栏中，依次单击 " **Azure Active Directory** " 和 "**条件访问**"，打开 "**策略**" 边栏选项卡。

2. 单击“新建策略”打开“新建”窗格。********

3. 指定名称。

4. 在“分配”下，单击“用户和组”。****

5. 在“包括”选项卡上，选择“所有用户”。********

6. 在 "**排除**" 选项卡上，在 "**用户和组**" 中添加复选标记，然后单击 "**选择排除的用户**"。

7. 选择创建的排除组。

   > [!NOTE] 
   > 作为最佳做法，我们建议在测试时至少从策略中排除一个管理员帐户，以确保不会将你锁在租户之外。

8. 根据组织的要求继续设置条件访问策略。

![条件访问中的“选择排除的用户”窗格](./media/conditional-access-exclusion/select-excluded-users.png)
  
让我们通过两个示例来了解可在哪种情况下使用访问评审管理条件访问策略中的排除项。

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>示例1：访问被阻止的国家/地区的用户的访问评审

假设某个条件访问策略会阻止来自特定国家/地区的访问。 该策略排除了某个组。 下面是评审该组成员的建议访问评审方法。

> [!NOTE] 
> 全局管理员或用户管理员角色需要创建访问评审。

1. 每周都会进行评审。

2. 永远不会结束，以确保将此排除组保持在最新状态。

3. 此组的所有成员在评审范围内。

4. 每个用户都需要自我证明，他们仍需要从这些被阻止的国家/地区进行访问，因此他们仍需要成为组的成员。

5. 如果用户未响应审核请求，则会自动从组中删除这些请求，并将无法再访问这些国家/地区。

6. 启用电子邮件通知，让用户了解访问评审的开始和完成。

    ![示例 1 的“创建访问评审”窗格](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>示例 2：对使用旧式身份验证进行访问的用户进行访问评审

假设你有一个条件性访问策略，该策略阻止用户使用旧身份验证和较旧的客户端版本进行访问，并包括从策略中排除的组。 下面是评审该组成员的建议访问评审方法。

1. 此项评审需是定期评审。

2. 该组中的每个人都需要接受评审。

3. 评审可配置为将业务部门主管列作选定的评审者。

4. 自动应用结果并删除未经批准的用户，让他们继续使用旧式身份验证方法。

5. 启用建议可以帮助大型组的评审者轻松做出决策。

6. 启用邮件通知，让用户知道访问评审的开始和完成时间。

    ![示例 2 的“创建访问评审”窗格](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>如果你有很多排除组，因此需要创建多个访问评审，则现在的 Microsoft Graph beta 终结点中有一个 API，可通过它以编程方式创建和管理它们。 若要开始，请参阅 [Azure AD 访问评审 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root)和[通过 Microsoft Graph 检索 Azure AD 访问评审的示例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)。

## <a name="access-review-results-and-audit-logs"></a>访问评审结果和审核日志

做好组、条件访问策略和访问评审方面的一切准备工作后，可以监视和跟踪这些评审的结果。

1. 在 Azure 门户中，打开 "**访问评审**" 边栏选项卡。

2. 打开创建用于管理排除组的控制措施和程序。

3. 单击“结果”查看已批准哪些人保留在该列表中，以及删除了哪些人。****

    ![访问评审结果显示谁获得了批准](./media/conditional-access-exclusion/access-reviews-results.png)

4. 然后单击“审核日志”查看评审期间执行的操作。****

    ![访问评审审核日志列表操作](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

IT 管理员知道，管理策略的排除组有时不可避免。 不过，维护这些组，使企业所有者或用户自己定期查看这些组，并使用 Azure AD 访问评审来审核这些更改。

## <a name="next-steps"></a>后续步骤

- [创建组或应用程序的访问评审](create-access-review.md)
- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)
