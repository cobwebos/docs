---
title: Azure Migrate 服务器评估中的依赖关系分析
description: 介绍如何使用依赖关系分析来使用 Azure Migrate Server 评估进行评估。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024755"
---
# <a name="dependency-analysis"></a>依赖项分析

本文介绍 Azure Migrate：服务器评估中的依赖项分析。

## <a name="overview"></a>概述

依赖关系分析有助于确定要评估并迁移到 Azure 的本地计算机之间的依赖关系。 

- 在 Azure Migrate：服务器评估中，将计算机收集到一个组中，然后对该组进行评估。 依赖关系分析有助于更准确地对计算机分组，并为评估提供较高的置信度。
- 依赖关系分析使你能够标识必须一起迁移的计算机。 你可以确定计算机是否正在使用，或者是否可以取消使用（而不是迁移）。
- 分析依赖关系有助于确保不会遗留任何内容，并在迁移过程中避免意外中断。
- 如果不确定计算机是否是想要迁移到 Azure 的应用部署的一部分，则分析会特别有用。
- [查看](common-questions-discovery-assessment.md#what-is-dependency-visualization)有关依赖项分析的常见问题。

有两个选项可用于部署依赖关系分析

- **基于代理**的依赖项分析需要在要分析的每台本地计算机上安装代理。
- **无代理**：通过无代理分析，无需在要交叉检查的计算机上安装代理。 此选项目前为预览版，仅适用于 VMware Vm。

> [!NOTE]
> 基于代理的依赖项分析在 Azure 政府版中不可用。 你可以使用无代理依赖项分析。

## <a name="agentless-analysis"></a>无代理分析

无代理依赖项分析工作方式是从启用了它的计算机捕获 TCP 连接数据。 你要分析的计算机上未安装任何代理。

### <a name="collected-data"></a>收集的数据

启动依赖项发现后，设备每五分钟轮询一次计算机中的数据以收集数据。 此数据通过 vCenter Server 使用 vSphere Api 从来宾 Vm 收集。 收集的数据将在 Azure Migrate 设备上进行处理，以推导标识信息，并每隔6小时发送到 Azure Migrate。

轮询从计算机收集此数据： 
- 具有活动连接的进程的名称。
- 运行具有活动连接的进程的应用程序的名称。
- 活动连接上的目标端口。

## <a name="agent-based-analysis"></a>基于代理的分析

对于基于代理的分析，服务器评估使用 Azure Monitor 中的[服务映射解决方案](../azure-monitor/insights/service-map.md)来实现依赖关系的可视化和分析。 必须在要分析的每台计算机上安装[Microsoft Monitoring Agent/Log Analytics 代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)和[依赖项代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。

### <a name="collected-data"></a>收集的数据

对于基于代理的分析，会收集以下数据：

- 源计算机服务器名称、进程、应用程序名称。
- 目标计算机服务器名称、进程、应用程序名称和端口。
- 为 Log Analytics 查询收集和提供连接、延迟和数据传输信息的数目。 


## <a name="compare-agentless-and-agent-based"></a>比较无代理和基于代理的

无代理可视化和基于代理的可视化对象之间的差异在表中进行了总结。

**要求** | **无代理** | **基于代理**
--- | --- | ---
支持 | 此选项目前为预览版，仅适用于 VMware Vm。 [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)支持的操作系统。 | 公开上市（GA）。
Agent | 无需在要交叉检查的计算机上安装代理。 | 要在要分析的每台本地计算机上安装的代理： [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)和[依赖项代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
Log Analytics | 不要求。 | Azure Migrate 使用[Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服务映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解决方案进行依赖项分析。 
工作原理 | 捕获启用了依赖关系可视化的计算机上的 TCP 连接数据。 发现后，它会按五分钟的间隔收集数据。 | 计算机上安装的服务映射代理收集有关每个进程的 TCP 进程和入站/出站连接的数据。
数据 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。<br/><br/> 为 Log Analytics 查询收集和提供连接、延迟和数据传输信息的数目。 
可视化 | 可在一小时到30天内查看单服务器的依赖关系图。 | 单个服务器的依赖关系图。<br/><br/> 仅可在一小时内查看地图。<br/><br/> 一组服务器的依赖关系图。<br/><br/> 在映射视图中添加和删除组中的服务器。
数据导出 | 当前无法以表格格式下载。 | 可以通过 Log Analytics 查询数据。



## <a name="next-steps"></a>后续步骤
- 查看有关为[VMware vm](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)、[物理服务器](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)和[hyper-v vm](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)设置基于代理的分析的要求。
- [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)VMware vm 的无代理分析要求。
- [设置](how-to-create-group-machine-dependencies.md)基于代理的依赖项可视化
- [试用](how-to-create-group-machine-dependencies-agentless.md)VMware vm 的无代理依赖项可视化。
- 查看有关依赖关系可视化的[常见问题](common-questions-discovery-assessment.md#what-is-dependency-visualization)。


