---
title: SSIS 集成运行时中的包执行故障排除 |Microsoft Docs
description: 本文提供有关 SSIS 集成运行时中的 SSIS 包执行故障排除指南
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: f17c364d258ef356a98180c9903603d92a6a9245
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078516"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>SSIS 集成运行时中的包执行故障排除

本文包含 SSIS 集成运行时、 潜在原因和操作来解决这些错误中执行 SSIS 包时可能会遇到的最常见错误。

## <a name="where-can-i-find-logs-for-troubleshoot"></a>在哪里可以找到故障排除的日志

* ADF 门户可用于检查输出中的 SSIS 包执行活动包括执行结果、 错误消息和操作 id。 详细信息，请参阅[监视管道](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* SSIS 目录 (SSISDB) 可以用于检查执行的详细信息日志。 详细信息，请参阅[监视器正在运行的包和其他操作](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>常见错误、 原因和解决方案

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>错误消息：`"Connection Timeout Expired."`或 `"The service has encountered an error processing your request. Please try again."`

* 可能的原因和建议的操作：
  * 重载的数据源/目标。 检查在数据源/目标的负载，并查看其是否具有足够的容量。 例如，如果使用 Azure SQL，则建议要考虑向上缩放，如果数据库可能超时。
  * 尤其是当连接是跨区域之间或在本地与 azure SSIS 集成运行时和数据源/目标之间的网络不稳定，。 建议应用重试模式在 SSIS 包中的执行以下步骤：
    * 请确保将 SSIS 包可以 （例如在失败时不产生负面影响的情况下重新运行。 丢失数据，数据 dup....）
    * 配置**重试**并**重试间隔**执行 SSIS 包中的活动的常规选项卡![在常规选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * 可以在 SSIS 包中的连接管理器或 SSIS 活动中有关 ADO.NET 和 OLEDB 源/目标组件，设置 ConnectRetryCount 和 ConnectRetryInterval

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>出现错误消息： `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* 可能的原因和建议的操作：
  * 此问题通常意味着数据源/目标不可从 SSIS 集成运行时，它可能由不同的原因：
    * 请确保您正确传递数据源/目标名称 /IP
    * 请确保防火墙已正确设置
    * 请确保在本地数据源/目标是否为 vNet 配置正确。
      * 你可以验证问题是否从 vNet 配置通过预配 Azure VM 在同一 vNet 中。 然后检查是否可以从 Azure VM 访问数据源/目标
      * 您可以找到更多详细信息与 SSIS 集成运行时在使用 vNet [Azure SSIS 集成运行时加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>错误消息:"`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* 可能的原因和建议的操作：
  * 在包中使用的 ADO.NET 提供程序未安装 SSIS 集成运行时中。 可以使用自定义安装来安装该提供程序。 有关自定义安装程序的更多详细信息可在[自定义 Azure SSIS 集成运行时安装](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>错误消息:"`The connection '...' is not found`"

* 可能的原因和建议的操作：
  * 此错误可能是因为旧版本 SSMS 中的已知的问题。 如果包中包含自定义组件 （例如，SSIS 的 Azure 功能包或第三方组件），使用 SSMS 执行部署的计算机上未安装，该组件将不再通过 SSMS 和导致错误。 升级[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)到包含该问题已修复的最新版本。

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>错误消息："没有足够空间的磁盘上"

* 可能的原因和建议的操作：
  * 此错误意味着在 SSIS Integration Runtime 节点的本地磁盘用完。 检查你的包或自定义安装程序会占用很多磁盘空间。
    * 如果磁盘由您的包，它将包执行完成后释放它们。
    * 如果磁盘使用自定义安装程序，你将需要停止 SSIS 集成运行时，修改您的脚本，然后重新启动 SSIS 集成运行时。 指定自定义安装程序的整个 Azure Blob 容器将通过复制到 SSIS IR 节点，因此请验证是否存在该容器下的任何不必要的内容。

### <a name="error-message-cannot-open-file-"></a>错误消息："无法打开文件 '...'"

* 可能的原因和建议的操作：
  * 当包执行找不到 SSIS 集成运行时中的本地磁盘中的文件时，将发生此错误。
    * 不建议在 SSIS 集成运行时中执行的包中使用绝对路径。 使用当前执行的工作目录 （.） 或临时文件夹 （%TEMP%)改为。
    * 如果需要保留某些 SSIS 集成运行时节点上的文件，它会建议，以准备通过文件[自定义安装程序](how-to-configure-azure-ssis-ir-custom-setup.md)。 执行完成后，将清除执行的工作目录中的所有文件。
    * 另一种方法是使用 Azure 文件而不是将文件存储在 SSIS Integration Runtime 节点。 更多详细信息，请参阅[ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)。

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>错误消息："数据库 SSISDB 已达到其大小配额"

* 可能的原因和建议的操作：
  * 创建 SSIS 集成运行时已达到其配额时，Azure SQL 或托管实例中创建 SSISDB。
    * 请考虑增加你要解决此问题的数据库的 DTU。 详细信息，请参阅 [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * 检查包是否会生成许多日志。 如果是这样，可以配置弹性作业来清理这些日志。 请参阅[清理 Azure 弹性数据库作业使用的 SSISDB 日志](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)的详细信息。

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>错误消息："请求限制为数据库是...并达到了。"

* 可能的原因和建议的操作：
  * 如果在 SSIS 集成运行时中并行执行多个包，可能会发生此错误的原因命中 SSISDB 的请求限制。 请考虑增加要解决此问题在 SSISDB 的 DTC。 详细信息，请参阅 [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>错误消息："SSIS 操作失败，意外的操作状态:..."

* 可能的原因和建议的操作：
  * 主要是由暂时性错误导致错误，因此请尝试重新运行包执行。 建议应用重试模式在 SSIS 包中的执行以下步骤：
    * 请确保将 SSIS 包可以重新运行失败时却无副作用 （例如，丢失数据，数据 dup....）
    * 配置**重试**并**重试间隔**执行 SSIS 包中的活动的常规选项卡![在常规选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * 可以在 SSIS 包中的连接管理器或 SSIS 活动中有关 ADO.NET 和 OLEDB 源/目标组件，设置 ConnectRetryCount 和 ConnectRetryInterval

### <a name="error-message-there-is-no-active-worker"></a>错误消息："没有任何活动的工作。"

* 可能的原因和建议的操作：
  * 此错误通常意味着 SSIS 集成运行时处于不正常状态。 检查 Azure 门户中的状态和详细信息的错误为： [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>错误消息："集成运行时不能升级，并且最终将停止工作，因为我们无法访问自定义安装程序提供的 Azure Blob 容器。"

* SSIS 集成运行时无法访问自定义安装程序配置的存储时，将发生此错误。 检查你提供的 SAS Uri 有效并且尚未过期。

### <a name="package-takes-unexpected-long-time-to-execute"></a>包需要意外的长时间才能执行

* 可能的原因和建议的操作：
  * 过多的包执行已计划在 SSIS 集成运行时。 在这种情况下，将为其启用可执行队列中等待所有这些执行。
    * 每个红外线 （ir） 的最大并行执行计数 = 节点计数 * 每个节点的最大并行执行
    * 请参阅[Azure 数据工厂中创建 Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)如何设置的节点数和每个节点的最大并行执行。
  * SSIS 集成运行时已停止或处于不正常状态。 检查[AZURE-SSIS 集成运行时](monitor-integration-runtime.md#azure-ssis-integration-runtime)如何检查 SSIS 集成运行时状态和错误。
  * 建议当您确定应该在特定的时间内完成包执行将超时设置：![在常规选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>在包执行过程中的性能不佳

* 可能的原因和建议的操作：

  * 检查 SSIS 集成运行时是否在与数据源和目标位于同一区域中。

  * 启用"性能"日志记录级别

      可以设置为"性能"，以收集执行过程中每个组件的更多详细持续时间信息的包执行的日志记录级别。 可以在找到详细信息： [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * 检查在 Azure 门户中 IR 监视页中的红外线 （ir） 节点性能。
    * 如何监视 SSIS 集成运行时：[Azure SSIS 集成运行时](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * SSIS 集成运行时的 CPU/内存使用情况的历史记录位于 Azure 门户中的数据工厂指标![监视 SSIS 集成运行时的指标](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
