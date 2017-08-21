---
title: "Azure Active Directory 中条件性访问的最佳实践 | Microsoft Docs"
description: "了解用户须知内容，以及在配置条件性访问策略时应避免的操作。"
services: active-directory
keywords: "对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 3e524c116479c1af6eb6a601c9b57d27a697c5a2
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

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


## <a name="what-you-should-avoid-doing"></a>你应避免的操作

条件访问框架为你提供了极大的配置灵活性。 但是，极大的灵活性也意味着在发布之前你应该仔细检查每个配置策略，以避免产生不理想的结果。 在这种情况下，你应该特别注意影响完整集的任务，例如**所有用户/组/云应用**。

在你的环境中，应避免以下配置：


**对于所有用户、所有云应用：**

- **阻止访问** - 此配置将阻止你的整个组织（这绝对不是一个好的选项）。

- **需要符合的设备** - 对于尚未注册其设备的用户，此策略将阻止所有访问权限（包括对 Intune 门户的访问权限）。 如果你是不具有注册设备的管理员，则此策略会阻止你回到 Azure 门户更改策略。

- **需要加入域** - 如果你不具有加入域的设备，此阻止访问权限的策略还可能会阻止你组织中所有用户的访问权限。


**对于所有用户、所有云应用、所有设备平台：**

- **阻止访问** - 此配置将阻止你的整个组织（这绝对不是一个好的选项）。


## <a name="common-scenarios"></a>常见方案

### <a name="requiring-multi-factor-authentication-for-apps"></a>要求对应用使用多重身份验证

在许多环境中，某些应用所需的保护级别要比其他应用要高一些。
例如，有权访问敏感数据的应用就需要更高的保护级别。
如果想要为这些应用添加另一层保护，可以配置条件访问策略，要求访问这些应用的用户执行多重身份验证。


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>通过不受信任的网络访问时要求执行多重身份验证

此方案与前一种方案类似，两者都提出了多重身份验证的要求。
但是，主要区别在于此项要求的条件。  
前一种方案的重心是有权访问敏感数据的应用，而本方案的重心则是受信任的位置。  
换而言之，如果用户通过不受信任的网络访问应用，则你可能要求执行多重身份验证。


### <a name="only-trusted-devices-can-access-office-365-services"></a>只允许受信任的设备访问 Office 365 服务

如果在环境中使用 Intune，马上就可以在 Azure 控制台中开始使用条件性访问策略接口。

许多 Intune 客户都在使用条件性访问来确保只有受信任的设备可以访问 Office 365 服务。 这意味着，访问这些服务的移动设备已注册到 Intune 并满足合规策略要求，Windows 电脑已加入本地域。 一项重要改进是不需要针对每个 Office 365 服务设置相同的策略。  创建新策略时，可以配置云应用，以包含想要使用条件性访问保护的每个 O365 应用。

## <a name="next-steps"></a>后续步骤

若要了解如何配置条件性访问策略，请参阅 [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md)（Azure Active Directory 中的条件性访问入门）。

