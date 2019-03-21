---
title: 故障排除：下载的 Azure Active Directory 活动日志中缺少数据 | Microsoft 文档
description: 为提供了一种解决方法，解决在下载的 Azure Active Directory 活动日志中缺少数据的问题。
services: active-directory
documentationcenter: ''
author: priyamohanram
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
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfb56ea81abeeba83bee73356c682b3e9fae866f
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58292903"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>无法在已下载的 Azure Active Directory 活动日志中找到所有数据

## <a name="symptoms"></a>症状

我下载了活动日志（审核日志或登录日志），但发现所选时间范围内的记录不全。 为什么？ 

 ![报告](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

下载时在 Azure 门户中的活动日志，我们将规模限制为 250,000 记录，按最新的第一个排序。 

## <a name="resolution"></a>解决方法

可以随时通过 [Azure AD 报告 API](concept-reporting-api.md) 获取多达一百万条记录。

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 报告常见问题解答](reports-faq.md)

