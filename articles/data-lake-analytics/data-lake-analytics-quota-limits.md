---
title: 调整 Azure Data Lake Analytics 中的配额和限制
description: 了解如何调整和增加 Azure Data Lake Analytics (ADLA) 帐户中的配额和限制。
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
editor: jasonwhowell
manager: kfile
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: e493c45ef49d370bbf7d007e039b4102aaa3cdf0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623972"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>调整 Azure Data Lake Analytics 中的配额和限制

了解如何调整和增加 Azure Data Lake Analytics (ADLA) 帐户中的配额和限制。 了解这些限制有助于理解 U-SQL 作业行为。 所有配额限制都是软限制，因此随时可通过联系 Azure 支持增加最大限制。

## <a name="azure-subscriptions-limits"></a>Azure 订阅限制

每个区域每个订阅的最大 ADLA 帐户数：5

如果尝试创建第 6 个 ADLA 帐户，将收到错误“已达到订阅名称区域中允许的 Data Lake Analytics 帐户的最大数量 (5)”。 

如果你想超越此限制，可以尝试以下选项：
* 选择其他区域（如果适用）
* 通过[创建支持票证](#increase-maximum-quota-limits)，联系 Azure 客户支持人员，请求增加配额。

## <a name="default-adla-account-limits"></a>默认 ADLA 帐户限制

**每个帐户的最大分析单位数 (AU)：** 32

这是可在帐户中同时运行的最大 AU 数。 如果所有作业上正在运行的 AU 总数超出此限制，较新的作业将自动排队。 例如：

* 如果只有一个作业正在以 32 AU 运行，当提交第二个作业时，该作业会在作业队列中等待，直到第一个作业完成。
* 如果已有四个作业正在运行，每个作业使用 8 AU，提交需要 8 AU 的第五个作业时，该作业会在作业队列中等待，直到有 8 AU 可用。

**每个作业的最大分析单位数 (AU)：** 32

这是每个单独的作业可以在帐户中分配到的默认最大 AU 数。 除非提交者受到计算策略（作业提交限制）的影响，这会给每个作业提供更多 AU，否则分配超过此限制的作业将被拒绝。 此值的上限是帐户的 AU 限制。

**每个帐户的最大并发 U-SQL 作业数：** 20

这是可在帐户中同时运行的最大作业数。 大于此值时，较新的作业将自动排队。

## <a name="adjust-adla-account-limits"></a>调整 ADLA 帐户限制

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择一个现有的 ADLA 帐户。
3. 单击“属性”。
4. 调整“最大 AU 数”，“最大运行作业数”和“作业提交限制”的值以满足你的需求。

## <a name="increase-maximum-quota-limits"></a>增加最大配额限制

可以在 [Azure 服务特定的限制文档](../azure-subscription-service-limits.md#data-lake-analytics-limits)中找到有关 Azure 限制的更多信息。

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
