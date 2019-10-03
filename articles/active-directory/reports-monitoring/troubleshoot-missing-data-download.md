---
title: 故障排除：下载的 Azure Active Directory 活动日志中缺少数据 | Microsoft Docs
description: 提供了一种解决方法，用于解决在下载的 Azure Active Directory 活动日志中缺少数据的问题。
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f120c1b86efe94f4ff6316e6116b9049582b07e9
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987979"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>无法在已下载的 Azure Active Directory 活动日志中找到所有数据

## <a name="symptoms"></a>症状

我下载了活动日志（审核日志或登录日志），但发现所选时间范围内的记录不全。 为什么? 

 ![报告](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

下载 Azure 门户中的活动日志时, 会将扩展限制为250000个记录, 并按最新的顺序进行排序。 

## <a name="resolution"></a>解决

可以随时通过 [Azure AD 报告 API](concept-reporting-api.md) 获取多达一百万条记录。

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 报告常见问题解答](reports-faq.md)

