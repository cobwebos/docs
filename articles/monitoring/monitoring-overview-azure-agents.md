---
title: Azure 监视代理概述 | Microsoft Docs
description: 本文提供可用于支持监视 Azure VM 的 Azure 代理的详细概述。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088397"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>用于监视 Azure 虚拟机的 Azure 代理概述
Microsoft Azure 提供多种方式用于从 Azure 中托管的虚拟机，或者从运行 Microsoft Windows 和 Linux 的其他云提供程序收集各种数据。  本文将会描述每种代理的差异和功能，帮助你确定哪种代理能够为服务管理或一般性监视要求提供支持。  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Azure 诊断和 Log Analytics 代理的比较
在 Azure 中，目前可以使用两种类型的代理来监视 Azure VM - Azure 诊断扩展，以及适用于 Linux 和 Windows 的 Log Analytics 代理。  这些代理在本质上都是收集指标和日志，并将其转发到存储库。 但是，两者的相似性也仅仅是体现在这一方面。  

[Azure 诊断扩展](../monitoring-and-diagnostics/azure-diagnostics.md)（自 2010 年推出正式版以来就已针对 Azure 云服务提供）代理以简单的方式从 Azure IaaS 资源（例如 VM）收集诊断数据，并将这些数据保存在 Azure 存储中。  将数据保存到存储中后，可以选择使用多种可用工具之一（例如 [Visual Studio 中的服务器资源管理器](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md)和 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)）查看这些数据。

可以选择收集：

* 一组预定义的操作系统性能计数器和事件日志，或者指定要收集的数据。 
* 针对 IIS Web 服务器发出的所有请求和/或失败的请求
* .NET 应用程序跟踪输出日志
* Windows 事件跟踪 (ETW) 事件 
* 从 syslog 收集日志事件  
* 故障转储 

或者，可以使用[事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)将数据转发到 [Application Insights](../application-insights/app-insights-cloudservices.md)、[Log Analytics](../log-analytics/log-analytics-overview.md) 或非 Azure 服务。 

对于高级监视（不仅仅是收集指标和日志子集），需要使用适用于 Windows 和 Linux 的 Log Analytics 代理。  借助此代理可以利用自动化和 Log Analytics 等 Azure 服务（包括它们提供的完整功能集），在 Azure VM 整个生命周期内为其提供全面的管理。 这包括：

* 对操作系统更新进行 [Azure 自动化更新管理](../automation/automation-update-management.md)
* 使用 [Azure 自动化 Desired State Configuration](../automation/automation-dsc-overview.md) 来保持一致的配置状态
* 使用 [Azure 自动化更改跟踪和库存](../automation/automation-change-tracking.md)来跟踪配置更改
* 使用 Log Analytics 从 OS 和托管的应用程序（例如 [FluentD](../log-analytics/log-analytics-data-sources-json.md)、[自定义日志](../log-analytics/log-analytics-data-sources-custom-logs.md)以及 [MySQL 和 Apache](../log-analytics/log-analytics-data-sources-linux-applications.md)）收集自定义日志
* Azure 服务（例如 [Application Insights](https://docs.microsoft.com/azure/application-insights/) 和 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/)）原生就会将其数据直接存储在 Log Analytics 中。  

## <a name="next-steps"></a>后续步骤

- 请参阅[使用 Log Analytics 从环境中的计算机收集数据](../log-analytics/log-analytics-concept-hybrid.md)，了解在数据中心或其他云环境中的计算机上部署代理的要求和可用方法。
- 请参阅[收集有关 Azure 虚拟机的数据](../log-analytics/log-analytics-quick-collect-azurevm.md)，以配置 Azure VM 的数据收集。 