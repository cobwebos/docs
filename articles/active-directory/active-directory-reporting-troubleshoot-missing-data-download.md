---
title: "故障排除：下载的 Azure Active Directory 活动日志中缺少数据 | Microsoft Docs"
description: "为提供了一种解决方法，解决在下载的 Azure Active Directory 活动日志中缺少数据的问题。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: d976e2476001ad4d23e55913681500f14e285014
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2018
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>我在下载的 Azure Active Directory 活动日志中找不到任何数据


## <a name="symptoms"></a>症状

我下载了活动日志（审核日志或登录日志），但发现所选时间范围内的记录不全。 为什么？ 

 ![报告](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>原因

在 Azure 门户中下载活动日志时，我们将规模限制为 120K 条记录，最新的记录排在最前面。 

## <a name="resolution"></a>解决方法

可以随时通过 [Azure AD 报告 API](active-directory-reporting-api-getting-started.md) 获取多达一百万条记录。 我们建议的方法是，按计划运行脚本，通过调用报告 API 以增量方式获取某个时段（例如每日或每周）的记录。

## <a name="next-steps"></a>后续步骤
请参阅 [Azure Active Directory 报告常见问题解答](active-directory-reporting-faq.md)。

