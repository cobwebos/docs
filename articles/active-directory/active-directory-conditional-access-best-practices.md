---
title: "Azure Active Directory 中条件性访问的最佳实践 | Microsoft Docs"
description: "了解用户须知内容，以及在配置条件性访问策略时应避免的操作。"
services: active-directory
keywords: "对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 8c6707505a6331b53e06b1de60575dd3637ea477
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中条件性访问的最佳实践

本主题介绍用户须知内容，以及在配置条件性访问策略时应避免的操作。 阅读本主题之前，应熟悉 [Azure Active Directory 中的条件性访问](active-directory-conditional-access-azure-portal.md)中列出的概念和术语

## <a name="what-you-should-know"></a>要点

### <a name="whats-required-to-make-a-policy-work"></a>使策略发挥作用需要什么？

新建策略时，未选择任何用户、组、应用或访问控制。

![云应用](./media/active-directory-conditional-access-best-practices/02.png)


若要使策略发挥作用，必须进行下列配置：


|对象           | 方式                                  | Why|
|:--            | :--                                  | :-- |
|云应用 |需要选择一个或多个应用。  | 条件访问策略的目标是使你能够微调已授权用户访问应用的方式。|
| 用户和组 | 需要选择至少一个已经授权的用户或组来访问所选云应用。 | 未在其中分配任何用户和组的条件性访问策略永远不会触发。 |
| 访问控制 | 需要至少选择一个访问控制。 | 策略处理器需要知道条件满足时需要执行的操作。|


除了这些基本要求，在许多情况下，还应该配置条件。 虽然策略也可以无需配置条件，但条件是微调应用访问权限的驱动因素。


![云应用](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>如何计算分配？

所有分配在逻辑上采用 **AND** 运算符。 如果配置了多个分配来触发策略，则必须满足所有分配。  

如果需要配置一个位置条件并将其应用到从组织网络外部进行的所有连接，可通过以下方式实现此目的：

- 包含**所有位置**
- 排除**所有受信任的 IP**

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



## <a name="policy-migration"></a>策略迁移

应考虑迁移未在 Azure 门户中创建的策略，因为：

- 现在可以解决以前无法处理的方案。

- 可以通过合并来减少需要管理的策略数。   

- 可以在一个中心位置管理所有条件访问策略。

- Azure 经典门户将停用。   


有关详细信息，请参阅[在 Azure 门户中迁移经典策略](active-directory-conditional-access-migration.md)。


## <a name="next-steps"></a>后续步骤

若要了解如何配置条件性访问策略，请参阅 [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md)（Azure Active Directory 中的条件性访问入门）。
