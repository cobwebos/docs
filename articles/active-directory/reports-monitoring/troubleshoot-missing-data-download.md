---
title: 故障排除：下载的活动日志中缺少数据 |Microsoft Docs
description: 提供了一种解决方法，用于解决在下载的 Azure Active Directory 活动日志中缺少数据的问题。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5003d7b644a0c75401a17ed6a37f31acd8180aa9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85608069"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>无法在已下载的 Azure Active Directory 活动日志中找到所有数据

## <a name="symptoms"></a>症状

我下载了活动日志（审核日志或登录日志），但发现所选时间范围内的记录不全。 为什么？ 

 ![报表](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

下载 Azure 门户中的活动日志时，会将扩展限制为250000个记录，并按最新的顺序进行排序。 

## <a name="resolution"></a>解决方法

可以随时通过 [Azure AD 报告 API](concept-reporting-api.md) 获取多达一百万条记录。

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 报告常见问题解答](reports-faq.md)

