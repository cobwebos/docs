---
title: "Azure Active Directory 混合标识设计注意事项 - 确定多重身份验证要求"
description: "借助条件性访问控制，Azure Active Directory 会在验证用户身份时先检查你选取的特定条件，然后才允许访问应用程序。 一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。"
documentationcenter: 
services: active-directory
author: femila
manager: billmath
editor: 
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4491d7385b9cfc2105e83f0e1099b68cde75feec


---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>确定混合标识解决方案的多重身份验证要求
在充斥移动性的当下，用户访问云中和任何设备中的数据和应用程序，确保该信息安全变得尤为重要。  每天都会有关于安全漏洞的新闻头条。  尽管并不能保证避免此类漏洞，但多重身份验证提供了额外的安全层，可帮助防止利用这些漏洞。
首先评估多重身份验证的组织要求。 即，组织要尝试保护的内容。  必须对定义为设置和支持组织用户进行多重身份验证的技术要求进行评估。

> [!NOTE]
> 如果不熟悉 MFA 及其作用，强烈建议你阅读[什么是 Azure 多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)一文，然后再继续阅读本节。
> 
> 

务必回答以下问题：

* 贵公司是否会尝试保护 Microsoft 应用安全？ 
* 如何发布这些应用？
* 贵公司是否会提供允许员工访问本地应用的远程访问？

如果是，是何种类型的远程访问？还需要评估要访问这些应用程序的用户将位于何处。 对定义正确的多重身份验证策略而言，此评估是另一个重要步骤。 务必回答以下问题：

* 用户将位于何处？
* 用户是否可以位于任何位置？
* 贵公司是否想要根据用户的位置建立限制？

在了解了这些要求后，还必须评估多重身份验证的用户的要求。 此评估非常重要，因为将针对推出多重身份验证定义要求。 务必回答以下问题：

* 用户是否熟悉多重身份验证？
* 某些使用是否需要提供额外的身份验证？  
  * 如果是，始终、从外部网络访问时、访问特定应用程序，还是在其他情况下？
* 用户是否需要培训如何设置和实施多重身份验证？
* 什么是贵公司想要为其用户启用多重身份验证的关键方案？

在回答完上述问题后，将能够了解本地是否存在已实施的多重身份验证。 必须对定义为设置和支持组织用户进行多重身份验证的技术要求进行评估。 务必回答以下问题：

* 贵公司是否需要保护使用 MFA 的特权帐户？
* 贵公司出于合规性原因，是否需要为某些应用程序启用 MFA？
* 贵公司是否需要为这些应用程序的所有合格用户或仅管理员启用 MFA？
* 需要始终启用 MFA，还是仅当用户在公司网络之外登录时启用 MFA？

## <a name="next-steps"></a>后续步骤
[定义混合标识采用策略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)




<!--HONumber=Dec16_HO5-->


