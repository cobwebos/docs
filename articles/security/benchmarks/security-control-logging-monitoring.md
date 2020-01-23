---
title: Azure 安全控制-日志记录和监视
description: 安全控制日志记录和监视
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545494"
---
# <a name="security-control-logging-and-monitoring"></a>安全控制：日志记录和监视

安全日志记录和监视重点介绍与启用、获取和存储 Azure 服务的审核日志相关的活动。

## <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft 维护 Azure 资源的时间源，但你可以选择管理计算资源的时间同步设置。

如何配置 Azure 计算资源的时间同步：

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.2 | 6.5、6。6 | 客户 |

通过 Azure Monitor 引入日志来聚合由终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。 如何载入 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

如何 Azure Monitor 收集平台日志和指标：

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

如何 Azure Monitor 收集 Azure 虚拟机内部主机日志：

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

如何开始 Azure Monitor 和第三方 SIEM 集成：

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：启用 Azure 资源的审核日志记录

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.3 | 6.2、6。3 | 客户 |

启用 Azure 资源的诊断设置，以便访问审核、安全和诊断日志。 自动可用的活动日志，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

如何 Azure Monitor 收集平台日志和指标：

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

了解 Azure 中的日志记录和不同日志类型：

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.4 | 6.2、6。3 | 客户 |

如果计算资源由 Microsoft 拥有，则 Microsoft 负责监视它。 如果计算资源属于你的组织，你需要负责监视它。 可以使用 Azure 安全中心监视操作系统。 安全中心从操作系统收集的数据包括操作系统类型和版本、OS 日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和已登录的用户。 Log Analytics 代理还会收集故障转储文件。

如何 Azure Monitor 收集 Azure 虚拟机内部主机日志：

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

了解 Azure 安全中心数据收集：

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.5 | 6.4 | 客户 |

在 Azure Monitor 中，根据组织的符合性法规设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期/存档存储。

如何为 Log Analytics 工作区设置日志保持参数：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.6 | 6.7 | 客户 |

分析和监视日志中的异常行为，并定期检查结果。 使用 Azure Monitor 的 Log Analytics 工作区查看日志数据并执行查询。

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。 

如何载入 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

了解 Log Analytics 工作区：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure Monitor 中执行自定义查询：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：为异常活动启用警报

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.7 | 6.8 | 客户 |

将 Azure 安全中心与 Log Analytics 工作区结合使用，以便在安全日志和事件中发现异常活动时进行监视和警报。

或者，你可以将和机载数据启用到 Azure Sentinel。

如何载入 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

如何在 Azure 安全中心管理警报：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

如何在 log analytics 日志数据上发出警报：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8：集中反恶意软件日志记录

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.8 | 8.6 | 客户 |

为 Azure 虚拟机和云服务启用反恶意软件事件收集。

如何为虚拟机配置 Microsoft 反恶意软件：

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

如何为云服务配置 Microsoft 反恶意软件：

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

了解 Microsoft 反恶意软件：

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.9 | 8.7 | 客户 |

实现 DNS 日志记录的第三方解决方案。

## <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 2.1 | 8.8 | 客户 |

基于每个节点手动配置控制台日志记录和 PowerShell 脚本。

## <a name="next-steps"></a>后续步骤

请参阅下一个安全控制：[标识和访问控制](security-control-identity-access-control.md)
