---
title: "Azure Active Directory 报告保留策略 | Microsoft 文档"
description: "有关 Azure Active Directory 中报表数据的保留策略"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/05/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: aa7a69c933abfda3bf4d1ac1a298c4ba684efd7e
ms.contentlocale: zh-cn
ms.lasthandoff: 04/06/2017

---
# Azure Active Directory 报告保留策略
<a id="azure-active-directory-report-retention-policies" class="xliff"></a>


本主题解答有关 Azure Active Directory 中不同活动报告的数据保留期的最常见问题。 

**问：如何开始收集活动数据？**

**答：**

| Azure AD 版本 | 开始收集 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 注册订阅时 |
| Azure AD Free | 首次打开 [Azure Active Directory 边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)或使用[报告 API](https://aka.ms/aadreports) 时  |

---
**问：活动数据何时出现在 Azure 门户中？**

**答：**

- **立即** - 如果已在 Azure 经典门户中使用报告
- **2 小时内** - 如果尚未在 Azure 经典门户中启用报告

---
**问：如何开始收集安全信号？**  

**答：**当用户选择使用标识防护中心时，收集安全信号的过程将会开始。 


---
**问：收集的数据将存储多久？**

**答：**

**活动报告**    

| 报表                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 目录审核        | 7 天        | 30 天             | 30 天             |
| 登录活动       | 7 天        | 30 天             | 30 天             |

**安全信号**

| 报表         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| 有风险的用户  | 7 天        | 30 天             | 90 天             |
| 有风险的登录 | 7 天        | 30 天             | 90 天             |

---
