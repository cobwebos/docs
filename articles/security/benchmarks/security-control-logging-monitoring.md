---
title: Azure 安全控制 - 日志记录和监视
description: 安全控制日志记录和监视
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545494"
---
# <a name="security-control-logging-and-monitoring"></a>安全控制：日志记录和监视

安全日志记录和监视侧重于与为 Azure 服务启用、获取和存储审核日志相关的活动。

## <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的时间同步源

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft 维护 Azure 资源的时间源，但是，您可以选择管理计算资源的时间同步设置。

如何为 Azure 计算资源配置时间同步：

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全日志管理

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.2 | 6.5, 6.6 | 客户 |

通过 Azure 监视器引入日志以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure 监视器中，使用日志分析工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。

或者，您可以将数据启用到 Azure Sentinel 或第三方 SIEM。 如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

如何使用 Azure 监视器收集平台日志和指标：

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

如何使用 Azure 监视器收集 Azure 虚拟机内部主机日志：

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

如何开始使用 Azure 监视器和第三方 SIEM 集成：

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.3 | 6.2, 6.3 | 客户 |

在 Azure 资源上启用诊断设置，以访问审核、安全和诊断日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

如何使用 Azure 监视器收集平台日志和指标：

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

了解 Azure 中的日志记录和不同的日志类型：

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 从操作系统收集安全日志

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.4 | 6.2, 6.3 | 客户 |

如果计算资源归 Microsoft 所有，则 Microsoft 负责监视它。 如果计算资源归组织所有，则您有责任对其进行监视。 可以使用 Azure 安全中心监视操作系统。 安全中心从操作系统收集的数据包括操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和登录用户。 日志分析代理还会收集崩溃转储文件。

如何使用 Azure 监视器收集 Azure 虚拟机内部主机日志：

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

了解 Azure 安全中心数据收集：

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全日志存储保留

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.5 | 6.4 | 客户 |

在 Azure 监视器中，根据组织的合规性法规设置日志分析工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

如何为日志分析工作区设置日志保留参数：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6： 监视和查看日志

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.6 | 6.7 | 客户 |

分析和监视日志是否存在异常行为，并定期查看结果。 使用 Azure 监视器的日志分析工作区查看日志并执行日志数据的查询。

或者，您可以将数据启用并车载到 Azure Sentinel 或第三方 SIEM。 

如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

了解日志分析工作区：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 监视器中执行自定义查询：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 启用异常活动的警报

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.7 | 6.8 | 客户 |

使用 Azure 安全中心与日志分析工作区监视和警报安全日志和事件中的异常活动。

或者，您可以将数据启用和车载到 Azure Sentinel。

如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

如何管理 Azure 安全中心的警报：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

如何对日志分析日志数据发出警报：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反恶意软件日志记录

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.8 | 8.6 | 客户 |

为 Azure 虚拟机和云服务启用反恶意软件事件集合。

如何为虚拟机配置 Microsoft 反恶意软件：

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

如何为云服务配置 Microsoft 反恶意软件：

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

了解微软反恶意软件：

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9： 启用 DNS 查询日志记录

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.9 | 8.7 | 客户 |

为 DNS 日志记录实施第三方解决方案。

## <a name="210-enable-command-line-audit-logging"></a>2.10： 启用命令行审核日志记录

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 2.1 | 8.8 | 客户 |

根据每个节点手动配置控制台日志记录和 PowerShell 转录。

## <a name="next-steps"></a>后续步骤

请参阅下一个安全控制：[标识和访问控制](security-control-identity-access-control.md)
