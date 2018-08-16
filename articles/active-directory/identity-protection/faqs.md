---
title: Azure Active Directory Identity Protection 常见问题解答 | Microsoft Docs
description: 有关 Azure AD Identity Protection 的常见问题解答
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: f3123ed9a6d4898889b36e29c34c6c6a7496ea0d
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005444"
---
# <a name="azure-active-directory-identity-protection-faq"></a>Azure Active Directory Identity Protection 常见问题解答

本文包括了对 Azure Active Directory (Azure AD) 标识保护常见问题的解答。 有关详细信息，请参阅 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。 


## <a name="why-do-some-risk-events-have-closed-system-status"></a>为何某些风险事件具有“已关闭(系统)”状态？

答：这些是 Identity Protection 检测到的风险事件，之所以稍后关闭是因为已不再认为这些事件有风险。 这些事件不会计入用户的风险级别。 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>是否需要是全局管理员，才能在 Azure 门户中使用 Identity Protection？
**答：** 否。 若要使用 Identity Protection，可以是安全读者、安全管理员或全局管理员。

---

## <a name="how-do-i-get-identity-protection"></a>如何获取 Identity Protection？

**答：** 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)获取此问题的解答。

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>如何对“已标记为存在风险的用户”中的用户进行排序？

答：在“已标记为存在风险的用户”页面的顶部单击“下载”，下载“已标记为存在风险的用户”报表。 然后即可基于可用字段（包括“上次更新时间 (UTC)”），对下载数据进行排序。

---
