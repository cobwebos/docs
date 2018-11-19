---
title: 故障排除：下载的 Azure Active Directory 活动日志中缺少数据 | Microsoft Docs
description: 为提供了一种解决方法，解决在下载的 Azure Active Directory 活动日志中缺少数据的问题。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2607c5dacf6f261f27e7805e02df189a2753404c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625636"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>无法在已下载的 Azure Active Directory 活动日志中找到所有数据

## <a name="symptoms"></a>症状

我下载了活动日志（审核日志或登录日志），但发现所选时间范围内的记录不全。 为什么？ 

 ![报告](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

在 Azure 门户中下载活动日志时，我们将规模限制为 5000 条记录，最新的记录排在最前面。 

## <a name="resolution"></a>解决方法

可以随时通过 [Azure AD 报告 API](concept-reporting-api.md) 获取多达一百万条记录。 我们建议的方法是，[按计划运行脚本](tutorial-signin-logs-download-script.md)，通过调用报告 API 以增量方式获取某个时段（例如每日或每周）的记录。 

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 报告常见问题解答](reports-faq.md)

