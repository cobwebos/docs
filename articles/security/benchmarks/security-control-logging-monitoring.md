---
title: Azure 安全控制 - 日志记录和监视
description: Azure 安全控制 - 日志记录和监视
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e58df0997c62a131a3c9987369f8e076a62d6654
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408346"
---
# <a name="security-control-logging-and-monitoring"></a>安全控制：日志记录和监视

安全日志记录和监视侧重于与为 Azure 服务启用、获取和存储审核日志相关的活动。

## <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft 维护 Azure 资源的时间源，但是，你可以选择管理计算资源的时间同步设置。

- [如何为 Azure Windows 计算资源配置时间同步](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [如何为 Azure Linux 计算资源配置时间同步](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.2 | 6.5、6.6 | 客户 |

通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。 

- [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何使用 Azure Monitor 收集平台日志和指标](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.3 | 6.2、6.3 | 客户 |

在 Azure 资源上启用诊断设置，以访问审核、安全和诊断日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

- [如何使用 Azure Monitor 收集平台日志和指标](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [了解 Azure 中的日志记录和不同的日志类型](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.4 | 6.2、6.3 | 客户 |

如果计算资源归 Microsoft 所有，则 Microsoft 负责监视它。 如果计算资源归你的组织所有，则由你负责监视。 可以使用 Azure 安全中心监视 OS。 安全中心从操作系统收集的数据包括 OS 类型和版本、OS（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和登录用户。 Log Analytics 代理还会收集故障转储文件。

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [了解 Azure 安全中心数据收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.5 | 6.4 | 客户 |

在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

- [更改 Log Analytics 中的数据保留期](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [如何为 Azure 存储帐户日志配置保留策略](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.6 | 6.7 | 客户 |

分析和监视日志中的异常行为，并定期查看结果。 使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。

或者，可以将数据启用并加入 Azure Sentinel 或第三方 SIEM。 

- [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [了解 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [如何在 Azure Monitor 中执行自定义查询](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.7 | 6.8 | 客户 |

使用 Azure 安全中心和 Log Analytics 工作区监视安全日志和事件中的异常活动并发出警报。

或者，你可以将和机载数据启用到 Azure Sentinel。

- [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何在 Azure 安全中心管理警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [如何针对 Log Analytics 日志数据发出警报](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.8 | 8.6 | 客户 |

为 Azure 虚拟机和云服务启用反恶意软件事件集合。

- [如何为虚拟机配置 Microsoft Antimalware](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [如何为云服务配置 Microsoft Antimalware](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [了解 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.9 | 8.7 | 客户 |

根据组织的需求，从 Azure 市场实现 DNS 日志记录解决方案的第三方解决方案。  

## <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 2.10 | 8.8 | 客户 |

在所有受支持的 Azure Windows 虚拟机上使用 Microsoft Monitoring Agent 来记录进程创建事件和命令行字段。   对于受支持的 Azure Linux 虚拟机，可以手动配置每个节点的控制台日志记录，并使用 Syslog 来存储数据。  同时，使用 Azure Monitor 的 Log Analytics 工作区查看日志并对 Azure 虚拟机中记录的数据执行查询。 

- [Azure 安全中心中的数据收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [如何在 Azure Monitor 中执行自定义查询](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Azure Monitor 中的 Syslog 数据源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[标识和访问控制](security-control-identity-access-control.md)