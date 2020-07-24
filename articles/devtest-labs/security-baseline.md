---
title: Azure 开发测试实验室的 azure 安全基准
description: Azure 开发测试实验室的 azure 安全基准
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096331"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure 开发测试实验室的 azure 安全基准

Azure 开发测试实验室的 Azure 安全基准包含的建议可帮助你提高部署的安全状况。

此服务的基线取自[Azure 安全基准1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源
**指南：** Microsoft 维护 Azure 资源的时间源。 不过，你可以管理计算资源的时间同步设置。

请参阅以下文章，了解如何配置 Azure 计算资源的时间同步： [azure 中 Windows vm 的时间同步](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)。 

**Azure 安全中心监视：** 当前不可用

**责任：** 微软

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理
**指南：** 启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户以进行存档。 活动日志提供了有关在管理平面级别对 Azure 开发测试实验室实例执行的操作的见解。 使用 Azure 活动日志数据，您可以确定在开发测试实验室实例的管理平面级别完成的任何写入操作（PUT、POST、DELETE）的 "操作内容、操作人员和操作时间"。

有关详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/platform/diagnostic-settings.md)。

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录
**指南：** 启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户以进行存档。 活动日志提供了有关在管理平面级别对 Azure 开发测试实验室实例执行的操作的见解。 使用 Azure 活动日志数据，您可以确定在开发测试实验室实例的管理平面级别完成的任何写入操作（PUT、POST、DELETE）的 "操作内容、操作人员和操作时间"。

有关详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/platform/diagnostic-settings.md)。

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志
**指南：** Azure 开发测试实验室虚拟机由客户创建和拥有。 因此，组织负责监视它。 可以使用 Azure 安全中心监视计算操作系统。 安全中心从操作系统收集的数据包括 OS 类型和版本、OS（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和登录用户。 Log Analytics 代理还会收集故障转储文件。

有关详细信息，请参阅以下文章： 

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](../azure-monitor/learn/quick-collect-azurevm.md)
- [了解 Azure 安全中心数据收集](../security-center/security-center-enable-data-collection.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期
***指南：** 在 Azure Monitor 中，根据组织的符合性规定，设置与 Azure 开发测试实验室实例关联的 Log Analytics 工作区的日志保持期。

有关详细信息，请参阅以下文章：[如何设置日志保持参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志
**指南：** 启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中运行查询以搜索术语，确定趋势，分析模式，并根据可能已为 Azure 开发测试实验室收集的活动日志数据提供许多其他见解。

有关详细信息，请参阅以下文章：

- [如何启用 Azure 活动日志的诊断设置](../azure-monitor/platform/diagnostic-settings.md)
- [如何收集和分析 Azure Monitor 的 Log Analytics 工作区中的 Azure 活动日志](../azure-monitor/platform/activity-log.md)

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：针对异常活动启用警报
**指南：** 使用 Azure Log Analytics 工作区监视和警报与 Azure 开发测试实验室相关的安全日志和事件中的异常活动。

有关详细信息，请参阅以下文章：[如何在 log analytics 日志数据上发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视：** 当前不可用

**责任：** 面向

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录
**指南：** 不适用。 Azure 开发测试实验室不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录
**指南：** 不适用。 Azure 开发测试实验室不会处理或产生与 DNS 相关的日志。

**Azure 安全中心监视：** 不适用

**责任：** 面向

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录
**指南：** Azure 开发测试实验室创建由客户拥有和管理的 Azure 计算计算机。 在所有支持的 Azure Windows 虚拟机上使用 Microsoft Monitoring Agent，以记录进程创建事件和命令行字段。 对于受支持的 Azure Linux 虚拟机，可以手动配置每个节点的控制台日志记录，并使用 Syslog 来存储数据。 同时，使用 Azure Monitor 的 Log Analytics 工作区查看日志，并对 Azure 虚拟机中记录的数据运行查询。

- [Azure 安全中心中的数据收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [如何在 Azure Monitor 中运行自定义查询](../azure-monitor/log-query/get-started-queries.md)
- [Azure Monitor 中的 Syslog 数据源](../azure-monitor/platform/data-sources-syslog.md)

**Azure 安全中心监视：** 是的

**责任：** 面向

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [Azure 开发测试实验室中的环境的安全警报](environment-security-alerts.md)