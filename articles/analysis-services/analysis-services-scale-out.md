---
title: Azure Analysis Services 横向扩展 | Microsoft Docs
description: 通过横向扩展复制 Azure Analysis Services 服务器
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6a69d8d60b2e588ded9ccca20521195ae11ff136
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449424"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 横向扩展

通过横向扩展客户端查询可分布在多个*查询副本*中*查询池*，减少高查询工作负荷期间的响应时间。 也可将处理与查询池分开，确保客户端查询不受处理操作的负面影响。 可在 Azure 门户中配置横向扩展，也可使用 Analysis Services REST API 进行配置。

标准定价层中的服务器可使用横向扩展。 每个查询副本与服务器的计费费率相同。 将在服务器所在的同一区域中创建所有查询副本。 你可以配置的查询副本数量受服务器所在区域限制。 若要了解详细信息，请参阅[可用性(按区域)](analysis-services-overview.md#availability-by-region)。 横向扩展不会增加服务器的可用内存量。 要增加内存，需升级计划。 

## <a name="why-scale-out"></a>为什么扩展？

在典型的服务器部署中，一台服务器同时用作处理服务器和查询服务器。 如果服务器上针对模型的客户端查询数量超过服务器计划的查询处理单元 (QPU)，或模型处理与高查询工作负载同时发生，就会导致性能降低。 

通过横向扩展，可以创建查询池，最多 7 个其他查询副本资源 (8 个总数，包括你*主*服务器)。 可以调整以满足关键时刻对 QPU 的需求在查询池中的副本数，可以在任何时候分离将处理服务器与查询池。 

不论查询池中查询副本的数量如何，处理工作负载都不会分布在查询副本中。 主服务器用作处理服务器。 查询副本提供仅对主服务器和查询池中每个副本之间同步的模型数据库的查询。 

当向外扩展，可能需要 5 分钟以增量方式添加到查询池的新查询副本。 当所有查询副本都都已启动并运行，请对新客户端连接都进行负载均衡分布在查询池中的资源。 现有的客户端连接不会从当前连接到的资源更改。 向内扩展时，将终止与正在从查询池中删除的查询池资源的任何现有客户端连接。 客户端可以重新连接到其余的查询池资源。

## <a name="how-it-works"></a>工作原理

配置横向扩展第一次，在主服务器上的模型数据库时，*自动*与新查询池中的新副本进行同步。 自动同步只发生一次。 在自动同步 （blob 存储中静态加密） 的主服务器的数据文件将复制到第二个位置，也会在 blob 存储中静态加密。 在查询池中的副本就*并入*中文件的第二个集的数据。 

仅当你向外缩放服务器首次执行时自动同步，还可以执行手动同步。 同步可确保在查询池中的副本上的数据匹配的主服务器。 在处理 （刷新） 模型，在主服务器上的时，必须执行一次同步*后*处理操作都已完成。 此同步将从 blob 存储中的主服务器的文件更新的数据复制到文件的第二个集。 在查询池中的副本然后并入来自 blob 存储中文件的第二个集更新数据中。 

执行后续的横向扩展操作时，例如，增加两个到五个，在查询池中的副本数的新副本是中并入数据从 blob 存储中文件的第二个集。 没有同步。 如果您打算然后执行后会向外扩展，在查询池中的新副本的同步两次-并入冗余，资源混合速度。 执行后续的横向扩展操作时，务必要记住：

* 执行同步*向外缩放操作之前*以避免冗余，资源混合速度的添加的副本。

* 当自动执行这两个处理*和*向外缩放操作，务必首先处理数据的主服务器上，然后执行同步，然后执行向外缩放操作。 此顺序可确保对 QPU 和内存资源的最小影响。

* 即使在查询池中有没有副本，则允许同步。 如果从零到使用新数据的一个或多个副本从主服务器上的处理操作扩展，与查询池内的任何副本先执行同步，然后向外扩展。同步之前向外扩展可避免冗余，资源混合速度的新添加的副本。

* 当从主服务器中删除的模型数据库，它不会不自动会从删除查询池中的副本。 必须使用来执行同步操作[同步 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)从副本的共享的 blob 存储位置中删除该数据库的文件/秒，然后删除该模型的 PowerShell 命令在查询池中的副本上的数据库。

* 重命名主服务器上的数据库时要确保数据库已正确同步到任何副本所需额外的步骤。 重命名后，通过使用执行同步[同步 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)命令并且指定`-Database`使用旧的数据库名称的参数。 此同步从任何副本中删除的数据库和具有旧名称的文件。 然后执行另一个同步指定`-Database`使用新的数据库名称的参数。 第二个同步新命名的数据库复制到文件的第二个集，并将生成的任何副本。 无法在门户中使用同步模型命令执行这些同步。

### <a name="separate-processing-from-query-pool"></a>从查询池分离处理操作

为了最大限度地提高处理和查询操作的性能，可以选择将处理服务器与查询池分开。 分离后，现有和新的客户端连接仅会分配给查询池中的查询副本。 如果处理操作仅占用一小段时间，则可以选择将处理服务器与查询池分开仅执行处理和同步操作所需的时间，然后将其包含回查询池中。 

## <a name="monitor-qpu-usage"></a>监视 QPU 使用情况

若要确定服务器是否必须进行横向扩展，请在 Azure 门户中使用指标监视服务器。 如果 QPU 经常超过上限，则表示针对模型的查询数量超出了计划的 QPU 限制。 查询线程池队列中的查询数量超过可用的 QPU 时，查询池作业队列长度指标也会增加。 

另一个很好的指标监视是通过 ServerResourceType 平均 QPU。 此度量值进行比较的查询池的主服务器的平均 QPU。 

### <a name="to-configure-qpu-by-serverresourcetype"></a>若要配置通过 ServerResourceType QPU
1. 在指标折线图中，单击**添加的度量值**。 
2. 在中**资源**，选择你的服务器，然后在**度量值命名空间**，选择**Analysis Services 标准指标**，然后在**指标**，选择**QPU**，然后在**聚合**，选择**Avg**。 
3. 单击**应用拆分**。 
4. 在中**值**，选择**ServerResourceType**。  

有关详细信息，请参阅[监视服务器指标](analysis-services-monitor.md)。

## <a name="configure-scale-out"></a>配置横向扩展

### <a name="in-azure-portal"></a>在 Azure 门户中配置

1. 在门户中，单击“横向扩展”。使用滑块选择查询副本服务器的数量。 选择的副本数量不包括现有的服务器。

2. 在“从查询池分离处理服务器”中，选择“是”以将处理服务器和查询服务器分开。 客户端[连接](#connections)使用的默认连接字符串 (而无需`:rw`) 重定向到在查询池中的副本。 

   ![横向扩展滑块](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 单击“保存”以预配新的查询副本服务器。 

在配置服务器的横向扩展时第一次，将自动与在查询池中的副本同步主服务器上的模型。 一次，第一次配置横向扩展到一个或多个副本时，才会发生自动同步。 对同一台服务器上的副本数的后续更改*不会触发另一个自动同步*。 即使设置为零副本，然后再次向外缩放到任意数量的副本的服务器，自动同步将不会再次发生。 

## <a name="synchronize"></a>同步 

手动或使用 REST API，必须执行同步操作。

### <a name="in-azure-portal"></a>在 Azure 门户中配置

在“概述”>“模型”>“同步模型”中操作。

![横向扩展滑块](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

使用**同步**操作。

#### <a name="synchronize-a-model"></a>同步模型   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>获取同步状态  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

返回状态代码：


|代码  |描述  |
|---------|---------|
|-1     |  无效       |
|0     | 正在复制        |
|第     |  解除冻结       |
|2     |   已完成       |
|3     |   已失败      |
|4     |    正在完成     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用 PowerShell 之前,[安装或更新的最新的 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 

若要运行同步，请使用[同步 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)。

若要设置查询副本的数量，请使用[集 AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定可选的 `-ReadonlyReplicaCount` 参数。

若要分离处理服务器与查询池，请使用[集 AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定可选`-DefaultConnectionMode`参数，以使用`Readonly`。

## <a name="connections"></a>连接

服务器的“概述”页上有两个服务器名称。 如果尚未对服务器配置横向扩展，则这两个服务器名称的工作方式相同。 为服务器配置横向扩展之后，需要根据连接类型指定适当的服务器名称。 

对于最终用户客户端连接（如 Power BI Desktop、Excel 和自定义应用），请使用“服务器名称”。 

对于 PowerShell 中的 SSMS、SSDT 和连接字符串、Azure 函数应用以及 AMO，请使用“管理服务器名称”。 管理服务器名称包含特殊限定符 `:rw`（读取-写入）。 所有处理操作 （主要） 管理服务器上都发生。

![服务器名称](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>故障排除

**问题：** 用户收到错误“在连接模式 "ReadOnly" 下找不到服务器“\<服务器名称>”实例”。

**解决方案：** 选择时**分离处理服务器从查询池**选项，客户端连接使用的默认连接字符串 (而无需`:rw`) 重定向到查询池副本。 如果查询池中的副本因尚未完成同步而尚未联机，则重定向的客户端连接可能会失败。 若要防止连接失败，执行同步时查询池中必须至少有两个服务器。 每个服务器单独同步，而其他服务器保持联机。 如果在处理期间选择在查询池中没有处理服务器，则可以选择将其从池中删除以进行处理，然后在处理完成后但在同步之前将其添加回池中。 可以使用内存和 QPU 指标来监视同步状态。

## <a name="related-information"></a>相关信息

[监视服务器指标](analysis-services-monitor.md)   
[管理 Azure Analysis Services](analysis-services-manage.md) 
