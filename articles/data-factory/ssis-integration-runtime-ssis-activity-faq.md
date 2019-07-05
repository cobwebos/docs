---
title: 对 SSIS 集成运行时中的包执行进行故障排除 |Microsoft Docs
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
ms.openlocfilehash: 68a5d5278e1181695695647cff187d4b95624b40
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537644"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>对 SSIS 集成运行时中的包执行进行故障排除

本文包含 SSIS 集成运行时中执行 SQL Server Integration Services (SSIS) 包时可能会发现的最常见错误。 它描述了可能的原因和操作，来解决这些错误。

## <a name="where-to-find-logs-for-troubleshooting"></a>在哪里可以找到日志进行故障排除

使用 Azure 数据工厂门户检查 SSIS 包执行活动的输出。 输出包括执行结果、 错误消息和操作 id。 有关详细信息，请参阅[监视管道](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)。

使用 SSIS 目录 (SSISDB) 来检查执行的详细信息日志。 有关详细信息，请参阅[监视器正在运行的包和其他操作](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)。

## <a name="common-errors-causes-and-solutions"></a>常见错误、 原因和解决方案

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>错误消息："连接超时时间已到"或者"服务遇到处理你的请求时出错。 请重试。"

下面是可能的原因和建议的操作：
* 重载的数据源或目标。 检查您的数据源或目标上的负载，并查看其是否具有足够的容量。 例如，如果你使用 Azure SQL 数据库，请考虑扩展数据库很可能会超时。
* 尤其是当连接是跨区域之间或在本地与 Azure SSIS 集成运行时的数据源或目标之间的网络不稳定，。 将 SSIS 包中的重试模式应用通过执行以下步骤：
  * 请确保将 SSIS 包可以重新运行失败时不会产生副作用 （例如，数据丢失或重复数据）。
  * 配置**重试**并**重试间隔**的**执行 SSIS 包**上的活动**常规**选项卡。![在常规选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * 有关 ADO.NET 和 OLE DB 源或目标组件，设置**ConnectRetryCount**并**ConnectRetryInterval**在连接管理器中的 SSIS 包或 SSIS 活动中。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>错误消息："ADO NET 源未能获取连接 '...'" 使用"建立与 SQL Server 的连接时发生与网络相关或特定于实例的错误。 服务器找不到或无法访问。"

此问题通常意味着数据源或目标从 SSIS 集成运行时无法访问。 原因可以各不相同。 请尝试以下操作：
* 请确保您传递的数据源或目标的名称 /IP 正确。
* 请确保防火墙已正确设置。
* 请确保如果您的数据源或目标是在本地正确配置虚拟网络：
  * 你可以验证问题是否从虚拟网络配置通过预配在同一虚拟网络中的 Azure VM。 然后，检查是否可以从 Azure VM 访问的数据源或目标。
  * 您可以找到有关使用与 SSIS 集成运行时中的虚拟网络的更多详细信息[将 Azure SSIS 集成运行时加入到虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>错误消息："ADO NET 源未能获取连接 '...'" 使用"无法创建托管的连接管理器。"

可能的原因是包中使用的 ADO.NET 提供程序未安装 SSIS 集成运行时中。 可以使用自定义安装来安装该提供程序。 您可以找到有关自定义安装中的更多详细信息[自定义 Azure SSIS 集成运行时安装](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="error-message-the-connection--is-not-found"></a>错误消息："连接...未找到"

较旧版本的 SQL Server Management Studio (SSMS) 中的已知的问题会导致此错误。 如果包中包含未在其中使用 SSMS 执行部署的计算机安装的自定义组件 （例如，SSIS 的 Azure 功能包或合作伙伴组件），SSMS 将删除该组件，并会导致错误。 升级[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)到包含该问题已修复的最新版本。

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>错误消息："没有足够空间的磁盘上"

此错误意味着在 SSIS integration runtime 节点的本地磁盘用完。 检查你的包或自定义安装程序是否正在消耗大量磁盘空间：
* 如果磁盘由您的包，它将包执行完成后释放它们。
* 如果磁盘由自定义安装，你将需要停止 SSIS 集成运行时，修改您的脚本，并再次启动集成运行时。 整个 Azure blob 容器指定自定义安装程序将复制到 SSIS integration runtime 节点，因此检查是否存在该容器下的任何不必要的内容。

### <a name="error-message-cannot-open-file-"></a>错误消息："无法打开文件 '...'"

当包执行找不到文件中的本地磁盘中 SSIS 集成运行时，将发生此错误。 请尝试以下操作：
* 不要在 SSIS 集成运行时中正在执行的包中使用的绝对路径。 使用当前的执行工作目录 （.） 或临时文件夹 （%TEMP%)改为。
* 如果需要保存 SSIS 集成运行时节点上的一些文件，准备文件中所述[自定义安装程序](how-to-configure-azure-ssis-ir-custom-setup.md)。 执行完成后，将清理工作目录中的所有文件。
* 使用 Azure 文件而不是将文件存储在 SSIS integration runtime 节点。 有关详细信息，请参阅[使用 Azure 文件共享](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)。

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>错误消息："数据库 SSISDB 已达到其大小配额"

可能的原因是当你要创建的 SSIS 集成运行时，在 Azure SQL 数据库中或托管的实例中创建 SSISDB 数据库已达到其配额。 请尝试以下操作：
* 请考虑增加数据库的 DTU。 您可以找到详细信息中的[为 Azure SQL 数据库服务器的 SQL 数据库资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)。
* 检查包是否会生成许多日志。 如果是这样，您可以配置弹性作业来清理这些日志。 有关详细信息，请参阅[清理 Azure 弹性数据库作业使用的 SSISDB 日志](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)。

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>错误消息："请求限制为数据库是...并达到了。"

如果在 SSIS 集成运行时中并行运行多个包，因为 SSISDB 已达到其请求限制可能会发生此错误。 请考虑增加 DTC 的 SSISDB 若要解决此问题。 您可以找到详细信息中的[为 Azure SQL 数据库服务器的 SQL 数据库资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)。

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>错误消息："SSIS 操作失败，意外的操作状态:..."

主要是由暂时性问题导致错误，因此请尝试重新运行包执行。 将 SSIS 包中的重试模式应用通过执行以下步骤：

* 请确保将 SSIS 包可以重新运行失败时不会产生副作用 （例如，数据丢失或重复数据）。
* 配置**重试**并**重试间隔**的**执行 SSIS 包**上的活动**常规**选项卡。![在常规选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* 有关 ADO.NET 和 OLE DB 源或目标组件，设置**ConnectRetryCount**并**ConnectRetryInterval**在连接管理器中的 SSIS 包或 SSIS 活动中。

### <a name="error-message-there-is-no-active-worker"></a>错误消息："没有任何活动的工作。"

此错误通常意味着 SSIS 集成运行时具有不正常状态。 查看 Azure 门户中的状态和详细的错误。 有关详细信息，请参阅[AZURE-SSIS 集成运行时](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)。

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>错误消息："集成运行时不能升级，并且最终将停止工作，因为我们无法访问自定义安装程序提供的 Azure Blob 容器。"

SSIS 集成运行时无法访问自定义安装程序配置的存储时，将发生此错误。 检查共享的访问签名 (SAS) 提供的 URI 是否有效，并且尚未过期。

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>错误消息："Microsoft OLE DB Provider for Analysis Services。 Hresult:0x80004005 说明:COM 错误：COM 错误： mscorlib;已通过调用目标引发异常"

一个可能原因是配置用于 Azure Analysis Services 身份验证的用户名或密码启用 Azure 多重身份验证。 在 SSIS 集成运行时中不支持此身份验证。 请尝试使用服务主体进行 Azure Analysis Services 身份验证：
1. 准备服务主体，如中所述[使用服务主体自动化](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)。
2. 在连接管理器中，配置**使用特定用户名和密码**： 设置**AppID**作为用户名并**clientSecret**作为密码。

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>错误消息："ADONET 源未能获取连接 {GUID} 具有以下错误消息：用户 NT AUTHORITY\ANONYMOUS LOGON 登录失败"时使用托管的标识

请确保未配置身份验证方法的连接管理器中作为**Active Directory 密码身份验证**时参数*ConnectUsingManagedIdentity*是 **，则返回 True**. 您可以将其配置为**SQL 身份验证**相反，其中如果，则忽略*ConnectUsingManagedIdentity*设置。

### <a name="package-execution-takes-too-long"></a>包执行时间太长

下面是可能的原因和建议的操作：
* 过多的包执行已计划在 SSIS 集成运行时。 所有这些执行会在队列中等待其打开。
  * 通过使用以下公式来确定最大值： 
    
    每个红外线 （ir） 的最大并行执行计数 = 节点计数 * 每个节点的最大并行执行
  * 若要了解如何设置的节点数和每个节点的最大并行执行，请参阅[在 Azure 数据工厂中创建的 Azure SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。
* SSIS 集成运行时已停止或处于不正常状态。 若要了解如何检查 SSIS 集成运行时状态和错误，请参阅[AZURE-SSIS 集成运行时](monitor-integration-runtime.md#azure-ssis-integration-runtime)。

我们还建议上设置超时**常规**选项卡：![在常规选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)。

### <a name="poor-performance-in-package-execution"></a>在包执行过程中的性能不佳

请尝试以下操作：

* 确保将 SSIS 集成运行时所在的数据源和目标的同一区域。

* 设置对包执行的日志记录级别**性能**收集执行过程中每个组件的持续时间信息。 有关详细信息，请参阅[Integration Services (SSIS) 日志记录](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)。

* 检查 Azure 门户中的红外线 （ir） 节点性能：
  * 有关如何监视 SSIS 集成运行时信息，请参阅[AZURE-SSIS 集成运行时](monitor-integration-runtime.md#azure-ssis-integration-runtime)。
  * 您可以通过 Azure 门户中查看数据工厂的度量值来找到 SSIS 集成运行时 CPU/内存历史记录。
    ![SSIS 集成运行时的监视指标](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
