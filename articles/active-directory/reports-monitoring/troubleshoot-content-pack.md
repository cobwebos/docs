---
title: Azure Active Directory 活动日志内容包错误故障排除 | Microsoft 文档
description: 本文将主要介绍 Azure Active Directory 活动内容包的错误消息列表，以及修复这些错误的步骤。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78676ac2f2dcff74a27e0260a5d83e924f7c246f
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434817"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Azure Active Directory 活动日志内容包错误故障排除 

|  |
|--|
|目前，Azure AD Power BI 内容包使用 Azure AD Graph API 检索 Azure AD 租户中的数据。 因此，你可能会发现内容包中可用数据与使用[用于报告的 Microsoft Graph API](concept-reporting-api.md) 检索的数据之间存在一些差异。 |
|  |

在使用 Azure Active Directory (Azure AD) 的 Power BI 内容包时，可能会遇到以下错误： 

- [刷新失败](troubleshoot-content-pack.md#refresh-failed) 
- [无法更新数据源凭据](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [导入数据的时间过长](#data-import-is-too-slow) 

本文提供了有关可能的原因以及如何修复这些错误的信息。
 
## <a name="refresh-failed"></a>刷新失败 
 
**此错误是如何发现的**：根据 Power BI 的电子邮件或刷新历史记录中的失败状态。 


| 原因 | 如何解决 |
| ---   | ---        |
| 当连接到内容包的用户的凭据被重置，但在内容包的连接设置中未更新时，可能会导致刷新失败错误。 | 在 Power BI 中，找到与 Azure AD 活动日志仪表板（**Azure Active Directory 活动日志**）相对应的数据集，选择计划刷新，然后输入你的 Azure AD 凭据。 |
| 由于基础内容包中的数据问题可能会导致刷新失败。 | [提交一个支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。|
 
 
## <a name="failed-to-update-data-source-credentials"></a>无法更新数据源凭据 
 
**此错误是如何发现的**：在 Power BI 中，当连接到 Azure AD 活动日志内容包时，会出现此错误。 

| 原因 | 如何解决 |
| ---   | ---        |
| 连接的用户既不是全局管理员，也不是安全读取者或安全管理员。 | 使用全局管理员、安全读取者或安全管理员的帐户来访问内容包。 |
| 你的租户不是高级租户，或者至少没有一个拥有高级许可文件的用户。 | [提交一个支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。|
 


## <a name="data-import-is-too-slow"></a>数据导入速度太慢 
 
**此错误是如何发现的**：在 Power BI 中，在连接内容包后，数据导入过程开始为 Azure AD 活动日志准备仪表板。 将显示以下消息：**正在导入数据...** 且没有进一步的进展。  

| 原因 | 如何解决 |
| ---   | ---        |
| 根据租户大小，此步骤可能会需要从几分钟到 30 分钟不等的时间。 | 如果该消息在一个小时内没有更改显示你的仪表板，请[提交一个支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。|

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure AD 报告 Power BI 内容包](quickstart-install-power-bi-content-pack.md)。
* [将 Power BI 内容包用于 Azure AD 报表来可视化数据](howto-power-bi-content-pack.md)
* [如何获取对 Azure Active Directory 的支持](../fundamentals/active-directory-troubleshooting-support-howto.md)
