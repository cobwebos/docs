---
title: "Azure Active Directory PoC 操作手册成分 | Microsoft Docs"
description: "研究并快速实现标识和访问管理方案"
services: active-directory
keywords: "Azure Active Directory, 操作手册, 概念证明, PoC"
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2017
ms.author: dstefan
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 2a555326e5bb2eb7b89e80be721b20488c4f7a2d
ms.lasthandoff: 04/12/2017


---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Azure Active Directory 概念证明操作手册成分 

## <a name="theme"></a>主题
Azure AD 在企业的多个领域提供标识和访问解决方案。 我们将方案分类为以下几个方面： 

* [多个应用，一个标识](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [增强安全性](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [通过自助服务进行缩放](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

通过定义主题来构造 PoC，有助于集中与业务目标产生共鸣的努力成果，这通常是一开始关注概念证明的动机。 

## <a name="environment"></a>环境

请务必确定将提供 PoC 的环境的具体细节。 理想情况下，可在 PoC 完成后在该环境上生成。 目标环境非常重要，并且应在尽可能使其在真实与约束开销或额外关注之间寻求最佳平衡。 典型的 PoC 环境有：
* **生产：**该方案将在实时环境中实现，且已部署 Microsoft 云服务（生产 AD、Office 365、Azure AD 租户/SSO 解决方案）。 
* **用户验收测试 (UAT)/开发环境：**具有测试基础结构（并行 AD 和潜在的 Azure AD 租户/SSO 解决方案），结构中包含类似于生产的测试数据。 通常情况下，测试环境在企业的多个项目之间共享。

本指南中的大多数方案本质上是附加内容。 因此，可在生产租户中部署它们，而不会影响 PoC 外的用户。 在整个文档中，我们将出指出哪些方案会在租户范围内产生影响。 在这些情况下，可能需要考虑非生产环境。 


## <a name="target-users"></a>目标用户

请务必确定将演练方案的目标用户集，尤其是当环境是生产环境或测试环境时。 PoC 的目标用户类别有：
* **试点用户：**环境中的真实用户，他们将该解决方案与他们用于日常工作职能的帐户一起使用
* **测试用户：**在环境中创建的测试帐户 

本指南中的大多数方案可由试点用户演练。 在整个文档中，我们将出指出目标用户的注意事项（如果必要）。


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
