---
title: "Azure Data Lake Analytics 配额限制 | Microsoft Docs"
description: "了解如何调整和增加 Azure Data Lake Analytics (ADLA) 帐户中的配额限制。"
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b68138f04eea1a21731118803d14698320e00be0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics 配额限制
了解如何调整和增加 Azure Data Lake Analytics (ADLA) 帐户中的配额限制。 了解这些限制有助于理解 U-SQL 作业行为。 所有这些限制是软限制，可通过联系我们随时增加最大限制。

## <a name="azure-subscriptions-limits"></a>Azure 订阅限制

**每个订阅的最大 ADLA 帐户数：**5。 这是可对每个订阅创建的最大 ADLA 帐户数。 尝试创建第 6 个 ADLA 帐户时，会收到此错误“已达到订阅名称区域中允许的 Data Lake Analytics 帐户的最大数量(5)”。 通过删除订阅中使用的 ADLA 帐户或联系我们打开支持票证便可轻松解决该问题。

## <a name="adla-account-limits"></a>ADLA 帐户限制

**每个帐户的最大分析单位数 (AU)：**250。 

这是可在帐户中同时运行的最大 AU 数。 跨所有作业运行的总 AU 数不能超过该值。 超出此值将导致新作业自动进行排队。 例如：

  * 用户可能正在以 250 AU 运行一项作业，当提交第二项作业时，该作业会排列在作业队列中，直到第一项作业完成。
  * 用户可能已在运行 5 项作业，每项以 50 AU 提交，当以一定 AU（如 20）提交第 6 项作业时，它将在作业队列中等待，并在有可用的 20 AU 后开始运行。


**每个帐户并发 U-SQL 作业的最大数：**20。 

这是可在帐户中同时运行的最大作业数。 超出此值将导致新作业自动进行排队。

## <a name="adjust-the-adla-quota-limits-per-account"></a>调整每个帐户的 ADLA 配额限制

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择已创建的 ADLA 帐户
3. 单击“属性”
4. 根据需要调整**并行**和**并发作业**。

    ![Azure Data Lake Analytics 门户边栏选项卡](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="to-increase-the-max-quota-limits"></a>增加最大配额限制

1. 在 Azure 门户中打开支持请求。

    ![Azure Data Lake Analytics 门户边栏选项卡](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics 门户边栏选项卡](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. 对问题类型选择“配额”
3. 选择“订阅”（确保不是“试用版”订阅）。
4. 对配额类型选择“Data Lake Analytics”

    ![Azure Data Lake Analytics 门户边栏选项卡](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. 在问题边栏选项卡中，请说明你请求的限制提升以及为何需要此额外容量的**详细信息**。

    ![Azure Data Lake Analytics 门户边栏选项卡](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. 验证联系信息并创建支持请求。

Microsoft 会审核你的请求，并尽快满足你的业务需求。

## <a name="next-steps"></a>后续步骤
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

