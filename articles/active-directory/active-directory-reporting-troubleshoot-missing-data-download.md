---
title: "故障排除：下载的 Azure Active Directory 活动日志中缺少数据 - 预览 | Microsoft 文档"
description: "为你提供了一种解决方法，解决在下载的 Azure Active Directory 活动日志预览中缺少数据的问题。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: e0d65edcb7c14114565402038b0958c3a2ffb477
ms.lasthandoff: 03/10/2017


---

# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>我在下载的 Azure Active Directory 活动日志中找不到任何数据


## <a name="symptoms"></a>症状

我下载了活动日志（审核日志或登录日志），但发现所选时间范围内的记录不全。 为什么？ 

 ![报告](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>原因

当你在 Azure 门户中下载活动日志时，我们将规模限制为 120K 条记录，最新的记录排在最前面。 

## <a name="resolution"></a>解决方法

你可以随时通过 [Azure AD 报告 API](active-directory-reporting-api-getting-started.md) 获取多达一百万条记录。 我们建议的方法是，按计划运行脚本，通过调用报告 API 以增量方式获取某个时段（例如每日或每周）的记录。

## <a name="next-steps"></a>后续步骤
请参阅 [Azure Active Directory 报告常见问题解答](active-directory-reporting-faq.md)。


