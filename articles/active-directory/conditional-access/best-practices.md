---
title: Azure Active Directory 中条件性访问的最佳实践 | Microsoft Docs
description: 了解用户须知内容，以及在配置条件性访问策略时应避免的操作。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11d4d319fa31dd2493810dc7293d415554f79d94
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571114"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中条件性访问的最佳实践

使用 [Azure Active Directory (Azure AD) 条件访问](../active-directory-conditional-access-azure-portal.md)，可以控制授权用户访问云应用程序的方式。 本文提供以下事项的信息：

- 应了解的内容 
- 在配置条件性访问策略时应避免的操作。 

本文假定你熟悉 [Azure Active Directory 中的条件访问是什么？](../active-directory-conditional-access-azure-portal.md)中所述的概念和术语



## <a name="whats-required-to-make-a-policy-work"></a>使策略发挥作用需要什么？

新建策略时，未选择任何用户、组、应用或访问控制。

![云应用](./media/best-practices/02.png)


若要使策略发挥作用，必须进行下列配置：


| 对象           | 方式                                  | Why |
| :--            | :--                                  | :-- |
| 云应用 |选择一个或多个应用。  | 条件访问策略的目标是使你能够控制已授权用户访问云应用的方式。|
| 用户和组 | 至少选择一个已经授权的用户或组来访问所选云应用。 | 未在其中分配任何用户和组的条件性访问策略永远不会触发。 |
| 访问控制 | 至少选择一个访问控制。 | 策略处理器需要知道条件满足时需要执行的操作。 |




## <a name="what-you-should-know"></a>要点



### <a name="how-are-conditional-access-policies-applied"></a>条件访问策略是如何应用的？

访问云应用时，可能会应用多个条件访问策略。 在这种情况下，必须满足所应用的所有策略。 例如，如果一个策略需要 MFA，并且另一个策略需要符合标准的设备，则你必须通过 MFA 并使用符合标准的设备。 

所有策略都是在两个阶段中强制实施的：

- 在**第一个**阶段中，将对所有策略进行评估并收集不满足的所有访问控制。 

- 在**第二个**阶段中，将提示你去满足尚未满足的要求。 如果其中一个策略阻止访问，则会阻止你并且不会提示你去满足其他策略控制。 如果没有策略阻止你，则会提示你按以下顺序满足其他策略控制：

    ![顺序](./media/best-practices/06.png)
    
    然后是外部 MFA 提供程序和使用条款。



### <a name="how-are-assignments-evaluated"></a>如何计算分配？

所有分配在逻辑上采用 **AND** 运算符。 如果配置了多个分配，则必须满足所有分配才能触发策略。  

如果需要配置一个位置条件并将其应用到从组织网络外部进行的所有连接，则请执行以下操作：

- 包括**所有位置**
- 排除**所有受信任的 IP**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>如果你被锁定在 Azure AD 管理门户之外，该怎么办？

如果你因为条件性访问策略中的设置不正确而被锁定在 Azure AD 门户之外，则请执行以下操作：

- 检查组织中是否有其他管理员尚未被阻止。 具有 Azure 门户访问权限的管理员可以禁用影响你登录的策略。 

- 如果组织中没有管理员可以更新策略，则需提交支持请求。 Microsoft 支持人员可以审核并更新妨碍访问的条件性访问策略。


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果在 Azure 经典门户和 Azure 门户中配置了策略，会发生什么情况？  

仅当满足所有要求时，Azure Active Directory 才实施策略，用户才可获取访问权限。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果在 Intune Silverlight 门户和 Azure 门户中配置了策略，会发生什么情况？

仅当满足所有要求时，Azure Active Directory 才实施策略，用户才可获取访问权限。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果对同一个用户配置了多个策略，会发生什么情况？  

每次登录时，Azure Active Directory 都会评估所有策略，确保只有满足所有要求，才向该用户授予访问权限。 阻止访问优先于所有其他配置设置。 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>条件性访问是否适用于 Exchange ActiveSync？

是的可以在某些条件性访问策略中使用 Exchange ActiveSync[限制](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/conditional-access-for-exo-and-spo#exchange-activesync)。 

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>要如何与 Office 365 应用配置条件性访问？

由于 Office 365 应用的互连，我们建议创建策略时通常分配一起使用的应用。

常见的互连应用程序包括 Microsoft Flow、 Microsoft Planner、 Microsoft Teams、 Office 365 Exchange Online、 Office 365 SharePoint Online 和 Office 365 Yammer。

非常重要的策略的访问控制会话或任务的开始处时需要用户交互，如多重身份验证。 如果不这样做，用户将无法完成应用程序的一些任务。 例如，如果您需要多重身份验证在非托管的设备访问 SharePoint 上，但不适用于电子邮件，用户使用其电子邮件将无法将 SharePoint 文件附加到一条消息。 可在本文中，找到更多信息[什么是 Azure Active Directory 条件访问中的服务依赖项？](service-dependencies.md)。





## <a name="what-you-should-avoid-doing"></a>应避免的操作

条件访问框架提供了极大的配置灵活性。 不过，极大的灵活性也意味着应先仔细检查每个配置策略，然后才能发布，以免产生不良结果。 在这种情况下，应该特别注意影响完整集的任务，例如**所有用户/组/云应用**。

在环境中，应避免以下配置：


**对于所有用户、所有云应用：**

- **阻止访问** - 此配置将阻止整个组织（这绝对不是一个好的选项）。

- **需要符合的设备** - 对于尚未注册其设备的用户，此策略将阻止所有访问权限（包括对 Intune 门户的访问权限）。 如果是不具有注册设备的管理员，则此策略会阻止你回到 Azure 门户更改策略。

- **需要加入域** - 如果不具有加入域的设备，此阻止访问权限的策略还可能会阻止组织中所有用户的访问权限。

- **需要应用保护策略**-此策略阻止访问也有可能阻止你的组织中的所有用户的访问权限，如果你没有 Intune 策略。 如果你是管理员不具有 Intune 应用保护策略的客户端应用程序的情况下，此策略会阻止你回到如 Intune 和 Azure 门户。

**对于所有用户、所有云应用、所有设备平台：**

- **阻止访问** - 此配置将阻止整个组织（这绝对不是一个好的选项）。


## <a name="how-should-you-deploy-a-new-policy"></a>应如何部署新的策略？

第一步，应使用[假设状况法](what-if-tool.md)对策略进行评估。

当新策略针对你的环境准备就绪后，分阶段部署它们：

1. 对一小组用户应用策略，验证策略的表现是否符合预期。 

2.  当扩展策略以包括更多用户时， 继续从策略中排除所有管理员，以确保在需要进行更改时他们仍然具有访问权限且可以更新策略。

3. 仅当必要时，才将策略应用于所有用户。 

最佳做法是创建一个符合以下条件的用户帐户：

- 专用于策略管理 
- 已从所有策略中排除


## <a name="policy-migration"></a>策略迁移

考虑迁移未在 Azure 门户中创建的策略，因为：

- 现在可以解决以前无法处理的方案。

- 可以通过合并来减少需要管理的策略数。   

- 可以在一个中心位置管理所有条件访问策略。

- Azure 经典门户已停用。   


有关详细信息，请参阅[在 Azure 门户中迁移经典策略](policy-migration.md)。


## <a name="next-steps"></a>后续步骤

如果希望了解：

- 如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](app-based-mfa.md)。
- 如何规划条件访问策略，请参阅[如何在 Azure Active Directory 中规划条件访问部署](plan-conditional-access.md)。
