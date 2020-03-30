---
title: 使用 Azure 监视器监视 Azure 存储服务以进行存储（预览）*微软文档
description: 本文介绍了 Azure 监视器存储功能，该功能使存储管理员能够快速了解其 Azure 存储帐户的性能和利用率问题。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662513"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>使用 Azure 监视器监视存储服务（预览）

Azure 存储监视器（预览）通过提供 Azure 存储服务性能、容量和可用性的统一视图，提供 Azure 存储帐户的全面监视。 您可以通过两种方式观察存储容量和性能，直接从存储帐户查看，或者从 Azure 监视器查看，以查看跨存储帐户组。 

本文将帮助您了解 Azure 存储监视器（预览）提供的经验，以便获得有关存储帐户的运行状况和性能的可操作知识，并能够专注于热点并诊断延迟、限制、和可用性问题。

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Azure 监视器存储简介（预览版）

在深入了解体验之前，您应该了解它如何呈现和可视化信息。 无论是直接从存储帐户还是从 Azure 监视器中选择存储功能，Azure 存储监视器都提供了一致的体验。 

组合它提供：

* **在比例透视**显示基于存储服务或 API 操作的运行状况的快照视图，显示存储服务接收的请求总数的利用率，以及显示存储服务或 API 操作类型处理请求的平均时间的延迟。 您还可以按 Blob、文件、表和队列查看容量。

* **向下钻取**对特定存储帐户的分析，以帮助诊断问题或按类别执行详细分析 - 可用性、性能、故障和容量。 选择其中任一选项可深入了解指标。  

* **可自定义**，您可以在其中更改要查看的指标，修改或设置与您的限制一致的阈值，并将其另存为您自己的工作簿。 工作簿中的图表可以固定到 Azure 仪表板。  

此功能不要求您启用或配置任何内容，默认情况下会收集存储帐户中的存储指标。 如果您不熟悉 Azure 存储上可用的指标，请查看 Azure[存储](../../storage/common/storage-metrics-in-azure-monitor.md)指标中的说明和定义。

>[!NOTE]
>访问此功能不收取任何费用，并且只需为配置或启用的 Azure 监视器基本功能付费，如[Azure 监视器定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页上所述。

>[!NOTE]
>Azure 存储监视器不支持通用[v1 帐户](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)。
>

## <a name="view-from-azure-monitor"></a>从 Azure 监视器查看

在 Azure 监视器中，您可以查看订阅中多个存储帐户的事务、延迟和容量详细信息，并帮助识别性能、容量问题和故障。

要查看所有订阅中存储帐户的利用率和可用性，请执行以下步骤。

1. 登录到 Azure[门户](https://portal.azure.com)。

2. 从 Azure 门户中的左侧窗格中选择 **"监视器"，** 在 **"见解"** 部分下选择 **"存储帐户（预览）"。**

    ![多个存储帐户视图](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>概述工作簿

在所选订阅的 **"概述**"工作簿上，该表显示订阅中最多 10 个存储帐户的交互式存储指标和服务可用性状态。 您可以根据从以下下拉列表中选择的选项筛选结果：

* **订阅**- 仅列出具有存储帐户的订阅。  

* **存储帐户**- 默认情况下，预先选择了 10 个存储帐户。 如果在范围选择器中选择所有或多个存储帐户，则最多将返回 200 个存储帐户。 例如，如果您在所选的三个订阅中共有 573 个存储帐户，则只显示 200 个帐户。 

* **时间范围**- 默认情况下，根据所做的相应选择显示最后 4 小时的信息。

下拉列表下的计数器磁贴将汇总订阅中的存储帐户总数，并反映所选的存储帐户总数中有多少。 工作簿中报告交易记录指标或错误的列有条件颜色编码或热图。 最深的颜色具有最高值，较浅的颜色基于最低值。 对于基于错误的列，该值为红色，对于基于指标的列，该值为蓝色。

在列中选择"**可用性****"、"E2E 延迟**"、"**服务器延迟**"和"**事务错误类型/错误**"中的值将引导您到根据为该存储帐户选择的列定制的特定类型的存储指标的报表。 有关每个类别的工作簿的详细信息，请参阅下面的[详细存储工作簿](#detailed-storage-workbooks)部分。 

>[!NOTE]
>有关哪些错误可以在报表中显示的详细信息，请参阅[响应类型架构](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions)并查找响应类型，如**服务器其他错误**、**客户端其他错误**、**客户端限制错误**等。 根据所选的存储帐户，如果报告的错误类型超过三种，则所有其他错误都表示为 **"其他**" 类别。

默认**可用性**阈值为：

* 警告 - 99%
* 严重 - 90%

要根据观察结果或要求设置可用性阈值，请查看[修改可用性阈值](#modify-the-availability-threshold)。 

### <a name="capacity-workbook"></a>容量工作簿

选择页面顶部的 **"容量**"，然后打开 **"容量**"工作簿。 它显示帐户中使用的总存储量和帐户中每个数据服务用于帮助识别已使用存储的容量。

![多个存储帐户 容量工作簿](./media/storage-insights-overview/storage-account-capacity-02.png) 

工作簿中用于报告具有蓝色值的容量指标的列有条件颜色编码或热图。 最深的颜色具有最高值，较浅的颜色基于最低值。

当您在工作簿中的任何一列下选择值时，将向下钻取到存储帐户的 **"容量**"工作簿。 有关向下钻取报表的详细信息，请参阅下面的[详细存储工作簿](#detailed-storage-workbooks)部分。 

## <a name="view-from-a-storage-account"></a>从存储帐户查看

要直接从存储帐户访问 VM 的 Azure 监视器，：

1. 在 Azure 门户中，选择存储帐户。

2. 从列表中选择存储帐户。 在“监视”部分选择“见解(预览版)”。

    ![选定的存储帐户概述页](./media/storage-insights-overview/storage-account-direct-overview-01.png)

在存储帐户**的概述**工作簿上，它显示了几个存储性能指标，可帮助您快速评估：

* 存储服务的运行状况，可立即查看超出您的控制范围的问题是否影响所部署到的区域的存储服务，该列位于 **"摘要"** 列下。

* 交互式性能图表，显示与存储容量、可用性、事务和延迟相关的最基本详细信息。  

* 突出显示服务可用性、存储服务事务总数、E2E 延迟和服务器延迟的指标和状态磁贴。

为**失败**、**性能**、**可用性**和**容量**选择任一按钮将打开相应的工作簿。 

![选定的存储帐户概述页](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>详细的存储工作簿

无论您是在列**可用性****、E2E 延迟**、**服务器延迟**和**事务错误类型/错误**从多个存储帐户**概述**工作簿中选择值，还是从特定存储帐户的概述工作簿中选择"**故障**"、"**性能**"、"**可用性**"和 **"容量****"** 中的任何一个按钮，每个按钮都会提供一组针对该类别定制的交互式存储相关信息。  

* **可用性**打开**可用性**工作簿。 它显示 Azure 存储服务的当前运行状况状态、显示每个对象的可用运行状况状态，该表由存储帐户中定义的数据服务分类，趋势线表示所选时间范围，以及可用性趋势图帐户中的每个数据服务。  

    ![可用性报告示例](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E 延迟**和**服务器延迟**将打开**性能**工作簿。 它包括显示 E2E 延迟和服务器延迟的汇总状态磁贴、E2E 与服务器延迟的性能图表，以及按存储帐户中定义的数据服务分类的 API 成功调用的表。

    ![绩效报告示例](./media/storage-insights-overview/storage-account-performance-01.png)

* 选择网格中列出的任何错误类别将打开**失败**工作簿。 报表显示除描述错误和成功请求之外的所有其他客户端错误的指标磁贴、客户端限制错误、特定于 ClientOtherError 属性的事务**响应类型**维度指标的性能图表以及两个表 -**按 API 名称的事务**和**按响应类型的事务**。

   ![失败报告示例](./media/storage-insights-overview/storage-account-failures-01.png)

* **容量**打开**容量**工作簿。 它显示磁贴和图表中帐户中每个存储数据对象的存储总量，以及帐户中存储的数据对象数。  

    ![选定的存储帐户容量页](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>针和导出

您可以通过选择分区右上角的图钉图标，将任一指标部分固定到 Azure 仪表板。

![指标节针到仪表板示例](./media/storage-insights-overview/workbook-pin-example.png)

多订阅和存储帐户**概述**或**容量**工作簿支持通过选择图钉图标右侧的向下箭头图标来导出 Excel 格式的结果。

![导出工作簿网格结果示例](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>自定义 Azure 监视器以进行存储（预览）

本节重点介绍用于编辑工作簿以支持数据分析需求进行自定义的常见方案：

* 将工作簿的范围范围范围，以便始终选择特定的订阅或存储帐户
* 更改网格中的指标
* 更改可用性阈值
* 更改颜色渲染

自定义项将保存到自定义工作簿中，以防止覆盖我们已发布的工作簿中的默认配置。 工作簿保存在资源组中，无论是在您专用的"**我的报告"** 部分中，还是"**共享报表**"部分中，有权访问资源组的每个人都可以访问。 保存自定义工作簿后，需要转到工作簿库来启动它。

![从命令栏启动工作簿库](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>指定订阅或存储帐户

您可以将多订阅和存储帐户**概述**或**容量**工作簿配置为每次运行时限定为特定订阅或存储帐户，执行以下步骤。

1. 从门户中选择 **"监视器**"，然后从左侧窗格中选择 **"存储帐户（预览）。"**

2. 在 **"概述**"工作簿上，从命令栏中选择 **"编辑**"。

3. 从 **"订阅**"下拉列表中选择一个或多个您希望默认订阅的订阅。 请记住，工作簿支持选择最多 10 个订阅。  

4. 从 **"存储帐户**"下拉列表中选择您希望默认的一个或多个帐户。 请记住，工作簿支持选择最多 200 个存储帐户。 

5. 选择"从命令栏**中保存为"** 以保存工作簿的副本与自定义项，然后单击 **"完成编辑**"以返回到读取模式。  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>修改工作簿中的指标和颜色

预构建的工作簿包含指标数据，您可以修改或删除任何一个可视化效果，并根据您的团队的特定需求进行自定义。

在我们的示例中，我们使用多订阅和存储帐户容量工作簿，以演示如何：

* 删除指标
* 更改颜色渲染

您可以针对任何预构建**的失败**、**性能**、**可用性**和**容量**工作簿执行相同的更改。

1. 从门户中选择 **"监视器**"，然后从左侧窗格中选择 **"存储帐户（预览）。"**

2. 选择 **"容量**"以切换到容量工作簿，然后从命令栏中选择"从命令栏**进行编辑**"。

    ![选择编辑以修改工作簿](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 在指标部分旁边，选择 **"编辑**"。

    ![选择"编辑"以修改容量工作簿指标](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. 我们将删除"**帐户使用的容量时间线"** 列，因此在指标网格中选择**列设置**。

    ![编辑列设置](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. 在 **"编辑"列设置**窗格中，在 **"列**"部分**Microsoft.存储/存储帐户 -容量-使用容量时间线$下选择。帐户使用的容量时间轴 $**， 下拉列表**下列呈现器**选择**隐藏**.

6. 选择 **"保存"并关闭**以提交更改。

现在，让我们更改报表中的容量指标的颜色主题，以使用绿色而不是蓝色。

1. 在指标网格中选择**列设置**。

2. In the **Edit column settings** pane, select under the **Columns** section **microsoft.storage/storageaccounts-Capacity-UsedCapacity$|microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$|microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storage/storageaccounts/tableservices-Capacity-TableCapacity$**. 在下拉列表 **"调色板**"下，选择 **"绿色**"。

3. 选择 **"保存"并关闭**以提交更改。

4. 选择"从命令栏**中保存为"** 以保存工作簿的副本与自定义项，然后单击 **"完成编辑**"以返回到读取模式。  

### <a name="modify-the-availability-threshold"></a>修改可用性阈值

在此示例中，我们使用存储帐户容量工作簿，并演示如何修改可用性阈值。 默认情况下，磁贴和网格报告百分比可用性配置为最小阈值为 90，最大阈值为 99。 我们将将 **"可用****点"网格中可用性% 的**最小阈值更改为 85%，这意味着如果阈值小于 85%，运行状况状态将更改为严重。 

1. 从门户中选择**存储帐户**，然后从列表中选择存储帐户。

2. 从左侧窗格中选择**见解（预览）。**

3. 在工作簿中，选择 **"可用性**"以切换到可用性工作簿，然后从命令栏中选择 **"编辑**"。 

4. 向下滚动到页面底部和左侧的 API**网格可用性**旁边，选择 **"编辑**"。

    ![按 API 名称网格设置编辑可用性](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. 选择**列设置**，然后在 **"编辑列设置"** 窗格中，在 **"列"** 部分下选择 **"可用性 （%） "（阈值 = 格式化）**.

6. 将 **"严重**运行状况状态"的值从**90**更改为**85，** 然后单击"**保存并关闭**"。

    ![修改关键状态的可用性阈值](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 选择"从命令栏**中保存为"** 以保存工作簿的副本与自定义项，然后单击 **"完成编辑**"以返回到读取模式。

## <a name="troubleshooting"></a>疑难解答

本节将帮助您诊断和排除使用 Azure 监视器进行存储（预览）时可能遇到的一些常见问题。 使用下面的列表来查找与具体问题相关的信息。

### <a name="resolving-performance-capacity-or-availability-issues"></a>解决性能、容量或可用性问题

为了帮助解决使用 Azure 监视器进行存储（预览）确定的任何与存储相关的问题，请参阅 Azure 存储[故障排除指南](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)。  

### <a name="why-can-i-only-see-200-storage-accounts"></a>为什么我只能看到 200 个存储帐户？

所选存储帐户数的限制为 200，而不考虑所选的订阅数。

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>单击仪表板中最近固定的磁贴时会发生什么情况？

* 如果单击磁贴上的任意位置，它将将带您到固定磁贴的选项卡。 例如，如果在"存储帐户概览"选项卡中固定图形，则当您单击仪表板中的磁贴时，它将打开该默认视图，但是，如果您从自己的已保存副本中固定图形，则该图形将打开已保存的副本的视图。
* 标题左上角的筛选器图标将打开"配置磁贴设置"选项卡。
* 右上角的椭圆图标将为您提供"自定义标题数据"、"自定义"、"刷新"和"从仪表板中删除"的选项。

### <a name="what-happens-when-i-save-a-workbook"></a>保存工作簿时会发生什么情况？

* 保存工作簿时，它允许您使用编辑创建工作簿的新副本并更改标题。 保存不会覆盖工作簿，当前工作簿将始终是默认视图。
* **未保存**的工作簿只是默认视图。


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>为什么我在门户中看不到所有订阅？

门户将仅在门户启动时显示所选订阅的数据。 要更改所选订阅的内容，请转到右上角，然后单击带有筛选器图标的笔记本。 这将显示目录 + 订阅选项卡。

![目录 + 订阅](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>如何更改可用性的着色和阈值？

有关如何更改颜色和阈值的可用性的详细步骤，请参阅["修改可用性阈值"](storage-insights-overview.md#modify-the-availability-threshold)部分。

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>如何分析和排除 Azure 监视器中显示的数据以进行存储？

 有关如何分析和排除 Azure 监视器中存储监视器中显示的 Azure 存储数据的详细信息，请参阅["监视、诊断和故障排除 Microsoft Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting)"一文。

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>为什么我看不到指标中的所有类型的错误？

目前，最多显示三种不同类型的错误，其余错误在单个存储桶中分组。 它使用拆分限制进行控制，可以修改。 要更改此属性：

1. 单击编辑工作簿。
2. 转到指标，单击编辑，然后选择 **"交易记录"、"总和**"或要编辑的任何指标。

    ![转到指标，然后单击编辑，然后点击"交易，总和"](./media/storage-insights-overview/fqa7.png)

1. 然后更改拆分数。

    ![选择指标参数"](./media/storage-insights-overview/fqa7-2.png)

如果要看到 n 种不同类型的错误，请将拆分比 Limit 指定为 n+1，则对于其余错误，请额外看到 1 个错误。

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>我在某个存储帐户上保存了工作簿。 为什么我现在找不到它？

每个工作簿都保存在您将其保存在的存储帐户中。 尝试查找用户在其中保存工作簿的特定存储帐户。 否则，在不知道资源（存储帐户）的情况下，无法查找特定的工作簿。

### <a name="what-is-time-range"></a>什么是时间范围？

时间范围显示来自特定时间范围的数据。 例如，如果时间范围为 24 小时，则显示过去 24 小时的数据。

### <a name="what-is-time-granularity-time-grain"></a>什么是时间粒度（时间粒度）？

时间粒度是两个数据点之间的时差。 例如，如果时间粒度设置为 1 秒，这意味着每秒收集指标。

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>将工作簿的任何部分固定到仪表板后，时间粒度是多少？

默认时间粒度设置为自动，当前无法更改。

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>如何更改仪表板上工作簿步骤的时间范围/时间范围？

默认情况下，仪表板磁贴上的时间跨度/时间范围设置为 24 小时，要更改右上角的椭圆的单击，请选择 **"自定义磁贴数据**"，选中"覆盖标题级别的仪表板时间设置"框，然后使用下拉菜单选择时间跨度。  

![选择磁贴右角的椭圆，然后选择"自定义此数据"](./media/storage-insights-overview/fqa-data-settings.png)

![在"配置磁贴设置"中选择时间跨度下拉列表以更改时间跨度/时间范围](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>如何更改工作簿或固定到仪表板的工作簿步骤的标题？

固定到仪表板的工作簿或工作簿步骤的标题将保留与工作簿中相同的名称。 要更改标题，必须保存自己的工作簿副本。 然后，您可以在按保存之前命名工作簿。

![选择"保存在顶部"以保存工作簿的副本并更改其名称](./media/storage-insights-overview/fqa-change-workbook-name.png)

要更改已保存工作簿中步骤的名称，请选择在步骤下进行编辑，然后在设置的最底部选择齿轮。

![选择在工作簿步骤底部进行编辑以打开设置](./media/storage-insights-overview/fqa-edit.png)
![设置 在设置中选择底部的齿轮，以便能够更改步骤名称](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)[和服务运行状况通知](../../service-health/alerts-activity-log-service-notifications.md)以设置自动警报以帮助检测问题。

* 了解工作簿旨在支持的方案、如何通过查看[使用 Azure 监视器工作簿创建交互式报表](../app/usage-workbooks.md)来编写新报表和自定义现有报表等。

* 有关使用存储分析及其他工具来识别、诊断和排查 Azure 存储相关问题的深入指导，请参阅[监视、诊断和排查 Microsoft Azure 存储问题](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)。
