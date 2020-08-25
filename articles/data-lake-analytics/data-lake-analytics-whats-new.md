---
title: Data Lake Analytics 的最新更改
description: 本文提供了对 Data Lake Analytics 进行的最新更改的持续更新的列表。
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: 6f4885594e4f67d91c595d8afdddc15e8ed4ae75
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263174"
---
# <a name="whats-new-in-data-lake-analytics"></a>Data Lake Analytics 中的新增功能

Azure Data Lake Analytics 会针对特定组件定期更新。 为了让大家随时了解最新更新，本文将提供以下方面的信息：

- 关键组件 beta 版本预览的通知
- 重要组件版本信息，例如：组件可用版本的列表、当前默认版本等。


## <a name="notification-of-key-component-beta-preview"></a>关键组件 beta 版本预览的通知

没有可供预览的关键组件 beta 版本。 

## <a name="u-sql-runtime"></a>U-SQL 运行时

Azure Data Lake U-SQL 运行时（包括编译器、优化器和作业管理器）用于处理 U SQL 代码。

从任何工具提交 Azure Data Lake 分析作业时，作业会使用生产环境中当前可用的默认运行时。 

运行时版本会定期更新。 以前的运行时会保留一段时间以供使用。 当新的 Beta 版本可供预览时，它也会在其中进行提供。

> [!CAUTION]
> - 选择与默认运行时不同的运行时可能会中断 U-SQL 作业。 强烈建议不要将这些非默认版本用于生产，而仅将其用于测试。
> - 非默认运行时版本有固定的生命周期。 它会自动过期。

以下版本是当前的默认运行时版本。

- release-20200124live_adl_16283022_2

若要了解如何排查 SQL 运行时故障，请参阅[排查 U-SQL 运行时故障](runtime-troubleshoot.md)。

## <a name="net-framework"></a>.NET Framework

Azure Data Lake Analytics 现在使用 .NET Framework v4.7.2。 

如果 Azure Data Lake Analytics U-SQL 脚本代码使用自定义程序集，而这些自定义程序集使用 .NET 库，请验证代码以检查是否存在任何中断。

使用[排查 .NET 升级问题](runtime-troubleshoot.md)了解如何排查 .NET 升级问题。

## <a name="release-note"></a>发行说明

有关最新更新详细信息，请参阅 [Azure Data Lake Analytics 发行说明](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes)。


## <a name="next-steps"></a>后续步骤

* 通过 [Azure 门户](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md) 使用 Data Lake Analytics 入门

