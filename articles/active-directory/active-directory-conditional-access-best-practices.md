---
title: Azure Active Directory 中条件性访问的最佳实践 | Microsoft Docs
description: 了解用户须知内容，以及在配置条件性访问策略时应避免的操作。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c155e18ddc4f1713aa9c62a991f4e849e024c5da
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723654"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中条件性访问的最佳实践

使用 [Azure Active Directory (Azure AD) 条件访问](active-directory-conditional-access-azure-portal.md)，可以控制授权用户访问云应用程序的方式。 本文提供以下事项的信息：

- 应了解的内容 
- 在配置条件性访问策略时应避免的操作。 

本文假定你熟悉 [Azure Active Directory 中的条件性访问](active-directory-conditional-access-azure-portal.md)中列出的概念和术语



## <a name="whats-required-to-make-a-policy-work"></a>使策略发挥作用需要什么？

新建策略时，未选择任何用户、组、应用或访问控制。

![云应用](./media/active-directory-conditional-access-best-practices/02.png)


若要使策略发挥作用，必须进行下列配置：


|对象           | 方式                                  | Why|
|:--            | :--                                  | :-- |
|云应用 |需要选择一个或多个应用。  | 条件访问策略的目标是使你能够控制已授权用户访问云应用的方式。|
| 用户和组 | 需要选择至少一个已经授权的用户或组来访问所选云应用。 | 未在其中分配任何用户和组的条件性访问策略永远不会触发。 |
| 访问控制 | 需要至少选择一个访问控制。 | 策略处理器需要知道条件满足时需要执行的操作。|




## <a name="what-you-should-know"></a>要点

### <a name="how-are-assignments-evaluated"></a>如何计算分配？

所有分配在逻辑上采用 **AND** 运算符。 如果配置了多个分配，则必须满足所有分配才能触发策略。  

如果需要配置一个位置条件并将其应用到从组织网络外部进行的所有连接，则请执行以下操作：

- 包括**所有位置**
- 排除**所有受信任的 IP**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>如果你被锁定在 Azure AD 管理门户之外，该怎么办？

如果你因为条件性访问策略中的设置不正确而被锁定在 Azure AD 门户之外，则请执行以下操作：

- 验证组织中是否有其他管理员尚未被阻止。 具有 Azure 门户访问权限的管理员可以禁用影响你登录的策略。 

- 如果组织中没有管理员可以更新策略，则需提交支持请求。 Microsoft 支持人员可以审核并更新妨碍访问的条件性访问策略。


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果在 Azure 经典门户和 Azure 门户中配置了策略，会发生什么情况？  

仅当满足所有要求时，Azure Active Directory 才实施策略，用户才可获取访问权限。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果在 Intune Silverlight 门户和 Azure 门户中配置了策略，会发生什么情况？

仅当满足所有要求时，Azure Active Directory 才实施策略，用户才可获取访问权限。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果对同一个用户配置了多个策略，会发生什么情况？  

每次登录时，Azure Active Directory 都会评估所有策略，确保只有满足所有要求，才向该用户授予访问权限。


### <a name="does-conditional-access-work-with-exchange-activesync"></a>条件性访问是否适用于 Exchange ActiveSync？

适用，可以在条件访问策略中使用 Exchange ActiveSync。






## <a name="what-you-should-avoid-doing"></a>应避免的操作

条件访问框架提供了极大的配置灵活性。 不过，极大的灵活性也意味着应先仔细检查每个配置策略，然后才能发布，以免产生不良结果。 在这种情况下，应该特别注意影响完整集的任务，例如**所有用户/组/云应用**。

在环境中，应避免以下配置：


**对于所有用户、所有云应用：**

- **阻止访问** - 此配置将阻止整个组织（这绝对不是一个好的选项）。

- **需要符合的设备** - 对于尚未注册其设备的用户，此策略将阻止所有访问权限（包括对 Intune 门户的访问权限）。 如果是不具有注册设备的管理员，则此策略会阻止你回到 Azure 门户更改策略。

- **需要加入域** - 如果不具有加入域的设备，此阻止访问权限的策略还可能会阻止组织中所有用户的访问权限。


**对于所有用户、所有云应用、所有设备平台：**

- **阻止访问** - 此配置将阻止整个组织（这绝对不是一个好的选项）。


## <a name="how-should-you-deploy-a-new-policy"></a>应如何部署新的策略？

第一步，应使用[假设状况法](active-directory-conditional-access-whatif.md)对策略进行评估。

做好将新策略部署到环境中的准备以后，应分阶段进行部署：

1. 对一小组用户应用策略，验证策略的表现是否符合预期。 

2.  将策略的范围扩大，使之包括更多用户时，继续将所有管理员排除在策略之外。 这样是为了确保在需要更改的情况下，管理员仍然可以访问并对策略进行更新。

3. 将策略应用到所有用户的前提是确实需要这样做。 

最佳做法是创建一个符合以下条件的用户帐户：

- 专用于策略管理 
- 已从所有策略中排除


## <a name="policy-migration"></a>策略迁移

考虑迁移未在 Azure 门户中创建的策略，因为：

- 现在可以解决以前无法处理的方案。

- 可以通过合并来减少需要管理的策略数。   

- 可以在一个中心位置管理所有条件访问策略。

- Azure 经典门户已停用。   


有关详细信息，请参阅[在 Azure 门户中迁移经典策略](active-directory-conditional-access-migration.md)。


## <a name="next-steps"></a>后续步骤

若要了解如何配置条件性访问策略，请参阅 [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md)（Azure Active Directory 中的条件性访问入门）。
