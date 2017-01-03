---
title: "Azure Active Directory 混合标识设计注意事项 - 确定访问控制要求 | Microsoft 文档"
description: "介绍标识的重要组成部分，确定混合环境中用户对资源的访问权限的要求。"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 032467e0885d574a7b0f4a1c043e1800e5cc4071


---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>确定混合标识解决方案的访问控制要求
组织在设计混合标识解决方案时，也可以借机评审正在规划中的、用户对资源的访问权限要求。 数据访问权限涉及到标识的所有四个重要组成部分：

* 管理
* 身份验证
* 授权
* 审核

后续部分将更详细说明身份验证与授权，而管理和审核是混合标识生命周期的组成部分。 有关这些功能的详细信息，请参阅[确定混合标识管理任务](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)。

> [!NOTE]
> 有关每个重要组成部分的详细信息，请参阅[标识的四个重要组成部分 - 混合 IT 生命周期内的标识管理](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx)。
> 
> 

## <a name="authentication-and-authorization"></a>身份验证和授权
有各种适用于身份验证和授权的方案，这些方案具有特殊要求，必须通过公司要采用的混合标识解决方案来满足。 涉及企业到企业 (B2B) 通信的方案会给 IT 管理员带来额外的挑战，因为他们需要确保组织使用的身份验证和授权方法能够与他们的业务合作伙伴通信。 在设计身份验证和授权要求的过程中，请务必回答以下问题：

* 组织是否只验证和授权位于其标识管理系统中的用户？
  * 是否针对 B2B 方案制定了任何计划？
  * 如果是，是否知道要使用哪些协议（SAML、OAuth、Kerberos、令牌或证书）来连接这两家企业？
* 要采用的混合标识解决方案是否支持这些协议？

另一个要考虑的要点是用户与合作伙伴要使用的身份验证存储库位于何处，以及要使用哪种管理模型。 请考虑以下两个核心选项：

* 集中式：在此模型中，用户的凭据、策略和管理可以集中在本地或云中。
* 混合：在此模型中，用户的凭据、策略和管理集中在本地，并在云中复制。

组织采用哪种模型根据其业务要求的不同而异。请回答以下问题，识别标识管理系统所在的位置以及要使用的管理模式：

* 组织当前是否有本地标识管理？
  * 如果有，打算如何保留这种管理？
  * 是否有任何法规要求规定组织必须将标识管理系统部署在何处？
* 组织是否针对本地或云中的应用使用单一登录？
  * 如果是，采用混合标识模型是否会影响此过程？

## <a name="access-control"></a>Access Control
尽管身份验证和授权是核心元素，可通过用户的身份验证来实现对企业数据的访问，但控制这些用户拥有的访问级别以及管理员对其管理的资源拥有的访问级别也同样重要。 混合标识解决方案必须能够对资源、委托和基于角色的访问控制提供更细微的访问权限。 请务必回答以下有关访问控制的问题：

* 公司中是否指派了多个拥有提升权限的用户来管理标识系统？
  * 如果是，每个用户是否需要相同的访问级别？
* 公司是否需要向用户委托访问权限来管理特定的资源？
  * 如果是，发生这种情况的频繁度如何？
* 公司是否需要集成本地与云资源之间的访问控制功能？
* 公司是否需要根据相同的条件来限制资源访问权限？
* 公司是否有任何应用程序需要某些资源的自定义控制访问权限？
  * 如果是，这些应用位于何处（本地还是云中）？
  * 如果是，这些目标资源位于何处（本地还是云中）？

> [!NOTE]
> 务必记下每个答案并了解答案背后的依据。 [定义数据保护策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)会检查可用选项以及每个选项的优点/缺点。  回答这些问题后，就能选出最适合业务需求的选项。
> 
> 

## <a name="next-steps"></a>后续步骤
[确定事件响应要求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)




<!--HONumber=Dec16_HO5-->


