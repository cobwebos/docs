---
title: 如何排除 Azure 数据湖分析 U-SQL 运行时故障
description: 了解如何排除 U-SQL 运行时故障。
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648449"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>了解如何排除由于运行时更改而导致的 U-SQL 运行时故障

Azure 数据湖 U-SQL 运行时（包括编译器、优化器和作业管理器）是处理 U-SQL 代码的内容。

## <a name="choosing-your-u-sql-runtime-version"></a>选择 U-SQL 运行时版本

当您从 Visual Studio、ADL SDK 或 Azure 数据湖分析门户提交 U-SQL 作业时，作业将使用当前可用的默认运行时。 新版本的 U-SQL 运行时定期发布，包括次要更新和安全修补程序。

您还可以选择自定义运行时版本;也可以选择自定义运行时版本。或者因为您想要尝试新的更新，需要保留旧版本的运行时，或者为报告的问题提供了修补程序，而您无法等待常规的新更新。

> [!CAUTION]
> 选择与默认值不同的运行时可能会中断 U-SQL 作业。 仅使用这些其他版本进行测试。

在极少数情况下，Microsoft 支持可能会将不同版本的运行时固定为您的帐户的默认值。 请确保尽快恢复此引脚。 如果仍固定到该版本，它将在稍后某个日期过期。

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>监视作业 U-SQL 运行时版本

您可以通过 Visual Studio 的作业浏览器或 Azure 门户的作业历史记录查看过去作业在帐户作业历史记录中使用的运行时版本的历史记录。

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 选择 **"查看所有作业**"。 将显示帐户中所有活动作业和最近完成作业的列表。
3. 根据需要单击“筛选器”****，帮助按“时间范围”****、“作业名称”**** 和“作者”**** 值查找作业。
4. 您可以看到已完成作业中使用的运行时。

![显示过去作业的运行时版本](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

可用的运行时版本会随时间而变化。 默认运行时始终称为"默认"，我们至少保留以前的运行时可用一段时间，以及使特殊运行时可用的原因有多种。 显式命名的运行时通常遵循以下格式（斜体用于可变零件，[] 表示可选部件）：

release_YYYYMMDD_adl_buildno[_modifier]

例如，release_20190318_adl_3394512_2 是指 2019 年 3 月 18 日运行时版本的生成 3394512 的第二个版本，release_20190318_adl_3394512_private是指同一版本的私有生成。 注意：日期与该版本上次签入时相关，不一定是正式发布日期。

以下是当前可用的运行时版本。

- release_20190318_adl_3394512
- release_20190318_adl_5832669当前默认值
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>解决 U-SQL 运行时版本问题

您可能会遇到两个可能的运行时版本问题：

1. 脚本或某些用户代码正在将行为从一个版本更改为下一个版本。 此类重大更改通常随着发行说明的发布而提前传达。 如果您遇到此类重大更改，请联系 Microsoft 支持部门报告此突发行为（如果尚未记录），并针对较旧的运行时版本提交作业。

2. 在非默认运行时已显式或隐式使用时，该运行时已固定到您的帐户，并且该运行时在一段时间后已被删除。 如果遇到缺少运行时，请升级脚本以使用当前默认运行时运行。 如果您需要更多时间，请联系 Microsoft 支持

## <a name="see-also"></a>请参阅

- [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
- [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 门户监视 Azure 数据湖分析中的作业](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
