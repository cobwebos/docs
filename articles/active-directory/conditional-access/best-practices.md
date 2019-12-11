---
title: Azure Active Directory 中的条件性访问的最佳做法 |Microsoft Docs
description: 了解你应了解的内容以及在配置条件访问策略时应避免执行的操作。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccfbb31c29b9e240a4865c8d7d98d7b6af00d1fd
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963930"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中的条件性访问的最佳做法

使用[Azure Active Directory （Azure AD）条件访问](../active-directory-conditional-access-azure-portal.md)，可以控制授权用户访问云应用的方式。 本文提供以下事项的信息：

- 应了解的内容 
- 在配置条件访问策略时应避免执行的操作。 

本文假设你熟悉[Azure Active Directory 中的条件性访问中](../active-directory-conditional-access-azure-portal.md)所述的概念和术语：

## <a name="whats-required-to-make-a-policy-work"></a>使策略发挥作用需要什么？

新建策略时，未选择任何用户、组、应用或访问控制。

![云应用](./media/best-practices/02.png)

若要使策略发挥作用，必须进行下列配置：

| 对象           | 方式                                  | Why |
| :--            | :--                                  | :-- |
| 云应用 |选择一个或多个应用。  | 条件性访问策略的目标是使你能够控制授权用户访问云应用的方式。|
| 用户和组 | 至少选择一个已经授权的用户或组来访问所选云应用。 | 永远不会触发未分配用户和组的条件性访问策略。 |
| 访问控制 | 至少选择一个访问控制。 | 策略处理器需要知道条件满足时需要执行的操作。 |

## <a name="what-you-should-know"></a>要点

### <a name="how-are-conditional-access-policies-applied"></a>如何应用条件性访问策略？

访问云应用时可能应用多个条件性访问策略。 在这种情况下，必须满足所应用的所有策略。 例如，如果一个策略需要多重身份验证（MFA），而另一个策略需要相容设备，则必须完成 MFA，并使用兼容的设备。 

所有策略都是在两个阶段中强制实施的：

- 阶段 1： 
   - 详细信息集合：收集详细信息以确定已满足的策略。
   - 在此阶段，如果设备符合性是你的条件性访问策略的一部分，则用户可能会看到证书提示。 如果设备操作系统不是 Windows 10，浏览器应用可能会出现此提示。
   - 针对 "[仅报告" 模式下](concept-conditional-access-report-only.md)的所有已启用策略和策略进行策略评估的阶段1。
- 阶段 2：
   - 强制：考虑在第1阶段收集的详细信息，请求用户满足任何尚未满足的其他要求。
   - 将结果应用于会话。 
   - 所有已启用的策略的策略评估阶段2。

### <a name="how-are-assignments-evaluated"></a>如何计算分配？

所有分配在逻辑上采用 **AND** 运算符。 如果配置了多个分配，则必须满足所有分配才能触发策略。  

如果需要配置一个位置条件并将其应用到从组织网络外部进行的所有连接，则请执行以下操作：

- 包括**所有位置**
- 排除**所有受信任的 IP**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>如果你被锁定在 Azure AD 管理门户之外，该怎么办？

如果由于条件性访问策略中的设置不正确而导致 Azure AD 门户的锁定：

- 检查组织中是否有其他管理员尚未被阻止。 具有 Azure 门户访问权限的管理员可以禁用影响你登录的策略。 
- 如果组织中没有管理员可以更新策略，则需提交支持请求。 Microsoft 支持人员可以查看和更新阻止访问的条件性访问策略。

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果在 Azure 经典门户和 Azure 门户中配置了策略，会发生什么情况？  

仅当满足所有要求时，Azure Active Directory 才实施策略，用户才可获取访问权限。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果在 Intune Silverlight 门户和 Azure 门户中配置了策略，会发生什么情况？

仅当满足所有要求时，Azure Active Directory 才实施策略，用户才可获取访问权限。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果对同一个用户配置了多个策略，会发生什么情况？  

每次登录时，Azure Active Directory 都会评估所有策略，确保只有满足所有要求，才向该用户授予访问权限。 阻止访问优先于所有其他配置设置。 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>条件访问是否适用于 Exchange ActiveSync？

是的，可以在条件性访问策略中使用 Exchange ActiveSync。

某些云应用（如 SharePoint Online 和 Exchange Online）还支持旧版身份验证协议。 当客户端应用程序可以使用旧身份验证协议访问云应用程序时，Azure AD 无法在此访问尝试上强制实施条件性访问策略。 为了防止客户端应用绕过策略的实施，应该检查它是否能够做到只对受影响的云应用启用新式身份验证。

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>如何配置 Office 365 应用的条件访问？

由于 Office 365 应用程序是相互连接的，因此我们建议在创建策略时同时分配常用的应用程序。

常见的互联应用程序包括 Microsoft Flow、Microsoft Planner、Microsoft 团队、Office 365 Exchange Online、Office 365 SharePoint Online 和 Office 365 Yammer。

对于需要用户交互的策略（如多重身份验证），当访问在会话或任务开始时控制时，这一点非常重要。 如果不这样做，用户将无法在应用内完成一些任务。 例如，如果在非托管设备上需要多重身份验证来访问 SharePoint 而不是电子邮件，则使用电子邮件的用户将无法将 SharePoint 文件附加到邮件中。 有关详细信息，请参阅文章[Azure Active Directory 条件性访问中的服务依赖关系是什么？](service-dependencies.md)。

## <a name="what-you-should-avoid-doing"></a>应避免的操作

条件性访问框架为您提供了极佳的配置灵活性。 不过，极大的灵活性也意味着应先仔细检查每个配置策略，然后才能发布，以免产生不良结果。 在这种情况下，应该特别注意影响完整集的任务，例如**所有用户/组/云应用**。

在环境中，应避免以下配置：

**对于所有用户、所有云应用：**

- **阻止访问** - 此配置将阻止整个组织（这绝对不是一个好的选项）。
- **需要符合的设备** - 对于尚未注册其设备的用户，此策略将阻止所有访问权限（包括对 Intune 门户的访问权限）。 如果是不具有注册设备的管理员，则此策略会阻止你回到 Azure 门户更改策略。
- **需要加入域** - 如果不具有加入域的设备，此阻止访问权限的策略还可能会阻止组织中所有用户的访问权限。
- **需要应用保护策略**-如果没有 Intune 策略，此策略阻止访问也可能会阻止组织中所有用户的访问。 如果你是没有具有 Intune 应用保护策略的客户端应用程序的管理员，则此策略会阻止你返回到 Intune 和 Azure 等门户。

**对于所有用户、所有云应用、所有设备平台：**

- **阻止访问** - 此配置将阻止整个组织（这绝对不是一个好的选项）。

## <a name="how-should-you-deploy-a-new-policy"></a>应如何部署新的策略？

第一步，应使用[假设状况法](what-if-tool.md)对策略进行评估。

当新策略针对你的环境准备就绪后，分阶段部署它们：

1. 对一小组用户应用策略，验证策略的表现是否符合预期。 
1. 当扩展策略以包括更多用户时， 继续从策略中排除所有管理员，以确保在需要进行更改时他们仍然具有访问权限且可以更新策略。
1. 仅当必要时，才将策略应用于所有用户。 

最佳做法是创建一个符合以下条件的用户帐户：

- 专用于策略管理 
- 已从所有策略中排除

## <a name="policy-migration"></a>策略迁移

考虑迁移未在 Azure 门户中创建的策略，因为：

- 现在可以解决以前无法处理的方案。
- 可以通过合并来减少需要管理的策略数。   
- 你可以在一个中心位置管理所有条件访问策略。
- Azure 经典门户已停用。   

有关详细信息，请参阅[在 Azure 门户中迁移经典策略](policy-migration.md)。

## <a name="next-steps"></a>后续步骤

如果希望了解：

- 如何配置条件性访问策略的详细说明，请参阅[需要对具有 Azure Active Directory 条件性访问的特定应用的 MFA](app-based-mfa.md)。
- 如何规划你的条件访问策略，请参阅[如何在 Azure Active Directory 中规划条件性访问部署](plan-conditional-access.md)。
