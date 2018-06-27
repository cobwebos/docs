---
title: Azure Active Directory 报告保留策略 | Microsoft 文档
description: 有关 Azure Active Directory 中报表数据的保留策略
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8edce7d1a633148cf361deec0c3d09ef4c16ac9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225172"
---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory 报告保留策略


本文解答有关 Azure Active Directory 中不同活动报告的数据保留期的最常见问题。 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>问：如何开始收集活动数据？

**答：**

| Azure AD 版本 | 开始收集 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 注册订阅时 |
| Azure AD Free | 首次打开 [Azure Active Directory 边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)或使用[报告 API](https://aka.ms/aadreports) 时  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>问：活动数据何时出现在 Azure 门户中？

**答：**

- **立即** - 如果已在 Azure 门户中使用报告。
- **2 小时内** - 如果尚未在 Azure 门户中启用报告。

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>问：如何开始收集安全信号？  

**答：** 当用户选择使用标识防护中心时，收集安全信号的过程会开始。 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>问：收集的数据将存储多久？

**答：**

**活动报告**    

| 报表                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 目录审核        | 7 天        | 30 天             | 30 天             |
| 登录活动       | 不适用           | 30 天             | 30 天             |
| Azure MFA 使用情况        | 30 天       | 30 天             | 30 天             |

**安全信号**

| 报表         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| 有风险的用户  | 7 天        | 30 天             | 90 天             |
| 有风险的登录 | 7 天        | 30 天             | 90 天             |

---
