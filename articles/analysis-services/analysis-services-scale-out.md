---
title: "Azure Analysis Services 横向扩展 | Microsoft Docs"
description: "通过横向扩展复制 Azure Analysis Services 服务器"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: owend
ms.openlocfilehash: 0e58862684e62a65cf11266cc0320a9acd781f07
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 横向扩展

通过横向扩展，客户端查询可分布在查询池的多个查询副本中，从而在高查询工作负载期间缩短响应时间。 也可将处理与查询池分开，确保客户端查询不受处理操作的负面影响。 可在 Azure 门户中配置横向扩展，也可使用 Analysis Services REST API 进行配置。

## <a name="how-it-works"></a>工作原理

在典型的服务器部署中，一台服务器同时用作处理服务器和查询服务器。 如果服务器上针对模型的客户端查询数量超过服务器计划的查询处理单元 (QPU)，或模型处理与高查询工作负载同时发生，就会导致性能降低。 

通过横向扩展，可创建查询池，最多可添加 7 个查询副本（含你的服务器在内共 8 个）。 可减少查询副本的数量以满足关键时刻对 QPU 的需求，还可随时将处理服务器与查询池分开。 

不论查询池中查询副本的数量如何，处理工作负载都不会分布在查询副本中。 一台服务器用作处理服务器。 查询副本在查询池中仅向针对在每个副本之间同步的模型的查询提供服务。 

完成处理操作后，必须在处理服务器和查询副本服务器之间执行同步操作。 自动处理操作时，必须在成功完成处理操作后配置同步操作。

> [!NOTE]
> 标准定价层中的服务器可使用横向扩展。 每个查询副本与服务器的计费费率相同。

> [!NOTE]
> 横向扩展不会增加服务器的可用内存量。 要增加内存，需升级计划。

## <a name="monitor-qpu-usage"></a>监视 QPU 使用情况

 若要确定服务器是否必须进行横向扩展，请在 Azure 门户中使用指标监视服务器。 如果 QPU 经常超过上限，则表示针对模型的查询数量超出了计划的 QPU 限制。 查询线程池队列中的查询数量超过可用的 QPU 时，查询池作业队列长度指标也会增加。 有关详细信息，请参阅[监视服务器指标](analysis-services-monitor.md)。

## <a name="configure-scale-out"></a>配置横向扩展

### <a name="in-azure-portal"></a>在 Azure 门户中配置

1. 在门户中，单击“横向扩展”。使用滑块选择查询副本服务器的数量。 选择的副本数量不包括现有的服务器。

2. 在“从查询池分离处理服务器”中，选择“是”以将处理服务器和查询服务器分开。

   ![横向扩展滑块](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 单击“保存”以预配新的查询副本服务器。 

主服务器上的表格模型与副本服务器同步。 同步完成后，查询池开始在副本服务器之间分发传入的查询。 

### <a name="powershell"></a>PowerShell
使用 [Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) cmdlet。 指定 `-Capacity` 参数值大于 1。

## <a name="synchronization"></a>同步 

预配新的查询副本时，Azure Analysis Services 会在所有副本中自动复制模型。 还可执行手动同步操作。 处理模型时，应进行同步，以便在查询副本中同步更新。

### <a name="in-azure-portal"></a>在 Azure 门户中配置

在“概述”>“模型”>“同步模型”中操作。

![横向扩展滑块](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

同步模型   
`POST https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

获取模型同步的状态  
`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

## <a name="connections"></a>连接

服务器的“概述”页上有两个服务器名称。 如果尚未对服务器配置横向扩展，则这两个服务器名称的工作方式相同。 对服务器配置横向扩展之后，需根据连接类型指定适当的服务器名称。 

对于最终用户客户端连接（如 Power BI Desktop、Excel 和自定义应用），请使用“服务器名称”。 

对于 PowerShell 中的 SSMS、SSDT 和连接字符串、Azure 函数应用以及 AMO，请使用“管理服务器名称”。 管理服务器名称包含特殊限定符 `:rw`（读取-写入）。 所有处理操作均在管理服务器上发生。

![服务器名称](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>相关信息

[监视服务器指标](analysis-services-monitor.md)   
[管理 Azure Analysis Services](analysis-services-manage.md) 

