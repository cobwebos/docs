---
title: 在 SSIS 集成运行时中对包执行进行故障排除 |Microsoft Docs
description: 本文提供了有关 ssis 集成运行时中 SSIS 包执行的疑难解答指南
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
ms.openlocfilehash: a7ad0f3be754029c654b04d19750aab7bbcd210d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933635"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>在 SSIS 集成运行时中对包执行进行故障排除

本文包含在 SSIS 集成运行时中执行 SQL Server Integration Services (SSIS) 包时可能会发现的最常见错误。 它描述了可能的原因和解决错误的操作。

## <a name="where-to-find-logs-for-troubleshooting"></a>在何处查找用于疑难解答的日志

使用 Azure 数据工厂门户检查 SSIS 包执行活动的输出。 输出包括执行结果、错误消息和操作 ID。 有关详细信息, 请参阅[监视管道](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)。

使用 SSIS 目录 (SSISDB) 检查执行的详细日志。 有关详细信息, 请参阅[监视正在运行的包和其他操作](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)。

## <a name="common-errors-causes-and-solutions"></a>常见错误、原因和解决方案

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>错误消息:"连接超时已过期" 或 "服务在处理你的请求时遇到错误。 请重试。 "

下面是可能的原因和建议的操作:
* 数据源或目标被重载。 检查数据源或目标上的负载, 查看其是否有足够的容量。 例如, 如果使用的是 Azure SQL 数据库, 请考虑在数据库可能超时的情况下进行扩展。
* SSIS 集成运行时和数据源或目标之间的网络是不稳定的, 尤其是在连接是跨区域或在本地与 Azure 之间。 通过执行以下步骤, 应用 SSIS 包中的重试模式:
  * 请确保 SSIS 包可以在出现故障时重新运行, 而不会产生负面影响 (例如, 数据丢失或重复数据)。
  * 配置 "**常规**" 选项卡上的 "**执行 SSIS 包**" 活动的**重试**和**重试间隔**。![在“常规”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * 对于 ADO.NET 和 OLE DB 源组件或目标组件, 请在 "SSIS 包" 或 "SSIS" 活动的 "连接管理器" 中设置**ConnectRetryCount**和**ConnectRetryInterval** 。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>错误消息:"ADO NET 源未能获取连接 ' ... '" 在建立与 SQL Server 的连接时出现 "与网络相关的或特定于实例的错误"。 找不到或无法访问服务器。 "

此问题通常意味着无法从 SSIS 集成运行时访问数据源或目标。 原因可能各不相同。 请尝试以下操作：
* 请确保正确传递数据源或目标名称/ip。
* 请确保已正确设置防火墙。
* 如果数据源或目标是本地的, 请确保正确配置虚拟网络:
  * 可以通过在同一虚拟网络中预配 Azure VM 来验证此问题是否来自虚拟网络配置。 然后检查是否可以从 Azure VM 访问数据源或目标。
  * 在[将 AZURE ssis 集成运行时加入到虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)中的 ssis 集成运行时中, 可以找到有关使用虚拟网络的更多详细信息。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>错误消息:"ADO NET 源未能获取连接 ' ... '" 使用 "无法创建托管连接管理器"。

可能的原因是, 包中使用的 ADO.NET 提供程序未安装在 SSIS 集成运行时中。 您可以使用自定义安装程序来安装该提供程序。 有关自定义安装的详细信息, 请查看[AZURE SSIS 集成运行时的自定义设置](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="error-message-the-connection--is-not-found"></a>错误消息:"连接 ..."找不到 "

旧版 SQL Server Management Studio (SSMS) 中的某个已知问题可能导致此错误。 如果此包包含的自定义组件（例如 SSIS Azure Feature Pack 或合作伙伴组件）未安装在使用 SSMS 来执行部署的计算机上，则 SSMS 会删除该组件，导致此错误。 将 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 升级到已解决该问题的最新版本。

### <a name="error-messagessis-executor-exit-code--1073741819"></a>错误消息: "SSIS 执行程序退出代码:-1073741819"。

* 可能的原因和建议的操作：
  * 出现此错误的原因可能是, 当多线程中并行执行多个 Excel 源或目标时, Excel 源和目标的限制。 可以通过以下方式解决此限制: 将 Excel 组件更改为按顺序执行, 或将其分隔到不同的包中, 并通过 "执行包任务", 并将 ExecuteOutOfProcess 属性设置为 True 来触发此限制。

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>错误消息:“磁盘上没有足够的空间”

此错误表示本地磁盘已在 SSIS 集成运行时节点中使用。 检查你的包或自定义安装程序是否正在消耗大量磁盘空间:
* 如果包占用了磁盘，包执行完成后会释放磁盘空间。
* 如果自定义安装程序使用该磁盘, 则需要停止 SSIS 集成运行时, 修改脚本, 然后重新启动集成运行时。 你为自定义安装指定的整个 Azure blob 容器将被复制到 SSIS 集成运行时节点中, 因此请检查该容器下是否存在任何不必要的内容。

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>错误消息:"无法从主服务器检索资源。 Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException:代码: 300004。 说明: 加载文件 "* * *" 失败。 "

* 可能的原因和建议的操作：
  * 如果 SSIS 活动正在从文件系统 (包文件或项目文件) 执行包, 则会出现此错误, 这是因为在 SSIS 活动中提供的包访问凭据无法访问项目、包或配置文件
    * 如果使用的是 Azure 文件:
      * 文件路径\\应以\\ \<存储帐户名称\>开头。\\file.core.windows.net\<文件共享路径\>
      * 域应为 "Azure"
      * 用户名应为\<存储帐户名称\>
      * 密码应为\<存储访问密钥\>
    * 如果你使用的是本地文件, 请检查是否正确配置了 VNet、包访问凭据和权限, 以便你的 Azure SSIS 集成运行时可以访问本地文件共享

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>错误消息:"文件名" ... "在连接中指定的无效 "

* 可能的原因和建议的操作：
  * 指定的文件名无效
  * 请确保在连接管理器中使用 FQDN (完全限定的域名) 而不是短时间

### <a name="error-message-cannot-open-file-"></a>错误消息:“无法打开文件 '...'”

当包执行在 SSIS 集成运行时的本地磁盘中找不到文件时会发生此错误。 请尝试以下操作：
* 不要使用在 SSIS 集成运行时中执行的包中的绝对路径。 使用当前执行工作目录 (.) 或 temp 文件夹 (% TEMP%)是.
* 如果需要在 SSIS 集成运行时节点上保存一些文件, 请按照[自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)中所述准备文件。 执行完成后, 工作目录中的所有文件都将被清除。
* 使用 Azure 文件, 而不是在 SSIS 集成运行时节点中存储文件。 有关详细信息, 请参阅[使用 Azure 文件共享](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)。

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>错误消息:“数据库 'SSISDB' 已达到大小配额”

可能的原因是，在 Azure SQL 数据库中创建的 SSISDB 数据库或者在创建 SSIS 集成运行时时的托管实例已达到其配额。 请尝试以下操作：
* 考虑增加数据库的 DTU。 可以在 [Azure SQL 数据库服务器的 SQL 数据库资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)中找到详细信息。
* 检查包是否会生成许多日志。 如果是，则可配置一项弹性作业来清理这些日志。 有关详细信息，请参阅[使用 Azure 弹性数据库作业清理 SSISDB 日志](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)。

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>错误消息:“数据库的请求限制是 ...，现已达到该限制。”

如果多个包在 SSIS 集成运行时中并行运行, 则可能会发生此错误, 因为 SSISDB 达到了其请求限制。 请考虑增加 SSISDB 的 DTC, 以解决此问题。 可以在 [Azure SQL 数据库服务器的 SQL 数据库资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)中找到详细信息。

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>错误消息:“SSIS 操作失败并出现意外的操作状态: ...”

此错误通常是由暂时性问题引起的, 因此请尝试重新运行包执行。 通过执行以下步骤, 应用 SSIS 包中的重试模式:

* 请确保 SSIS 包可以在出现故障时重新运行, 而不会产生负面影响 (例如, 数据丢失或重复数据)。
* 配置 "**常规**" 选项卡上的 "**执行 SSIS 包**" 活动的**重试**和**重试间隔**。![在“常规”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* 对于 ADO.NET 和 OLE DB 源组件或目标组件, 请在 "SSIS 包" 或 "SSIS" 活动的 "连接管理器" 中设置**ConnectRetryCount**和**ConnectRetryInterval** 。

### <a name="error-message-there-is-no-active-worker"></a>错误消息:“没有任何活动的辅助角色。”

此错误通常表示 SSIS 集成运行时的状态不正常。 检查 "状态" 和 "详细" 错误的 Azure 门户。 有关详细信息, 请参阅[AZURE SSIS 集成运行时](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)。

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>错误消息:“集成运行时无法升级，最终将会停止工作，因为我们无法访问你为自定义安装提供的 Azure Blob 容器。”

如果 SSIS 集成运行时无法访问为自定义安装配置的存储, 则会出现此错误。 检查提供的共享访问签名 (SAS) URI 是否有效且未过期。

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>错误消息:“Microsoft OLE DB Provider for Analysis Services。 ‘Hresult:0x80004005 说明:’COM 错误:COM 错误: mscorlib；某个调用的目标引发了异常”

一个可能的原因是为启用了 Azure 多重身份验证的用户名或密码配置了 Azure Analysis Services 身份验证。 SSIS 集成运行时不支持此身份验证。 尝试使用服务主体进行 Azure Analysis Services 身份验证:
1. [使用服务主体在自动化](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)中描述的方式准备服务主体。
2. 在 "连接管理器" 中, 配置**使用特定的用户名和密码**: 将**AppID**设置为用户名, 将**clientSecret**设置为密码。

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>错误消息:"ADONET 源无法获取连接 {GUID}, 出现以下错误消息:使用托管标识时, 用户 "NT AUTHORITY\ANONYMOUS LOGON" 登录失败

如果参数*ConnectUsingManagedIdentity*为**True**, 请确保不将连接管理器的身份验证方法配置为**Active Directory 密码身份验证**。 可以改为将其配置为**SQL 身份验证**, 如果设置了*ConnectUsingManagedIdentity* , 则会忽略此设置。

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>意外触发多个包执行

* 可能的原因和建议的操作：
  * ADF 存储过程活动用于触发 SSIS 包的执行。 T-sql 命令可能会遇到暂时性问题, 并触发重新运行, 这将导致多个包执行。
  * 使用 ExecuteSSISPackage 活动, 以确保不会重新运行包执行, 除非用户在活动中设置了重试计数。 详细信息可在[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

### <a name="package-execution-takes-too-long"></a>包执行时间太长

下面是可能的原因和建议的操作:

* 在 SSIS 集成运行时上计划了太多的包执行。 所有这些执行都将在队列中等待。
  * 使用以下公式确定最大值:

    每个 IR 的最大并行执行计数 = 节点计数 * 每个节点的最大并行执行数
  * 若要了解如何设置节点计数和每个节点的最大并行执行, 请参阅[在 Azure 数据工厂中创建 AZURE SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。
* SSIS 集成运行时已停止或状态不正常。 若要了解如何检查 SSIS 集成运行时的状态和错误, 请参阅[AZURE ssis 集成运行时](monitor-integration-runtime.md#azure-ssis-integration-runtime)。

我们还建议你在 "**常规**" 选项卡上设置超时:![在 "常规" 选项卡](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)上设置属性。

### <a name="poor-performance-in-package-execution"></a>包的执行性能不佳

请尝试以下操作：

* 请确保 SSIS 集成运行时与数据源和目标位于同一区域。

* 将包执行的日志记录级别设置为 "**性能**", 收集执行中每个组件的持续时间信息。 有关详细信息, 请参阅[Integration Services (SSIS) 日志记录](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)。

* 在 Azure 门户中检查 IR 节点性能:
  * 有关如何监视 SSIS 集成运行时的信息, 请参阅[AZURE ssis 集成运行时](monitor-integration-runtime.md#azure-ssis-integration-runtime)。
  * 可以通过在 Azure 门户中查看数据工厂的指标, 查找 SSIS 集成运行时的 CPU/内存历史记录。
    ![监视 SSIS 集成运行时的指标](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
