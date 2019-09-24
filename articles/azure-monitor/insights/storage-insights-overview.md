---
title: 监视 Azure 存储服务的存储 Azure Monitor （预览版） |Microsoft Docs
description: 本文介绍存储功能的 Azure Monitor，该功能可让存储管理员快速了解 Azure 存储帐户的性能和利用率问题。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2019
ms.author: magoedte
ms.openlocfilehash: 1ab49d17ebf10a1ae0277768ddbd1878195d835d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212764"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>监视存储服务的存储 Azure Monitor （预览版）

针对存储（预览版）的 Azure Monitor 提供 Azure 存储服务性能、容量和可用性的统一视图，从而全面监视 Azure 存储帐户。 你可以通过两种方式观察存储容量和性能，直接从存储帐户或查看 Azure Monitor 查看跨存储帐户组。 

本文将帮助你了解存储（预览版） Azure Monitor 的体验，它提供了针对规模的存储帐户的运行状况和性能的可操作知识，并能够专注于热点并诊断延迟、限制和可用性问题。

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>存储 Azure Monitor 简介（预览版）

在深入体验之前，您应该了解它是如何呈现和可视化信息的。 无论是直接从存储帐户选择存储功能还是从 Azure Monitor 中选择存储功能，Azure Monitor 为存储提供一致的体验。 

它的组合提供：

* 根据存储服务或 API 操作的运行状况显示可用性的快照视图 **、显示存储**服务接收的请求总数的利用率，以及显示平均时间的延迟存储服务或 API 操作类型正在处理请求。 你还可以按 blob、文件、表和队列查看容量。

* 向**下钻取**特定存储帐户的分析，以帮助诊断问题或按类别-可用性、性能、故障和容量执行详细分析。 选择这些选项中的任何一个可提供指标的深入了解。  

* 可**自定义**，可在其中更改要查看的指标、修改或设置与限制相符的阈值，并将其另存为自己的工作簿。 工作簿中的图表可以固定到 Azure 仪表板。  

此功能不要求你启用或配置任何内容，默认情况下会收集存储帐户的存储度量值。 如果你不熟悉 Azure 存储空间中提供的指标，请查看[azure 存储指标](../../storage/common/storage-metrics-in-azure-monitor.md)来查看 azure 存储度量值中的说明和定义。

>[!NOTE]
>访问此功能并不收取任何费用，只需按[Azure Monitor 定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)页中所述，为你配置或启用 Azure Monitor 基本功能付费。

>[!NOTE]
>用于存储的 Azure Monitor 不支持[常规用途 v1 帐户](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)。
>

## <a name="view-from-azure-monitor"></a>从 Azure Monitor 查看

从 Azure Monitor 中，可以查看订阅中多个存储帐户的事务、延迟和容量详细信息，并帮助确定性能、容量问题和故障。

若要查看所有订阅中的存储帐户的使用情况和可用性，请执行以下步骤。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 从 "Azure 门户中的左窗格中选择"**监视器**"，然后在"**见解**"部分，选择"**存储帐户（预览）** "。

    ![多个存储帐户视图](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>概述工作簿

在所选订阅的**概述**工作簿中，表显示订阅中分组最多10个存储帐户的交互式存储指标和服务可用性状态。 您可以根据从下列下拉列表中选择的选项筛选结果：

* 仅列出包含存储帐户**的订阅。**  

* **存储帐户**-默认情况下，预先选择10个存储帐户。 如果在作用域选择器中选择 "全部" 或多个存储帐户，将返回最多200个存储帐户。 例如，如果你选择的三个订阅中共有573个存储帐户，则只会显示200个帐户。 

* **时间范围**-默认情况下，基于所做的相应选择显示最后4个小时的信息。

下拉列表下的计数器磁贴汇总了订阅中的存储帐户总数，并反映了所选总数。 对于报表事务指标或错误的工作簿中的列，有条件颜色编码或热图。 最深的颜色具有最高的值，较浅的颜色基于最低值。 对于基于错误的列，值为红色，对于基于度量值的列，该值为蓝色。

选择 "可用性" 列中的值 "**可用性**"、" **E2E 延迟**"、"**服务器延迟**" 和 "**事务错误类型/错误**" 会将您定向到针对特定类型的存储指标定制的报告，该报告与为该存储帐户。 有关每个类别的工作簿的详细信息，请参阅下面的 "[详细存储工作簿](#detailed-storage-workbooks)" 一节。 

>[!NOTE]
>有关报表中可以显示的错误的详细信息，请参阅[响应类型架构](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions)和查找响应类型，如**ServerOtherError**、 **ClientOtherError**、 **ClientThrottlingError**。 根据所选的存储帐户，如果报告了三种以上类型的错误，则所有其他错误将以**其他**的类别表示。

默认**可用性**阈值为：

* 警告-99%
* 严重-90%

若要基于观察结果或要求设置可用性阈值，请查看[修改可用性阈值](#modify-the-availability-threshold)。 

### <a name="capacity-workbook"></a>容量工作簿

选择页面顶部的 "**容量**"，打开**容量**工作簿。 其中显示了帐户中使用的总存储量以及帐户中每个数据服务所使用的容量，以帮助识别使用的存储量。

![多个存储帐户容量工作簿](./media/storage-insights-overview/storage-account-capacity-02.png) 

对于使用蓝色值报告容量指标的工作簿中的列，有条件颜色编码或热图。 最深的颜色具有最高的值，较浅的颜色基于最低值。

在工作簿中的任一列下选择一个值时，将向下钻取到存储帐户的**容量**工作簿。 下面的 "[详细存储工作簿](#detailed-storage-workbooks)" 一节中介绍了有关向下钻取报告的更多详细信息。 

## <a name="view-from-a-storage-account"></a>从存储帐户查看

直接从存储帐户访问用于 VM 的 Azure Monitor：

1. 在 Azure 门户中，选择 "存储帐户"。

2. 从列表中选择一个存储帐户。 在 "监视" 部分，选择 "见解（预览版）"。

    ![选择的存储帐户概述页](./media/storage-insights-overview/storage-account-direct-overview-01.png)

在存储帐户的**概述**工作簿中，它会显示多个存储性能度量值，可帮助你快速评估：

* 存储服务的运行状况，立即查看控件外的问题是否会影响部署到的区域中的存储服务，这在 "**摘要**" 列下列出。

* 显示与存储容量、可用性、事务和延迟相关的最重要详细信息的交互式性能图表。  

* 指标和状态磁贴突出显示服务可用性、存储服务的事务总数、E2E 延迟和服务器延迟。

为**故障**、**性能**、**可用性**和**容量**选择任何一个按钮将打开各自的工作簿。 

![选择的存储帐户概述页](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>详细存储工作簿

是否从多个存储帐户**概述**工作簿中选择了列 "**可用性**"、" **E2E 延迟**"、"**服务器延迟**" 和 "**事务错误类型/错误**" 中的值，或选择**故障**、**性能**、**可用性**和**容量**从特定存储帐户的**概述**工作簿中，每个都提供一组针对该类别量身定制的交互式存储相关信息。  

* **可用性**打开**可用性**工作簿。 它显示了 Azure 存储服务的当前运行状况状态，该表显示了存储帐户中定义的数据服务所分类的每个对象的可用运行状况状态，以及表示所选时间范围的趋势线，以及帐户中的每个数据服务。  

    ![可用性报表示例](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E 延迟**和**服务器延迟**打开**性能**工作簿。 它包括一个汇总状态磁贴，该磁贴显示 E2E 延迟和服务器延迟、E2E 与服务器延迟的性能图表，以及由存储帐户中定义的由数据服务分类的 API 对成功调用的延迟。

    ![性能报告示例](./media/storage-insights-overview/storage-account-performance-01.png)

* 选择网格中列出的任何错误类别将打开**失败**工作簿。 该报表显示除所述的所有客户端错误的指标磁贴和成功的请求，客户端限制错误，特定于 ClientOtherError 属性的事务**响应类型**维度指标的性能图表。和两个表-**按 API 名称**和**事务按响应类型**的事务。

   ![故障报表示例](./media/storage-insights-overview/storage-account-failures-01.png)

* **容量**打开**容量**工作簿。 它显示了磁贴和图表中帐户中每个存储数据对象使用的存储总量，以及在帐户中存储了多少个数据对象。  

    ![选择的存储帐户容量页](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>固定和导出

您可以通过选择部分右上角的图钉图标将任一指标部分固定到 Azure 仪表板。

![指标部分固定到仪表板示例](./media/storage-insights-overview/workbook-pin-example.png)

多订阅和存储帐户**概述**或**容量**工作簿支持通过选择图钉图标右侧的向下箭头图标来导出 Excel 格式的结果。

![导出工作簿网格结果示例](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>为存储自定义 Azure Monitor （预览）

本部分重点介绍编辑工作簿以自定义以支持数据分析需求的常见方案：

* 将工作簿的范围限定为始终选择特定订阅或存储帐户
* 更改网格中的度量值
* 更改可用性阈值
* 更改颜色渲染

自定义项保存到自定义工作簿，以防止覆盖已发布工作簿中的默认配置。 工作簿保存在资源组中，无论是在专用于你的**我的报表**节中，还是在有权访问资源组的每个人均可访问的 "**共享报表**" 部分中。 保存自定义工作簿后，需要先进入工作簿库以启动它。

![从命令栏启动工作簿库](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>指定订阅或存储帐户

你可以配置多订阅和存储帐户**概述**或**容量**工作簿，使其在每次运行时都作用于特定的一个或多个存储帐户，请执行以下步骤。

1. 从门户中选择 "**监视器**"，然后从左侧窗格中选择 "**存储帐户（预览）** "。

2. 在**概述**工作簿上，从命令栏中选择 "**编辑**"。

3. 从 "**订阅**" 下拉列表中选择一个或多个要在其默认设置的订阅。 请记住，工作簿最多支持选择10个订阅。  

4. 从 "**存储帐户**" 下拉列表中选择一个或多个要在其默认设置的帐户。 请记住，工作簿最多支持选择200个存储帐户。 

5. 选择命令栏中的 "**另存为**"，以保存工作簿的副本和自定义项，然后单击 "**完成编辑**" 以返回到读取模式。  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>修改工作簿中的指标和颜色

预生成的工作簿包含指标数据，你可以修改或删除任何可视化效果，并可根据团队的特定需求进行自定义。 

在我们的示例中，我们使用多订阅和存储帐户容量工作簿，演示如何：

* 删除指标
* 更改颜色渲染

你可以对任何一个预生成的**故障**、**性能**、**可用性**和**容量**工作簿执行相同的更改。

1. 从门户中选择 "**监视器**"，然后从左侧窗格中选择 "**存储帐户（预览）** "。

2. 选择 "**容量**" 要切换到容量工作簿，从命令栏中选择命令栏中的 "**编辑**"。

    ![选择 "编辑" 以修改工作簿](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 在 "指标" 部分旁边，选择 "**编辑**"。 

    ![选择 "编辑" 以修改容量工作簿指标](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. 我们将删除 "已**使用的帐户" 容量时间线**列，因此请选择 "指标" 网格中的 "**列设置**"。

    ![编辑列设置](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. 在 "**编辑列设置**" 窗格中，选择 " **Storageaccounts/容量-UsedCapacity 时间线 $ |" 部分下的列。使用的帐户容量时间线 $** ，在下拉列表**列呈现**器中，选择 "**隐藏**"。 

6. 选择 "**保存并关闭**" 以提交更改。

现在，让我们将报表中的容量度量值的颜色主题更改为使用绿色而不是蓝色。

1. 选择 "度量值" 网格中的**列设置**。

2. 在 "**编辑列设置**" 窗格中，选择 " **storageaccounts/UsedCapacity $ | storageaccounts/blobservices-容量-blobcapacity 相同 $ |/" 列下的列。storageaccounts/fileservices-FileCapacity $ | $ | $ | $ | $ | storageaccounts**/queueservices/QueueCapacity-storageaccounts $。 在下拉列表**调色板**中，选择 "**绿色**"。

3. 选择 "**保存并关闭**" 以提交更改。

4. 选择命令栏中的 "**另存为**"，以保存工作簿的副本和自定义项，然后单击 "**完成编辑**" 以返回到读取模式。  

### <a name="modify-the-availability-threshold"></a>修改可用性阈值

在此示例中，我们将使用存储帐户容量工作簿，并演示如何修改可用性阈值。 默认情况下，会将磁贴和网格报表百分比可用性配置为最小阈值为90，最大阈值为99。 我们将**按 API 名称**网格将可用性**百分比**的最小阈值值更改为 85%，这意味着，如果阈值小于 85%，则运行状况状态将更改为 "严重"。 

1. 从门户中选择**存储帐户**，然后从列表中选择一个存储帐户。

2. 从左侧窗格中选择 "**见解（预览版）** "。

3. 在工作簿中，选择 "**可用性**" 以切换到可用性工作簿，然后从命令栏中选择 "**编辑**"。 

4. 向下滚动到页面底部，在 "**按 API 的可用性**" 网格的左侧向下滚动，选择 "**编辑**"。

    !["按 API 名称编辑可用性" 网格设置](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. 选择 "**列设置**"，然后在 "**编辑列设置**" 窗格的 "**列**" 部分下，选择 "**可用性（%）"（阈值 + 格式）** 。

6. 将 "**严重**运行状况状态" 的值从 " **90** " 更改为 " **85** "，然后单击 "**保存并关闭**"。

    ![修改严重状态的可用性阈值](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 选择命令栏中的 "**另存为**"，以保存工作簿的副本和自定义项，然后单击 "**完成编辑**" 以返回到读取模式。

## <a name="troubleshooting"></a>疑难解答

本部分将帮助你诊断和解决在使用 Azure Monitor 进行存储（预览版）时可能遇到的一些常见问题。 使用下面的列表来查找与具体问题相关的信息。

### <a name="resolving-performance-capacity-or-availability-issues"></a>解决性能、容量或可用性问题

若要帮助解决你在存储（预览版） Azure Monitor 标识的任何存储相关问题，请参阅 Azure 存储[故障排除指南](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)。  

## <a name="next-steps"></a>后续步骤

* 配置[指标警报](../platform/alerts-metric.md)和[服务运行状况通知](../../service-health/alerts-activity-log-service-notifications.md)，设置自动警报，以帮助检测问题。

* 了解工作簿设计为支持的方案、如何创作新报表和自定义现有报表，以及如何通过查看[使用 Azure Monitor 工作簿创建交互式报表](../app/usage-workbooks.md)。

* 有关使用存储分析及其他工具来识别、诊断和排查 Azure 存储相关问题的深入指导，请参阅[监视、诊断和排查 Microsoft Azure 存储问题](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)。
