---
title: Azure Active Directory 标识保护常见问题解答 | Microsoft Docs
description: 有关 Azure AD 标识保护的常见问题解答
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d36b513b6835088b5120072906667cd30af505d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025740"
---
# <a name="azure-active-directory-identity-protection-faq"></a>Azure Active Directory 标识保护常见问题解答

本文包括了对 Azure Active Directory (Azure AD) 标识保护常见问题的解答。 有关详细信息，请参阅 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。 

## <a name="why-do-some-risk-detections-have-closed-system-status"></a>为什么某些风险检测的状态为 "已关闭（系统）"？

**答：** 这些风险检测已被标识保护检测到，以后关闭，因为这些事件不再被视为有风险。 这些事件不会计入用户的风险级别。 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>是否需要是全局管理员，才能在 Azure 门户中使用“标识保护”？
**答：** 否。 若要使用“标识保护”，可以是安全读取者、安全管理员或全局管理员。

---

## <a name="how-do-i-get-identity-protection"></a>如何获取“标识保护”？

**答：** 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)获取此问题的解答。

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>如何对“已标记为存在风险的用户”中的用户进行排序？

**答：** 通过单击 "已**标记为风险的用户**" 页顶部的 "**下载**"，下载标记为风险的用户报表。 然后即可基于可用字段（包括“上次更新时间 [UTC]”），对下载的数据进行排序。

---
