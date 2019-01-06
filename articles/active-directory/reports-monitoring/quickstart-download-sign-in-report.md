---
title: 快速入门：通过 Azure 门户下载登录报表 | Microsoft Docs
description: 了解如何通过 Azure 门户下载登录报表
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3e20af1c90f0e8a7a582d2d01dc4218a14496c40
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653282"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>快速入门：通过 Azure 门户下载登录报表

在此快速入门中，你将学习如何下载租户过去 24 小时的登录数据。

## <a name="prerequisites"></a>先决条件

需要：

* 一名 Azure Active Directory 租户，具有可查看登录活动报表的高级许可证。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。
* 一位属于该租户的安全管理员、安全读者、报表读者或全局管理员角色的用户。 此外，该租户中的用户均可访问其自己的登录信息。

## <a name="quickstart-download-a-sign-in-report"></a>快速入门：下载登录报表

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 从左侧导航窗格中选择“Azure Active Directory”，然后使用“切换目录”按钮选择活动目录。
3. 从仪表板中，选择 Azure Active Directory，然后选择“登录”。 
4. 在“日期”筛选器下拉列表中选择“过去 24 小时”，再选择“应用”以查看过去 24 小时的登录信息。 
5. 选择“下载”按钮，以下载包含所筛选记录的 CSV 文件。 

![报告](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 门户中的“登录活动”报表](concept-sign-ins.md)
* [Azure Active Directory 报告保留期](reference-reports-data-retention.md)
* [Azure Active Directory 报告延迟](reference-reports-latencies.md)
