---
title: Azure Data Lake Analytics 配额限制
description: 了解如何调整和增加 Azure Data Lake Analytics (ADLA) 帐户中的配额限制。
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: 22774511720173915207da80a6ca33d5dbc83e19
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics 配额限制

了解如何调整和增加 Azure Data Lake Analytics (ADLA) 帐户中的配额限制。 了解这些限制有助于理解 U-SQL 作业行为。 所有配额限制都是软限制，因此随时可通过联系 Azure 支持增加最大限制。

## <a name="azure-subscriptions-limits"></a>Azure 订阅限制

**每个订阅的最大 ADLA 帐户数：**5

这是可为每个区域的每个订阅创建的最大 ADLA 帐户数。 如果尝试创建第 6 个 ADLA 帐户，将收到错误“已达到订阅名称区域中允许的 Data Lake Analytics 帐户的最大数量 (5)”。 在此情况下，可以选择另一个区域（如果合适）或删除同一区域中任何未使用的 ADLA 帐户，或通过[开具支持票证](#increase-maximum-quota-limits)联系 Azure 支持以请求增加配额。

## <a name="adla-account-limits"></a>ADLA 帐户限制

**每个帐户的最大分析单位数 (AU)：**250

这是可在帐户中同时运行的最大 AU 数。 如果所有作业上正在运行的 AU 总数超出此限制，较新的作业将自动排队。 例如：

* 如果正在以 250 AU 运行一个作业，当提交第二个作业时，该作业会在作业队列中等待，直到第一个作业完成。
* 如果有五个作业正在运行，每个作业使用 50 AU，提交需要 20 AU 的第六个作业时，该作业会在作业队列中等待，直到有 20 AU 可用。

**每个帐户的最大并发 U-SQL 作业数：**20

这是可在帐户中同时运行的最大作业数。 大于此值时，较新的作业将自动排队。

## <a name="adjust-adla-quota-limits-per-account"></a>调整每个帐户的 ADLA 配额限制

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择一个现有的 ADLA 帐户。
3. 单击“属性”。
4. 根据需要调整**并行**和**并发作业**。

    ![Azure Data Lake Analytics 门户页](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>增加最大配额限制

1. 在 Azure 门户中提出支持请求。

    ![Azure Data Lake Analytics 门户页](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics 门户页](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. 对问题类型选择“配额”。
3. 选择“订阅”（确保不是“试用版”订阅）。
4. 对配额类型选择“Data Lake Analytics”。

    ![Azure Data Lake Analytics 门户页](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. 在问题页中，提供有关为何需要此额外容量的**详细信息**来说明请求提高上限的原因。

    ![Azure Data Lake Analytics 门户页](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. 验证联系信息并创建支持请求。

Microsoft 会审核你的请求，并尽快满足你的业务需求。

## <a name="next-steps"></a>后续步骤

* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
