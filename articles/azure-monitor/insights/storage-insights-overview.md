---
title: 使用用于存储的 Azure Monitor 监视 Azure 存储服务 | Microsoft Docs
description: 本文介绍了用于存储的 Azure Monitor 功能，可便于存储管理员快速了解自己 Azure 存储帐户存在的性能和利用率问题。
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 6d6754594a321d297d983f9bd7e90ce074857919
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961969"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>使用用于存储的 Azure Monitor 监视存储服务

用于存储的 Azure Monitor 通过提供 Azure 存储服务性能、容量和可用性的统一视图，提供对 Azure 存储帐户的全面监视。 可以通过两种方式来观察存储容量和性能：直接在存储帐户中查看，或在 Azure Monitor 中跨存储帐户组查看。 

本文有助于了解用于存储的 Azure Monitor 提供的体验，此体验不仅可便于大规模获得关于存储帐户的运行状况和性能的可操作见解，还可便于关注热点和诊断延迟、限制和可用性问题。

## <a name="introduction-to-azure-monitor-for-storage"></a>用于存储的 Azure Monitor 简介

在深入了解此体验之前，你应该了解它如何呈现和可视化信息。 无论你是直接从存储帐户选择存储功能，还是从 Azure Monitor 中选择，用于存储的 Azure Monitor 都提供一致的体验。 

它提供以下组合功能：

* 大规模透视功能根据存储服务或 API 操作的运行状况显示可用性快照视图，利用率功能显示存储服务收到的请求总数，延迟功能显示存储服务或 API 操作类型处理请求的平均用时。 还可以按 blob、文件、表和队列查看容量。

* 对特定存储帐户进行向下钻取分析，以帮助诊断问题或按类别（即可用性、性能、故障和容量）进行详细分析。 选择这些选项中的任何一个都可以深入了解指标。  

* 可自定义，即可以更改要查看的指标、修改或设置与限制一致的阈值，并保存为你自己的工作簿。 工作簿中的图表可以固定到 Azure 仪表板。  

此功能不需要启用或配置任何设置，存储帐户的存储指标是默认收集的。 如果不熟悉可用于 Azure 存储的指标，请查阅 [ Azure 存储指标](../../storage/common/monitor-storage.md)，以查看 Azure 存储指标的说明和定义。

>[!NOTE]
>访问此功能不需任何费用。系统只会对你配置或启用的 Azure Monitor 基本功能收费，如 [Azure Monitor 定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页中所述。

>[!NOTE]
>用于存储的 Azure Monitor 不支持[常规用途 v1 帐户](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)。
>

## <a name="view-from-azure-monitor"></a>在 Azure Monitor 中查看

在 Azure Monitor 中，可以查看订阅中多个存储帐户的事务、延迟和容量详细信息，有助于发现性能、容量问题和故障。

若要查看所有订阅中的存储帐户的利用率和可用性，请按照以下步骤操作。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 依次选择 Azure 门户左侧窗格中的“Monitor”，以及“见解”部分下的“存储帐户”。

    ![包含多个存储帐户的视图](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>“概述”工作簿

在所选订阅的 **概述** 工作簿中，表显示订阅中分组最多5个存储帐户的交互式存储指标和服务可用性状态。 可以根据你从下面的下拉列表中选择的选项来筛选结果：

* 订阅 - 只列出包含存储帐户的订阅。  

* **存储帐户** -默认情况下，预先选择5个存储帐户。 如果你在范围选择器中选择所有或多个存储帐户，最多会返回 200 个存储帐户。 例如，如果你选择的三个订阅共有 573 个存储帐户，那么只会显示 200 个帐户。 

* **时间范围** - 默认情况下，系统会根据你所做的选择显示过去 4 小时的相应信息。

下拉列表下面的计数器磁贴汇总了订阅中的存储帐户总数，并反映了已选择的的存储帐户总数。 工作簿中报告事务指标或错误的列有条件颜色编码或热度地图。 最深的颜色具有最高值，较浅的颜色基于最低值。 对于基于错误的列，值为红色；对于基于指标的列，值为蓝色。

选择“可用性”、“E2E 延迟”、“服务器延迟”和“事务错误类型/错误”列中的值会定向到针对特定类型存储指标量身定制的报告，这些指标与为相应存储帐户选择的列匹配。 若要详细了解每个类别的工作簿，请参阅下面的[详细存储工作簿](#detailed-storage-workbooks)部分。 

>[!NOTE]
>若要详细了解哪些错误可以在报告中显示，请参阅[响应类型架构](../../storage/common/monitor-storage-reference.md#metrics-dimensions)，并查找 ServerOtherError、ClientOtherError、ClientThrottlingError 等响应类型。 根据所选的存储帐户，如果报告的错误超过三种类型，则其他所有错误都在“其他”类别下表示。

默认“可用性”阈值为：

* 警告 - 99%
* 严重 - 90%

若要根据观察结果或要求设置可用性阈值，请查阅[修改可用性阈值](#modify-the-availability-threshold)。 

### <a name="capacity-workbook"></a>容量工作簿

选择页面顶部的“容量”。此时，“容量”工作簿打开。 其中显示了帐户中使用的总存储容量，以及帐户中每个数据服务使用的容量，以帮助发现过度利用和未充分利用的存储。

![多个存储帐户的“容量”工作簿](./media/storage-insights-overview/storage-account-capacity-02.png) 

工作簿中使用蓝色值报告容量指标的列有条件颜色编码或热度地图。 最深的颜色具有最高值，较浅的颜色基于最低值。

选择工作簿中任何一列下的值都会向下钻取到相应存储帐户的“容量”工作簿。 下面的[详细存储工作簿](#detailed-storage-workbooks)部分介绍了关于向下钻取报告的更多详情。 

## <a name="view-from-a-storage-account"></a>在存储帐户中查看

若要直接从存储帐户访问用于 VM 的 Azure Monitor，请执行以下操作：

1. 在 Azure 门户中，选择“存储帐户”。

2. 从列表中选择一个存储帐户。 在“监视”部分中，选择“见解”。

    ![所选存储帐户的“概述”页](./media/storage-insights-overview/storage-account-direct-overview-01.png)

此存储帐户的“概述”工作簿中显示了多个存储性能指标，有助于你快速进行评估：

* 存储服务的运行状况：可便于立即确定是否有超出你控制范围的问题正在影响部署区域中的存储服务，“摘要”列下对此进行了说明。

* 交互式性能图：显示与存储容量、可用性、事务和延迟相关的最基本详细信息。  

* 指标和状态磁贴：突出显示了服务可用性、对存储服务执行的事务总数、E2E 延迟以及服务器延迟。

选择“故障”、“性能”、“可用性”和“容量”按钮中的任何一个都会打开相应的工作簿。 

![所选存储帐户的“概述”页](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>详细存储工作簿

无论是从多个存储帐户的“概述”工作簿中选择“可用性”、“E2 延迟”、“服务延迟”和“事务错误类型/错误”列中的值，还是从特定存储帐户的“概述” 工作簿中选择“故障”、“性能”“可用性”和“容量”按钮中的任何一个，都会获得一组针对相应类别量身定制的交互式存储相关信息。  

* 单击“可用性”打开“可用性”工作簿。 其中显示了 Azure 存储服务的当前运行状况状态，并包含显示按存储帐户中定义的数据服务进行分类的每个对象的运行状况状态的表（包含表示所选时间范围的趋势线），以及帐户中每个数据服务的可用性趋势图。  

    ![“可用性”报告示例](./media/storage-insights-overview/storage-account-availability-01.png)

* 单击“E2E 延迟”和“服务器延迟”打开“性能”工作簿。 其中包括显示 E2E 延迟和服务器延迟的汇总状态磁贴、E2E 延迟与服务器延迟的性能图，以及按存储帐户中定义的数据服务进行分类的 API 对成功调用的延迟进行细分的表。

    ![“性能”报告示例](./media/storage-insights-overview/storage-account-performance-01.png)

* 选择网格中列出的任何错误类别都会打开“故障”工作簿。 此报告显示其他所有客户端错误的指标磁贴（已描述的错误和成功请求除外）、客户端限制错误、特定于 ClientOtherError 属性的事务“响应类型”维度指标的性能图，以及“事务数(按 API 名称)”和“事务数(按响应类型)”这两个表。

   ![“故障”报告示例](./media/storage-insights-overview/storage-account-failures-01.png)

* 单击“容量”打开“容量”工作簿。 它在磁贴和图表中显示帐户中的每个存储数据对象使用的存储总量，以及帐户中存储了多少个数据对象。  

    ![所选存储帐户的“容量”页](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>固定和导出

可以将任何一个指标部分固定到 Azure 仪表板，具体方法是选择相应部分右上角的图钉图标。

![仪表板指标部分的图钉示例](./media/storage-insights-overview/workbook-pin-example.png)

多订阅和存储帐户的“概述”或“容量”工作簿支持通过选择图钉图标右侧的向下箭头图标来导出 Excel 格式的结果。

![导出工作簿网格结果示例](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>自定义用于存储的 Azure Monitor

此部分重点介绍了编辑工作簿的常见方案，以进行自定义来满足数据分析需求：

* 限定工作簿的范围，以始终选择特定的一个或多个订阅或存储帐户
* 更改网格中的指标
* 更改可用性阈值
* 更改颜色呈现

自定义项会保存到自定义工作簿，以防覆盖已发布工作簿中的默认配置。 无论是在私有的“我的报表”部分中，还是在有权访问资源组的每个人均可访问的“共享报表”部分中，工作簿都保存在某个资源组中。 保存自定义工作簿后，需要转到工作簿库来启动它。

![从命令栏启动工作簿库](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>指定订阅或存储帐户

可以配置多订阅和存储帐户的“概述”或“容量”工作簿，以将每次运行时的范围限定为特定的一个或多个订阅或存储帐户。请按照以下步骤操作。

1. 依次选择门户中的“Monitor”，以及左侧窗格中的“存储帐户”。

2. 在“概述”工作簿上，选择命令栏中的“编辑”。

3. 在“订阅”下拉列表中，选择一个或多个要默认选择的订阅。 请注意，工作簿最多支持共选择 10 个订阅。  

4. 在“存储帐户”下拉列表中，选择一个或多个要默认选择的帐户。 请注意，工作簿最多支持共选择 200 个存储帐户。 

5. 选择命令栏中的“另存为”，以保存包含自定义项的工作簿副本，然后单击“完成编辑”返回到阅读模式。  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>修改工作簿中的指标和颜色

预生成工作簿包含指标数据，你可以修改或删除任何一个可视化效果，并根据团队的特定需求进行自定义。

我们的示例使用多订阅和存储帐户的“容量”工作簿来展示如何执行以下操作：

* 删除指标
* 更改颜色呈现

可以对任何一个预生成“故障”、“性能”、“可用性”和“容量”工作簿执行相同的更改。

1. 依次选择门户中的“Monitor”，以及左侧窗格中的“存储帐户”。

2. 选择“容量”以切换到“容量”工作簿，然后选择命令栏中的“编辑”。

    ![选择“编辑”来修改工作簿](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 选择“指标”部分旁边的“编辑”。

    ![选择“编辑”来修改“容量”工作簿指标](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. 我们将删除“帐户使用容量时间线”列。因此，请选择“指标”网格中“列设置”。

    ![编辑列设置](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. 在“编辑列设置”窗格中，选择“列”部分下的“microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$|Account used capacity Timeline$”，然后在“列呈现器”下拉列表下选择“隐藏”。

6. 选择“保存并关闭”，以提交更改。

现在，让我们将报告中的容量指标的颜色主题更改为使用绿色（而不是蓝色）。

1. 选择“指标”网格中的“列设置”。

2. 在 "**编辑列设置**" 窗格中，选择 " **storageaccounts/UsedCapacity $ `|` Storageaccounts/blobservices-blobcapacity 相同 $/-storageaccounts $/ `|` `|` `|` **-fileservices $ $/FileCapacity/storageaccounts-queueservices $" 列中的 "**列**" 部分下的 ""。 在“调色板”下拉列表下，选择“绿色”。

3. 选择“保存并关闭”，以提交更改。

4. 选择命令栏中的“另存为”，以保存包含自定义项的工作簿副本，然后单击“完成编辑”返回到阅读模式。  

### <a name="modify-the-availability-threshold"></a>修改可用性阈值

此示例使用存储帐户“容量”工作簿来展示如何修改可用性阈值。 默认情况下，为磁贴和网格报告的可用性百分比配置的最小阈值为 90，最大阈值为 99。 我们将把“可用性(按 API 名称)”网格中的“可用性(%)”的最小阈值更改为“85%”；也就是说，如果阈值小于 85%，则运行状况状态更改为“严重”。 

1. 在门户中选择“存储帐户”，然后从列表中选择一个存储帐户。

2. 在左侧窗格中，选择“见解”。

3. 在工作簿中，选择“可用性”以切换到“可用性”工作簿，然后选择命令栏中的“编辑”。 

4. 向下滚动到页面底部，在左侧“可用性(按 API 名称)”网格旁边，选择“编辑”。

    ![编辑“可用性(按 API 名称)”网格设置](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. 选择“列设置”，然后在“编辑列设置”窗格的“列”部分下，选择“可用性(%)(阈值 + 格式化)”。

6. 将“严重”运行状况状态的值从“90”更改为“85”，然后单击“保存并关闭”。

    ![修改“严重”状态的可用性阈值](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 选择命令栏中的“另存为”，以保存包含自定义项的工作簿副本，然后单击“完成编辑”返回到阅读模式。

## <a name="troubleshooting"></a>疑难解答

如需常规故障排除指南，请参阅专用的基于工作簿的见解[故障排除文章](troubleshoot-workbooks.md)。

此部分有助于诊断和排查在使用用于存储的 Azure Monitor 时可能会遇到的一些常见问题。 使用下面的列表来查找与具体问题相关的信息。

### <a name="resolving-performance-capacity-or-availability-issues"></a>解决性能、容量或可用性问题

若要获取帮助来排查在使用用于存储的 Azure Monitor 时遇到的任何存储相关问题，请参阅 Azure 存储[故障排除指南](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)。  

### <a name="why-can-i-only-see-200-storage-accounts"></a>为什么我只能看到 200 个存储帐户？

无论选择多少个订阅，所选存储帐户的数目都不能超过 200 个。

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>如何更改可用性着色和阈值？

有关如何更改可用性着色和阈值的详细步骤，请参阅[修改可用性阈值](#modify-the-availability-threshold)部分。

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>如何对用于存储的 Azure Monitor 中显示的数据进行分析和故障排除？

 若要详细了解如何对用于存储的 Azure Monitor 中显示的 Azure 存储数据进行分析和故障排除，请参阅[监视、诊断和排除用于存储的 Microsoft Azure 故障](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)一文。

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>为什么我在指标中看不到所有类型的错误？

目前，最多显示三种不同类型的错误，其余错误都一起分组到一个桶中。 它是使用 splitByLimit 控制的，可以进行修改。 若要更改此属性，请执行以下操作：

1. 单击“编辑工作簿”。
2. 转到“指标”，单击“编辑”，然后选择“事务数总和”或要编辑的任何指标。

    ![转到“指标”，然后依次单击“编辑”和“事务数总和”](./media/storage-insights-overview/fqa7.png)

3. 然后，更改“拆分数”。

    ![选择指标参数](./media/storage-insights-overview/fqa7-2.png)

若要查看 n 种不同类型的错误，请将 splitByLimit 指定为 n+1（额外的 1 表示其余错误）。

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>我在某个存储帐户上保存了工作簿。 为什么现在找不到？

每个工作簿都保存在保存它的存储帐户中。 请尝试查找用户在其中保存了工作簿的特定存储帐户。 否则，如果不知道资源（存储帐户），就无法找到特定的工作簿。

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)和[服务运行状况通知](../../service-health/alerts-activity-log-service-notifications-portal.md)来设置自动警报，以帮助检测问题。

* 查看[使用 Azure Monitor 工作簿创建交互式报表](../platform/workbooks-overview.md)，了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。

* 有关使用存储分析及其他工具来识别、诊断和排查 Azure 存储相关问题的深入指导，请参阅[监视、诊断和排查 Microsoft Azure 存储问题](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)。

