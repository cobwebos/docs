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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: abd64b7d2fa7930f5b6177c7ac037840da34dc18
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333575"
---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory 报告保留策略

本文介绍 Azure Active Directory 中不同活动报告的数据保留策略。 

### <a name="when-does-azure-ad-start-collecting-data"></a>Azure AD 何时开始收集数据？

| Azure AD 版本 | 开始收集 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 注册订阅时 |
| Azure AD Free <br /> Azure AD Basic | 首次打开 [Azure Active Directory 边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)或使用[报告 API](https://aka.ms/aadreports) 时  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>活动数据何时出现在 Azure 门户中？

- **立即** - 如果已在 Azure 门户中使用报告。
- **2 小时内** - 如果尚未在 Azure 门户中启用报告。

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Azure AD 何时开始收集安全信号数据？  

当用户选择使用**标识防护中心**时，收集安全信号的过程会开始。 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Azure AD 存储数据多长时间？

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
