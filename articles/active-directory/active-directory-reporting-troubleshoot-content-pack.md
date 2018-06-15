---
title: Azure Active Directory 活动日志内容包错误故障排除 | Microsoft 文档
description: 本文将主要介绍 Azure Active Directory 活动内容包的错误消息列表，以及修复这些错误的步骤。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 801c425d0d467f8df63f981a1a03c6d202b4c3c5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589213"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Azure Active Directory 活动日志内容包错误故障排除 


在使用 Azure Active Directory 预览版的 Power BI 内容包，可能会遇到以下错误： 

- [刷新失败](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [无法更新数据源凭据](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [导入数据的时间过长](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
本主题提供了有关可能的原因以及如何修复这些错误的信息。
 
## <a name="refresh-failed"></a>刷新失败 
 
此错误是如何发现的：根据 Power BI 的电子邮件或刷新历史记录中的失败状态。 


| 原因 | 如何解决 |
| ---   | ---        |
| 当连接到内容包的用户的凭据被重置，但在内容包的连接设置中未更新时，可能会导致刷新失败错误。 | 在 Power BI 中，找到与 Azure Active Directory 活动日志仪表板（Azure Active Directory 活动日志）相对应的数据集，选择计划刷新，然后输入你的 Azure AD 凭据。 |
| 由于基础内容包中的数据问题可能会导致刷新失败。 | 请提交一个支持票证。 有关详细信息，请参阅[如何获取对 Azure Active Directory 的支持](active-directory-troubleshooting-support-howto.md)。|
 
 
## <a name="failed-to-update-data-source-credentials"></a>无法更新数据源凭据 
 
此错误是如何发现的：在 Power BI 中，当连接到 Azure Active Directory 活动日志（预览版）内容包时，会出现此错误。 

| 原因 | 如何解决 |
| ---   | ---        |
| 连接的用户既不是全局管理员，也不是安全读者或安全管理员。 | 请使用全局管理员、安全读者或安全管理员的帐户来访问内容包。 |
| 你的租户不是高级租户，或者至少没有一个拥有高级许可文件的用户。 | 请提交一个支持票证。 有关详细信息，请参阅[如何获取对 Azure Active Directory 的支持](active-directory-troubleshooting-support-howto.md)。|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>导入数据的时间过长 
 
此错误是如何发现的：在 Power BI 中，一旦连接内容包后，数据导入进程就会开始为 Azure Active Directory 活动日志准备仪表板。 你会看到消息：“正在导入数据...”  

| 原因 | 如何解决 |
| ---   | ---        |
| 根据租户大小，此步骤可能会需要从几分钟到 30 分钟不等的时间。 | 请耐心等待。 如果该消息在一个小时内没有更改显示你的仪表板，请提交支持票证。 有关详细信息，请参阅[如何获取对 Azure Active Directory 的支持](active-directory-troubleshooting-support-howto.md)。|

## <a name="next-steps"></a>后续步骤

若要为 Azure Active Directory 预览版安装 Power BI 内容包，请单击[此处](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/)。


