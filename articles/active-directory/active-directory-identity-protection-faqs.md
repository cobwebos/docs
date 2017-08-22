---
title: "Azure Active Directory Identity Protection 常见问题解答 | Microsoft Docs"
description: "有关 Azure AD Identity Protection 的常见问题解答"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 781f868c87cea3cd790d89c61e26eecf352babea
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

---
# <a name="azure-active-directory-identity-protection-faq"></a>Azure Active Directory Identity Protection 常见问题解答


## <a name="why-do-some-risk-events-have-closed-system-status"></a>为何某些风险事件具有“已关闭(系统)”状态？

**答：**这些是 Azure Active Directory Identity Protection 检测到的事件，之所以稍后关闭是因为已不再认为这些事件有风险。 这些事件不会计入用户的风险级别。 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>是否需要是全局管理员，才能在 Azure 门户中使用 Identity Protection？
**答：****不需要**。 若要使用 Identity Protection，可以是安全读者、安全管理员或全局管理员。

---

## <a name="how-do-i-get-identity-protection"></a>如何获取 Identity Protection？
**答：**请参阅 [Azure Active Directory Premium 入门](active-directory-get-started-premium.md)获取此问题的解答。

---

## <a name="what-happens-when-your-azure-active-directory-premium-2-trial-expires"></a>Azure Active Directory Premium 2 试用版过期时，会发生什么情况？

答：如果 Azure Active Directory Premium 2 试用版在 Azure Active Directory Free、Azure Active Directory Basic 或 Azure Active Directory Premium 1 版本上已到期，并且已启用 Identity Protection，即使不符合授权，但仍可继续进行访问。

---

