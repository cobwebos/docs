---
title: Azure AD Connect Health - 诊断重复属性同步错误 | Microsoft Docs
description: 本文档介绍如何直接在 Azure 门户中诊断重复属性同步错误，以及使用潜在可行的方法解决对象孤立的场景。
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
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305544"
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>重复属性同步错误的诊断和补救 

## <a name="overview"></a>概述
Azure Active Directory Connect Health 在突出同步错误的基础上更进一步，现在又引入了一个自助式补救体验，用于排查重复属性同步错误和修复 Azure AD 中的孤立对象。 诊断功能的主要优势：
- 提供诊断过程来缩小重复属性同步错误场景的范围，并指明具体的解决方法
- 针对 Azure AD 专有的场景应用修复程序，一键式解决错误
- 无需升级或配置即可启用此功能。
详细了解 Azure AD 的[重复属性复原](https://aka.ms/dupattributeresdocs)。

## <a name="problems"></a>问题
### <a name="common-scenario"></a>常见方案
发生 **QuarantinedAttributeValueMustBeUnique** 和 **AttributeValueMustBeUnique** 同步错误时，Azure AD 中往往会出现用户主体名称或代理地址冲突。 在本地端更新有冲突的源对象即可解决同步错误。 完成后续的同步后，将会解决同步错误。 例如，下图指示两个用户的 UserPrincipalName *Joe.J@contoso.com* 有冲突。有冲突的对象将在 Azure AD 中隔离。 

![诊断常见的同步错误场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>孤立的对象场景
有时，现有的用户会丢失源定位点。 源对象的删除发生在本地 AD 中，但删除信号的更改永远不会同步到 Azure AD。 发生此问题的原因可能包括同步引擎问题或域迁移。 如果还原或重新创建了同一个对象，则现有的用户在逻辑上应该是要从源定位点同步的用户。 对于充当仅限云的对象的现有用户，还可能会发现，有冲突的用户已同步到 Azure AD，无法在现有对象的同步中进行匹配。 没有任何一种直接方法可以重新映射源定位点。 详细阅读[现有的知识库文章](https://support.microsoft.com/help/2647098)。 例如，Azure AD 中的现有对象会保留 Joe 的许可证。 具有不同源定位点的新同步对象以重复属性状态出现在 Azure AD 中。 在本地 AD 中对 Joe 所做的更改无法应用到 Joe 在 Azure AD 中的原始用户（现有对象）。  

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

![诊断同步错误的诊断步骤](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

在 Azure 门户中，可以执行几个步骤来识别具体的可修复场景：  
1.  在诊断状态列中，状态会显示是否有潜在的故障排除流程可以缩小错误范围，以及是否有直接从 Azure Active Directory 解决问题的潜在方法。
| 状态 | 它意味着什么？ |
| ------------------ | -----------------|
| 未启动 | 尚未访问此诊断过程。 根据诊断结果，有潜在可行的方法可以直接从门户解决同步错误。 |
| 需要手动修复 | 该错误不符合从门户解决的条件。 问题的原因可能是：(1) 有冲突的对象类型不是用户；(2) 已完成诊断步骤，但没有可从门户实施的解决方法。 在这种情况下，解决方法之一仍旧是在本地解决。 [详细了解本地解决方法](https://support.microsoft.com/help/2647098) | 
| 等待同步 | 已应用修复程序。 正在等待下一个同步周期清除错误。 |
>[!IMPORTANT]
> 在每个同步周期后，将会重置诊断状态列。 
>

2.  在错误详细信息边栏选项卡中单击“诊断”按钮后，必须回答几个问题，并识别同步错误详细信息。 回答问题有助于识别孤立对象场景的原因。 

3.  如果结束诊断时出现“关闭”按钮，则表示根据给定的回答，没有可从门户实施的快速解决方法。 请参考最后一个步骤中显示的解决方法。 解决方法仍旧是在本地解决。 单击“关闭”按钮后，会发现当前同步错误的状态会切换到“需要手动修复”。 在当前同步周期会一直保留该状态。

4.  识别孤立对象的原因后，可以直接从门户解决重复属性同步错误。 单击“应用修复”按钮触发该过程。 当前同步错误的状态将更新为“等待同步”。

5.  完成接下来的同步周期后，该错误应会在列表中消除。

## <a name="how-to-answer-the-diagnosis-questions"></a>如何回答诊断问题 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>该用户是否在本地 Active Directory 中存在？

该问题的目的是试图识别本地 Active Directory 中现有用户的源对象。  
1.  检查 Active Directory 是否包含具有提供的 UserPrincipalName 的对象。 如果没有，则回答“否”。
2.  如果有，请检查该对象是否仍在同步的范围内。  
  - 在具有此 DN 的 Azure AD 连接器空间中搜索。
  - 如果找到了具有“等待添加”状态的对象，则回答“否”。 Azure AD Connect 无法将对象连接到正确的 AD 对象。
  - 如果找不到该对象，则回答“是”。

> 以下图为例，该问题的目的是试图识别本地 Active Directory 中是否仍然存在 *Joe Jackson*。
在**一般的场景**中，用户 *Joe Johnson* 和 *Joe Jackson* 都会在本地 Active Directory 中。 隔离的对象是两个不同的用户。

![诊断常见的同步错误场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> 在**孤立对象的场景**中，只有单个用户（即 *Joe Johnson*）在本地 Active Directory 中：

![诊断同步错误孤立对象的场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>这两个帐户是否属于同一用户？
此问题的目的是检查传入的有冲突用户和 Azure AD 中的现有用户对象，以确定两者是否属于同一用户。  
1.  有冲突的对象最近已同步到 Azure Active Directory。 根据以下属性比较对象：  
  - 显示名称
  - 用户主体名称
  - 对象 ID
2.  如果无法对它们进行比较，请检查 Active Directory 是否包含具有提供的 UserPrincipalNames 的对象。 如果两者均可找到，则回答“否”。  

> 在以下情况下，两个对象属于同一个用户 *Joe Johnson*。

![诊断同步错误孤立对象的场景](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>对孤立对象场景应用修复后发生的情况
根据提问的回答，如果可以通过 Azure AD 进行修复，则会显示“应用修复”按钮。 在这种情况下，表示本地对象正在与意外的 Azure AD 对象同步。 两个对象是使用“源定位点”映射的。 应用更改操作会执行如下所述的步骤：
- 将源定位点更新为 Azure AD 中的正确对象。
- 删除 Azure AD 中有冲突的对象（如果存在）。

![修复后诊断同步错误](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> “应用修复”时所做的更改只适用于孤立对象的情况。
>

完成上述步骤后，用户可以访问链接到现有对象的原始资源。 列表视图中的“诊断状态”值将会更新为“等待同步”。完成后续的同步后，将会解决同步错误。 Connect Health 将不再在列表视图中显示已解析的同步错误。 


## <a name="faq"></a>常见问题
 -  如果应用操作执行失败，会发生什么情况？  
如果执行失败，原因可能是 Azure AD Connect 当时遇到导出错误。 请刷新门户页，并在完成后续的同步后重试。 默认同步周期为 30 分钟。 

 -  如果**现有对象**是要删除的对象该怎么办？  
如果在此情况下应删除现有对象，则该过程不涉及到源定位点的更改。 应该可以从本地 AD 解决此问题。  

 -  用户需有哪种权限才能应用修复？  
在 RBAC 设置中指定的全局管理员或参与者有权访问诊断和故障排除过程。

 -  是否需要为此功能配置 AAD Connect 或更新 Azure AD Connect Health 代理？  
不需要，诊断过程是完全基于云的功能。

 -  如果软删除了现有对象，诊断过程是否会再次将该对象还原为活动状态？  
不会，修复过程不会更新除源定位点以外的对象属性。 
