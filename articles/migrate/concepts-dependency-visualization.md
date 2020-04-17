---
title: Azure 迁移服务器评估中的依赖项分析
description: 介绍如何使用 Azure 迁移服务器评估使用依赖项分析进行评估。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b11796f2c5d7c1d87f383e6780444e572352eff5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537740"
---
# <a name="dependency-analysis"></a>依赖项分析

本文介绍 Azure 迁移：服务器评估中的依赖项分析。

## <a name="overview"></a>概述

依赖项分析可帮助您确定要评估和迁移到 Azure 的本地计算机之间的依赖关系。 

- 在 Azure 迁移：服务器评估中，将计算机收集到组中，然后评估该组。 依赖关系分析可帮助您更准确地对计算机进行分组，并高度自信地进行评估。
- 依赖项分析使您能够识别必须一起迁移的计算机。 您可以识别计算机是否正在使用中，或者是否可以停用而不是迁移它们。
- 分析依赖项有助于确保不会留下任何内容，并避免在迁移期间出现意外中断。
- 如果您不确定计算机是否是要迁移到 Azure 的应用部署的一部分，则分析特别有用。
- [查看](common-questions-discovery-assessment.md#what-is-dependency-visualization)有关依赖项分析的常见问题。

有两种用于部署依赖项分析的选项

- **基于代理**：基于代理的依赖项分析要求在要分析的每个本地计算机上安装代理。
- **无代理**：使用无代理分析，您无需在要交叉检查的计算机上安装代理。 此选项当前处于预览状态，仅适用于 VMware VM。

> [!NOTE]
> Azure 政府中不提供基于代理的依赖项分析。 您可以使用无代理依赖项分析。

## <a name="agentless-analysis"></a>无代理分析

无代理依赖项分析的工作原理是从启用 TCP 连接数据的计算机上捕获 TCP 连接数据。 要分析的计算机上未安装代理。

### <a name="collected-data"></a>收集的数据

依赖项发现开始后，设备每隔五分钟轮询来自计算机的数据以收集数据。 这些数据通过 vCenter 服务器通过 vSphere API 从来宾 VM 中收集。 收集的数据在 Azure 迁移设备上处理，以推断标识信息，并每六小时发送到 Azure 迁移。

轮询从计算机收集此数据： 
- 具有活动连接的进程的名称。
- 运行具有活动连接的进程的应用程序的名称。
- 活动连接上的目标端口。

## <a name="agent-based-analysis"></a>基于代理的分析

对于基于代理的分析，服务器评估使用 Azure 监视器中的[服务映射解决方案](../azure-monitor/insights/service-map.md)来启用依赖项可视化和分析。 [Microsoft 监视代理/日志分析代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)和[依赖项代理](../azure-monitor/platform/agents-overview.md#dependency-agent)必须安装在要分析的每台计算机上。

### <a name="collected-data"></a>收集的数据

对于基于代理的可视化效果，将收集以下数据：

- 源计算机服务器名称、进程、应用程序名称。
- 目标计算机服务器名称、进程、应用程序名称和端口。
- 收集连接数、延迟和数据传输信息，并可用于日志分析查询。 


## <a name="compare-agentless-and-agent-based"></a>比较无代理和基于代理

表中总结了无代理可视化和基于代理的可视化之间的区别。

**要求** | **无代理** | **基于代理**
--- | --- | ---
支持 | 此选项当前处于预览状态，仅适用于 VMware VM。 [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)支持的操作系统。 | 在一般可用性 （GA） 中。
代理 | 无需在要交叉检查的计算机上安装代理。 | 要安装在要分析的每个本地计算机上的代理[：Microsoft 监视代理 （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)和[依赖项代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
Log Analytics | 非必需。 | Azure 迁移使用[Azure 监视器日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服务映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解决方案进行分析。 
工作原理 | 在启用依赖项可视化的计算机上捕获 TCP 连接数据。 发现后，它每隔五分钟收集数据。 | 安装在计算机上的服务映射代理收集有关每个进程的 TCP 进程和入站/出站连接的数据。
数据 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。<br/><br/> 收集连接数、延迟和数据传输信息，并可用于日志分析查询。 
可视化 | 单个服务器的依赖项映射可在 1 小时到 30 天的持续时间内查看。 | 单个服务器的依赖项映射。<br/><br/> 地图只能查看一个多小时。<br/><br/> 一组服务器的依赖项映射。<br/><br/> 从地图视图添加和删除组中的服务器。
数据导出 | 当前无法以表格格式下载。 | 数据可以通过日志分析进行查询。



## <a name="next-steps"></a>后续步骤
- 查看为[VMware VM、](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)[物理服务器](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)和[超](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)VM 设置基于代理的分析的要求。
- [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)VMware VM 无代理分析的要求。
- [设置](how-to-create-group-machine-dependencies.md)基于代理的依赖项可视化
- [尝试](how-to-create-group-machine-dependencies-agentless.md)VMware VM 的无代理依赖项可视化。
- 查看有关依赖项可视化的[常见问题](common-questions-discovery-assessment.md#what-is-dependency-visualization)。


