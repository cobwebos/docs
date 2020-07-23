---
title: Azure Migrate 服务器评估中的依赖关系分析
description: 介绍如何使用依赖关系分析来使用 Azure Migrate Server 评估进行评估。
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 386a8cefce722c4bff09e2a7fe6d25957630ff61
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118794"
---
# <a name="dependency-analysis"></a>依赖项分析

本文介绍 Azure Migrate：服务器评估中的依赖项分析。


依赖关系分析标识发现的本地计算机之间的依赖关系。 它提供以下优势： 

- 可以更准确地将计算机收集到组中，以便更准确地进行评估。
- 你可以确定必须一起迁移的计算机。 如果你不确定哪些计算机是你要迁移到 Azure 的应用部署的一部分，这会特别有用。
- 您可以确定是否正在使用计算机，以及哪些计算机可以解除授权而非迁移。
- 分析依赖关系有助于确保不会遗留任何内容，从而避免在迁移后出现意外中断。
- [查看](common-questions-discovery-assessment.md#what-is-dependency-visualization)有关依赖项分析的常见问题。


## <a name="analysis-types"></a>分析类型

有两个选项可用于部署依赖关系分析

**选项** | **详细信息** | **公有云** | **Azure Government**
----  |---- | ---- 
**无代理** | 使用 vSphere Api 轮询 VMware Vm 的数据。<br/><br/> 不需要在 Vm 上安装代理。<br/><br/> 对于 VMware Vm，此选项目前为预览版。 | 。 | 。
**基于代理的分析** | 使用 Azure Monitor 中的[服务映射解决方案](../azure-monitor/insights/service-map.md)来启用依赖项可视化和分析。<br/><br/> 需要在要分析的每台本地计算机上安装代理。 | 支持 | 不支持。


## <a name="agentless-analysis"></a>无代理分析

无代理依赖项分析工作方式是从启用了它的计算机捕获 TCP 连接数据。 Vm 上未安装任何代理。 具有相同源服务器和进程以及目标服务器、进程和端口的连接将按逻辑分组到依赖关系中。 可以在地图视图中可视化捕获的依赖项数据，或将其导出为 CSV。 你要分析的计算机上未安装任何代理。

### <a name="dependency-data"></a>依赖关系数据

开始发现依赖关系数据后，将开始轮询：

- Azure Migrate 设备每五分钟从计算机轮询 TCP 连接数据以收集数据。
- 数据通过 vCenter Server 使用 vSphere Api 从来宾 Vm 收集。
- 轮询会收集以下数据：

    - 具有活动连接的进程的名称。
    - 运行具有活动连接的进程的应用程序的名称。
    - 活动连接上的目标端口。

- 收集的数据将在 Azure Migrate 设备上进行处理，以推导标识信息，并每隔6小时发送到 Azure Migrate


## <a name="agent-based-analysis"></a>基于代理的分析

对于基于代理的分析，服务器评估使用 Azure Monitor 中的[服务映射](../azure-monitor/insights/service-map.md)解决方案。 在要分析的每台计算机上安装[Microsoft Monitoring Agent/Log Analytics 代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)和[依赖项代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。

### <a name="dependency-data"></a>依赖关系数据

基于代理的分析提供以下数据：

- 源计算机服务器名称、进程、应用程序名称。
- 目标计算机服务器名称、进程、应用程序名称和端口。
- 为 Log Analytics 查询收集和提供连接、延迟和数据传输信息的数目。 



## <a name="compare-agentless-and-agent-based"></a>比较无代理和基于代理的

无代理可视化和基于代理的可视化对象之间的差异在表中进行了总结。

要求 | **无代理** | **基于代理**
--- | --- | ---
**支持** | 仅适用于 VMware Vm 的预览。 [查看](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)支持的操作系统。 | 公开上市（GA）。
**代理** | 你要分析的计算机上无需代理。 | 需要在要分析的每台本地计算机上安装代理。
**Log Analytics** | 不需要。 | Azure Migrate 使用[Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)中的[服务映射](../azure-monitor/insights/service-map.md)解决方案进行依赖项分析。 
**Process** | 捕获 TCP 连接数据。 发现后，它会按五分钟的间隔收集数据。 | 计算机上安装的服务映射代理收集有关 TCP 进程的数据以及每个进程的入站/出站连接。
**Data** | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。<br/><br/> 为 Log Analytics 查询收集和提供连接、延迟和数据传输信息的数目。 
**可视化** | 可在一小时到30天内查看单服务器的依赖关系图。 | 单个服务器的依赖关系图。<br/><br/> 一组服务器的依赖关系图。<br/><br/>  仅可在一小时内查看地图。<br/><br/> 在映射视图中添加和删除组中的服务器。
数据导出 | 过去30天的数据可以下载 CSV 格式。 | 可以通过 Log Analytics 查询数据。



## <a name="next-steps"></a>后续步骤

- [设置](how-to-create-group-machine-dependencies.md)基于代理的依赖项可视化。
- [试用](how-to-create-group-machine-dependencies-agentless.md)VMware vm 的无代理依赖项可视化。
- 查看有关依赖关系可视化的[常见问题](common-questions-discovery-assessment.md#what-is-dependency-visualization)。
