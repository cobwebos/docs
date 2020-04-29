---
title: 在 Azure Synapse 中使用扩展 Spark 历史记录服务器调试应用-Apache Spark
description: 使用扩展 Spark 历史记录服务器在 Azure Synapse Analytics 中调试和诊断 Spark 应用程序。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429208"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>使用扩展的 Apache Spark 历史记录服务器来调试和诊断 Apache Spark 应用程序

本文提供了有关如何使用扩展的 Apache Spark 历史记录服务器来调试和诊断已完成和正在运行的 Spark 应用程序的指南。

此扩展包括 "数据" 选项卡、"图形" 选项卡和 "诊断" 选项卡。使用 "**数据**" 选项卡检查 Spark 作业的输入和输出数据。 "**关系图**" 选项卡显示作业图形的数据流和重播。 "**诊断**" 选项卡显示**数据倾斜**、**时间偏差**和**执行器使用情况分析**。

## <a name="access-the-apache-spark-history-server"></a>访问 Apache Spark 历史记录服务器

Apache Spark 历史记录服务器是用于完成和运行 Spark 应用程序的 web 用户界面。 你可以从 Azure Synapse Analytics 打开 Apache Spark history server web 界面。

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>从 Apache spark 应用程序节点中打开 Spark History Server web UI

1. 打开 [Azure Synapse Analytics](https://web.azuresynapse.net/)。

2. 单击 "**监视**"，然后选择 " **Apache Spark 应用程序**"。

    ![单击 "监视"，然后选择 "spark 应用程序"。](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. 选择一个应用程序，然后通过单击它打开**日志查询**。

    ![打开 "日志查询" 窗口。](./media/apache-spark-history-server/open-application-window.png)

4. 选择**spark history server**，然后将显示 Spark history SERVER web UI。

    ![打开 spark history server。](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>从数据节点打开 Spark History Server web UI

1. 从 Azure Synapse Studio 笔记本中，从 "作业执行" 输出单元或笔记本文档底部的 "状态" 面板中选择 " **Spark history server** "。 选择“会话详细信息”****。

   ![启动 Spark 历史记录服务器](./media/apache-spark-history-server/launch-history-server2.png "启动 Spark 历史记录服务器")

2. 从 "滑出" 面板中选择 " **Spark history server** "。

   ![启动 Spark 历史记录服务器](./media/apache-spark-history-server/launch-history-server.png "启动 Spark 历史记录服务器")

## <a name="explore-the-data-tab-in-spark-history-server"></a>浏览 Spark history server 中的 "数据" 选项卡

选择要查看的作业的 ID。 然后，在 "工具" 菜单上选择 "**数据**" 以获取数据视图。 本部分说明如何在 "数据" 选项卡中执行各种任务。

* 分别选择“输入”、“输出”和“表操作”，以选中这些选项卡************。

    ![Spark 应用程序选项卡的数据](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* 通过选择 "**复制**" 复制所有行。

    ![Spark 应用程序复制操作的数据](./media/apache-spark-history-server/apache-spark-data-copy.png)

* 通过选择 " **csv**" 将所有数据保存为 csv 文件。

    ![Spark 应用程序保存操作的数据](./media/apache-spark-history-server/apache-spark-data-save.png)

* 在字段**搜索**中输入关键字进行搜索。 搜索结果会立即显示。

    ![Spark 应用程序搜索操作的数据](./media/apache-spark-history-server/apache-spark-data-search.png)

* 选择列标题将表排序，选择加号展开某行以显示更多详细信息，或选择减号折叠某行。

    ![Spark 应用程序表的数据](./media/apache-spark-history-server/apache-spark-data-table.png)

* 通过选择 "**部分下载**" 下载一个文件。 选择的文件将下载到本地。 如果该文件不再存在，则会显示一个新的选项卡，其中显示错误消息。

    ![Spark 应用程序下载行的数据](./media/apache-spark-history-server/sparkui-data-download-row.png)

* 若要复制完整路径或相对路径，请从下拉菜单中选择 "**复制完整路径**" 或 "**复制相对路径**" 选项。 对于 Azure Data Lake Storage 文件，**在 Azure 存储资源管理器中打开**Azure 存储资源管理器，并在登录时查找文件夹。

    ![Spark 应用程序复制路径的数据](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* 如果要在一页中显示的行过多，请选择表下方的页码以导航页面。

    ![Spark 应用程序页面的数据](./media/apache-spark-history-server/apache-spark-data-page.png)

* 将鼠标悬停在**数据**旁的问号以显示工具提示，或选择问号以获取详细信息。

    ![Spark 应用程序更多信息的数据](./media/apache-spark-history-server/sparkui-data-more-info.png)

* 选择“向我们提供反馈”发送问题反馈。****

    ![Spark 图形 -“向我们提供反馈”](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark history server 中的 "图形" 选项卡

选择要查看的作业的 ID。 然后，在 "工具" 菜单上选择 "**关系图**" 以获取作业图形视图。

### <a name="overview"></a>概述

可以在生成的作业关系图中查看作业的概述。 默认情况下，该图显示所有作业。 可按**作业 ID**筛选此视图。

![Spark 应用程序和作业图形作业 ID](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>显示器

默认情况下，**进度**显示处于选中状态。 可以通过在 "**显示**" 下拉列表中选择 "**读取**" 或 "**写入**" 来检查数据流。

![Spark 应用程序和作业关系图显示](./media/apache-spark-history-server/sparkui-graph-display.png)

Graph 节点显示热度地图图例中显示的颜色。

![Spark 应用程序和作业图形热度地图](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>播放

若要播放作业，请选择 "**播放**"。 你可以随时选择 "**停止**"。 播放时，任务颜色显示不同的状态：

|颜色|含义|
|-|-|
|绿色|已成功完成：作业已成功完成。|
|橙色|重试：失败但不影响作业最终结果的任务实例。 这些任务包括可能稍后会成功的重复或重试实例。|
|蓝色|正在运行：任务正在运行。|
|白色|正在等待或已跳过：任务正在等待运行，或已跳过该阶段。|
|Red|失败：任务失败。|

下图显示了绿色、橙色和蓝色状态颜色。

![Spark 应用程序和作业图形颜色示例，运行](./media/apache-spark-history-server/sparkui-graph-color-running.png)

下图显示了绿色和白色状态颜色。

![Spark 应用程序和作业图形颜色示例，跳过](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

下图显示了红色和绿色状态颜色。

![Spark 应用程序和作业图形颜色示例，失败](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> 每个作业都可以播放。 不完整的作业不支持播放。

### <a name="zoom"></a>Zoom

使用鼠标滚动在作业图上放大和缩小，或选择 "**缩放到合适大小**" 以使其适应屏幕大小。

![Spark 应用程序和作业图形缩放到合适大小](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>工具提示

悬停在图形节点上以在出现失败任务时查看工具提示，并选择一个阶段以打开其 "阶段" 页。

![Spark 应用程序和作业图形工具提示](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

在 "作业关系图" 选项卡上，如果有任务满足以下条件，则会显示一个工具提示和一个小图标：

|条件|说明|
|-|-|
|数据偏斜|数据读取大小 > 此阶段中所有任务的平均数据读取大小 * 2 和数据读取大小 > 10 MB|
|时间偏差|此阶段内所有任务的执行时间 > 平均执行时间 * 2 和执行时间 > 2 分钟|
   
![Spark 应用程序和作业关系图倾斜图标](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>图形节点说明

作业图形节点显示每个阶段的下列信息：

  * ID。
  * 名称或说明。
  * 总任务数。
  * 读取的数据：输入大小和随机读取大小之和。
  * 数据写入：输出大小和无序写入大小的总和。
  * 执行时间：从第一次尝试的开始时间到最后一次尝试的完成时间的时间和。
  * 行计数：输入记录、输出记录、随机读取记录和随机写入记录的总和。
  * 进度。

    > [!NOTE]  
    > 默认情况下，"作业图形" 节点显示每个阶段的最后一次尝试（阶段执行时间除外）的信息。 但是，在播放期间，图形节点会显示每次尝试的信息。
    >  
    > 读取和写入的数据大小为 1MB = 1000 KB = 1000 * 1000 字节。

### <a name="provide-feedback"></a>提供反馈

选择“向我们提供反馈”发送问题反馈。****

![Spark 应用程序和作业图形反馈](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>浏览 Apache Spark history server 中的 "诊断" 选项卡

若要访问 "诊断" 选项卡，请选择作业 ID。 然后，在 "工具" 菜单上选择 "**诊断**" 以获取 "作业诊断" 视图。 诊断选项卡包括“数据倾斜”、“时间偏差”和“执行程序使用情况分析”************。

分别选择“数据倾斜”、“时间偏差”和“执行程序使用情况分析”，以选中这些选项卡************。

![SparkUI - 诊断数据偏斜选项卡](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>数据倾斜

选择 "**数据倾斜**" 选项卡时，会根据指定的参数显示相应的歪斜任务。

* **指定参数** - 第一部分显示用于检测数据倾斜的参数。 默认规则是：任务数据读取超过平均任务数据的三倍，读取的任务数据超过 10 MB。 如果要为歪斜任务定义自己的规则，可以选择参数，则会相应地刷新 "**歪斜阶段**" 和 "**扭曲字符**" 部分。

* **倾斜阶段** - 第二部分显示其任务满足上面指定的条件的阶段。 如果一个阶段中存在多个倾斜任务，则倾斜阶段表仅显示倾斜程度最高的任务（例如，数据倾斜的最大数据）。

    ![sparkui - 诊断数据偏斜选项卡](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **倾斜图**–当选择了 "倾斜阶段" 表中的某行时，扭曲图将基于数据读取和执行时间显示更多任务分布详细信息。 倾斜任务标记为红色，普通任务标记为蓝色。 此图表最多可显示100个示例任务，任务详细信息显示在右下角面板中。

    ![sparkui - 阶段 10 的偏斜图表](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>时间倾斜

“时间倾斜”**** 选项卡根据任务执行时间显示倾斜任务。

* **指定参数** - 第一部分显示用于检测时间倾斜的参数。 用于检测时间倾斜的默认条件是：任务执行时间是平均执行时间的三倍，任务执行时间大于 30 秒。 可以按需更改相关参数。 与上面的“时间倾斜”选项卡一样，倾斜阶段和倾斜图表显示相应的阶段和任务信息************。

* 选择“时间偏斜”****，然后筛选的结果就会根据在“指定参数”部分设置的参数显示在“偏斜的阶段”部分。******** 选择“偏斜的阶段”部分的一个项目，然后相应的图表就会在第 3 部分绘制，任务详细信息显示在右下面板中。****

    ![sparkui - 诊断时间偏斜部分](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>执行程序使用情况分析

执行器使用情况图直观显示 Spark 作业执行器的分配和运行状态。  

1. 选择 **"执行器使用情况分析**"，然后对执行器使用情况使用四种类型的曲线进行草拟，其中包括**分配**的执行器、**运行**执行器、**空闲**执行器和**最大执行** 对于分配的执行程序，每个 "执行程序添加" 或 "执行程序已移除" 事件增加或减少分配的执行程序。 可以在“作业”选项卡中选中“事件时间线”以进行更多比较。

   ![sparkui "诊断执行器" 选项卡](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. 选择彩色图标即可选中或取消选中所有草稿中的相应内容。

    ![sparkui 诊断选择图表](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>已知问题

使用弹性分布式数据集（Rdd）的输入/输出数据不会显示在 "数据" 选项卡中。

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET for Apache Spark 文档](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

