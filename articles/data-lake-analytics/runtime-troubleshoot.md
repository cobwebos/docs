---
title: 如何排查 Azure Data Lake Analytics 的 U SQL 运行时故障
description: 了解如何对 SQL 运行时故障进行故障排除。
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648449"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>了解如何排查由运行时更改引起的 SQL 运行时故障

Azure Data Lake U-SQL 运行时（包括编译器、优化器和作业管理器）用于处理你的 U SQL 代码。

## <a name="choosing-your-u-sql-runtime-version"></a>选择 U-SQL 运行时版本

当你从 Visual Studio、ADL SDK 或 Azure Data Lake Analytics 门户提交 U-SQL 作业时，作业将使用当前可用的默认运行时。 新版本的 U SQL 运行时将定期发布，同时包含次要更新和安全修补程序。

你还可以选择自定义运行时版本;由于你想要尝试新的更新，因此需要停留在较早版本的运行时，或者为报告的问题提供了一个修补程序，你无法等待定期更新。

> [!CAUTION]
> 选择不同于默认值的运行时可能会中断您的 U SQL 作业。 仅将这些其他版本用于测试。

在极少数情况下，Microsoft 支持部门可能会将不同版本的运行时固定为你的帐户的默认版本。 请确保尽快还原此 pin。 如果仍固定到该版本，则会在以后的某个日期过期。

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>监视作业 U-SQL 运行时版本

你可以通过 Visual Studio 的作业浏览器或 Azure 门户的作业历史记录，查看你过去的作业在你的帐户的作业历史记录中使用的运行时版本的历史记录。

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 选择 "**查看所有作业**"。 此时将显示帐户中所有活动和最近完成的作业的列表。
3. 根据需要单击“筛选器”，帮助按“时间范围”、“作业名称”和“作者”值查找作业。
4. 您可以看到在已完成的作业中使用的运行时。

![显示过去作业的运行时版本](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

可用的运行时版本随时间而改变。 默认运行时始终称为 "默认值"，我们至少保留上一次运行时的可用时间，并使特殊运行时可用于多种原因。 显式命名的运行时通常遵循以下格式（斜体用于可变部分，[] 表示可选部分）：

release_YYYYMMDD_adl_buildno[_modifier]

例如，release_20190318_adl_3394512_2 表示3394512年 3 18 2019 月版的运行时版本的第二个版本和 release_20190318_adl_3394512_private 是指同一版本的专用生成。 注意：该日期与上次签入的时间有关，而不一定是正式发布日期。

下面是当前可用的运行时版本。

- release_20190318_adl_3394512
- release_20190318_adl_5832669 当前默认值
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>排查 U-SQL 运行时版本问题

可能会遇到两个可能的运行时版本问题：

1. 脚本或某些用户代码将行为从一个版本更改为下一个版本。 此类重大更改通常会在发布发行说明之前提前传达。 如果你遇到此类重大更改，请联系 Microsoft 支持部门来报告此重大行为（如果尚未记录此行为），并针对较旧的运行时版本提交作业。

2. 如果已将非默认运行时固定到你的帐户，但在一段时间后删除了该运行时，则表示你已显式或隐式使用了非默认运行时。 如果遇到缺少的运行时，请升级您的脚本以与当前默认运行时一起运行。 如果需要更多时间，请联系 Microsoft 支持部门

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
- [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 门户监视 Azure Data Lake Analytics 中的作业](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
