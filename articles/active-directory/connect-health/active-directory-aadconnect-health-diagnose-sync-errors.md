---
title: Azure AD Connect Health - 诊断重复属性同步错误 | Microsoft Docs
description: 本文档介绍如何直接在 Azure 门户中诊断重复属性同步错误，以及使用潜在可行的方法修复对象孤立的场景。
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 4a6e0924492c26c9bad4ed0af207ad9764c3cc5c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831891"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>诊断并修正重复的属性同步错误

## <a name="overview"></a>概述
为了进一步凸显同步错误，Azure Active Directory (Azure AD) Connect Health 引入了自助式修复功能。 它可对重复的属性同步错误进行故障排除，并修复从 Azure AD 孤立的对象。
诊断功能具有以下优势：
- 它提供诊断过程，可缩小重复的属性同步错误的范围。 并且还能提供特定的修复。
- 它可对 Azure AD 中专有的场景应用修复，一步即可解决错误。
- 无需升级或配置即可启用此功能。
有关 Azure AD 的详细信息，请参阅[标识同步和重复属性复原](https://aka.ms/dupattributeresdocs)。

## <a name="problems"></a>问题
### <a name="a-common-scenario"></a>常见方案
发生 QuarantinedAttributeValueMustBeUnique 和 AttributeValueMustBeUnique 同步错误时，Azure AD 中往往会出现 UserPrincipalName 或代理地址冲突的情况。 在本地端更新有冲突的源对象即可解决同步错误。 下一次同步后，将会解决同步错误。例如，此图指示两位用户的 UserPrincipalName 有冲突。 两者都为 Joe.J@contoso.com。 有冲突的对象将在 Azure AD 中隔离。

![诊断常见的同步错误场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>孤立的对象场景
有时，现有的用户会丢失源定位点。 源对象的删除操作发生在本地 Active Directory 中。 但删除信号的更改永远不会同步到 Azure AD。 同步引擎出现问题或域迁移等原因会导致此类丢失。 如果还原或重新创建相同对象，则现有的用户在逻辑上应该是要从源定位点同步的用户。 

现有用户为仅限云的对象时，还可以看到冲突的用户同步到 Azure AD。 同步中的用户无法与现有对象相匹配。 没有任何一种直接方法可以重新映射源定位点。 查看更多[现有知识库](https://support.microsoft.com/help/2647098)。 

例如，Azure AD 中的现有对象会保留 Joe 的许可证。 具有不同源定位点的新同步对象以重复的属性状态出现在 Azure AD 中。 在本地 Active Directory 中对 Joe 所做的更改无法应用到 Joe 在 Azure AD 中的原始用户（现有对象）。  

![诊断同步错误孤立对象的场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Connect Health 中的诊断和故障排除步骤 
诊断功能支持具有以下重复属性的用户对象：

| 属性名称 | 同步错误类型|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique 或 AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique 或 AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> 需要在 RBAC 设置中指定“全局管理员”权限或“参与者”才能访问此功能。
>

在 Azure 门户中遵循以下步骤，可以在同步错误详细信息中缩小诊断范围，并提供更具体的解决方法：

![同步错误诊断步骤](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

在 Azure 门户中，可以采取几个步骤来识别具体的可修复场景：  
1.  检查“诊断状态”列。 状态显示是否还有一种可能的方式，直接从 Azure Active Directory 修复同步错误。 换而言之，故障排除流的存在可以缩小错误产生的情况并可能修复此错误。
| 状态 | 它意味着什么？ |
| ------------------ | -----------------|
| 未启动 | 尚未访问此诊断过程。 根据诊断结果，有潜在可行的方法可以直接从门户修复同步错误。 |
| 需要手动修复 | 该错误不符合从门户修复的条件。 有冲突的对象类型不是用户，或者已完成诊断步骤，但没有可从门户实施的解决方法。 后一种情况下，从本地端修复仍是解决方法之一。 [详细了解本地解决方法](https://support.microsoft.com/help/2647098)。 | 
| 等待同步 | 已应用修复程序。 门户正在等待下一个同步周期以清除错误。 |
  >[!IMPORTANT]
  > 每个同步周期后都会重置诊断状态列。 
  >

2.  在错误详细信息下选择“诊断”按钮。 需回答几个问题，确定同步错误的详细信息。 问题的答案有助于识别孤立对象的原因。

3.  如果结束诊断时出现“关闭”按钮，则表示根据回答，没有可从门户实施的快速解决方法。 请参考最后一个步骤中显示的解决方法。 从本地端修复仍是一种解决方法。 选择“关闭”按钮。 当前同步错误的状态将切换为“需要手动修复”。 当前的同步周期内，状态将保持不变。

4.  识别孤立对象的原因后，可以直接从门户解决重复的属性同步错误。 选择“应用修复”按钮触发该过程。 当前同步错误的状态将更新为“等待同步”。

5.  下一个同步周期后，该错误应会从列表中删除。

## <a name="how-to-answer-the-diagnosis-questions"></a>如何回答诊断问题 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>该用户是否存在于本地 Active Directory 中？

此问题的目的是试图识别本地 Active Directory 中现有用户的源对象。  
1.  检查 Active Directory 是否包含具有提供的 UserPrincipalName 的对象。 如果没有，请回答“否”。
2.  如果有，请检查该对象是否仍在同步的范围内。  
  - 使用 DN 在 Azure AD 连接器空间中搜索。
  - 如果找到了状态为“等待添加”的对象，请回答“否”。 Azure AD Connect 无法将该对象连接到正确的 Azure AD 对象。
  - 如果未找到该对象，请回答“是”。

在这些示例中，问题会尝试确定 Joe Jackson 是否仍存在于本地 Active Directory 中。
在一般的场景中，用户 Joe Johnson 和 Joe Jackson 都会在本地 Active Directory 中。 隔离的对象是两个不同的用户。

![诊断常见的同步错误场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

在孤立对象的场景中，只有单个用户（即 Joe Johnson）在本地 Active Directory 中：

![诊断同步错误孤立对象“用户是否存在”的场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>这两个帐户是否属于同一用户？
此问题的目的是检查传入的有冲突用户和 Azure AD 中的现有用户对象，确定两者是否属于同一用户。  
1.  有冲突的对象最近已同步到 Azure Active Directory。 比较对象的属性：  
  - 显示名称
  - 用户主体名称
  - 对象 ID
2.  如果 Azure AD 无法对它们进行比较，请检查 Active Directory 是否包含具有提供的 UserPrincipalNames 的对象。 如果找到这两个对象，请回答“否”。

在下面的示例中，两个对象属于同一用户 Joe Johnson。

![诊断同步错误孤立对象“同一用户”的场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>对孤立对象场景应用修复后发生的情况
根据以上问题的回答，如果可以从 Azure AD 进行修复，则会显示“应用修复”按钮。 在这种情况下，本地对象正在与意料之外的 Azure AD 对象同步。 两个对象是使用“源定位点”映射的。 “应用修复”更改需要执行这些或类似的步骤：
1. 将源定位点更新为 Azure AD 中的正确对象。
2. 删除 Azure AD 中有冲突的对象（如果存在）。

![修复后诊断同步错误](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> “应用修复”时所做的更改只适用于孤立对象的情况。
>

执行上述步骤之后，用户可以访问原始资源，这是对现有对象的链接。 列表视图中的“诊断状态”值会更新为“等待同步”。下一次同步后，将会解决同步错误。Connect Health 将不再在列表视图中显示已解决的同步错误。


## <a name="faq"></a>常见问题
**问：** 如果“应用修复”操作执行失败，会发生什么情况？  
**答：** 如果执行失败，原因可能是 Azure AD Connect 遇到导出错误。 请刷新门户页，并在完成下一次同步后重试。默认同步周期为 30 分钟。 


**问：** 如果**现有对象**是要删除的对象该怎么办？  
**答：** 如果应删除现有对象，该过程不涉及对源定位点的更改。 通常情况下，可以从本地 Active Directory 修复此错误。 


**问：** 用户应用修复需要哪些权限？  
**答：** 全局管理员或在 RBAC 设置中指定的参与者有权访问诊断和故障排除过程。


**问：** 是否需要为此功能配置 Azure AD Connect 或更新 Azure AD Connect Health 代理？  
**答：** 不需要，诊断过程是完全基于云的功能。


**问：** 如果软删除了现有对象，诊断过程是否会再次将该对象设为活动状态？  
**答：** 不会，修复过程不会更新除源定位点以外的对象属性。
