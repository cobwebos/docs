---
title: 将 Tez UI 与基于 Windows 的 HDInsight 配合使用 - Azure
description: 了解如何使用 Tez UI 调试基于 Windows 的 HDInsight 上的 Tez 作业。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: f54cc60f9490b8a5ca1872a290c3895ea8b0c5e4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590895"
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>使用 Tez UI 调试基于 Windows 的 HDInsight 上的 Tez 作业
Tez UI 可以用来调试使用 Tez 作为执行引擎的 Hive 作业。 Tez UI 可以将作业显示为包含已连接项目的图形，深入了解每个项目并检索统计信息和日志记录信息。

> [!IMPORTANT]
> 本文档中的步骤需要使用 Windows 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="prerequisites"></a>先决条件
* 基于 Windows 的 HDInsight 群集。 有关创建新群集的步骤，请参阅[开始使用基于 Windows 的 HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md)。

  > [!IMPORTANT]
  > Tez UI 只在 2016 年 2 月 8 日以后创建的基于 Windows 的 HDInsight 群集上提供。
  >
  >
* 基于 Windows 的远程桌面客户端。

## <a name="understanding-tez"></a>了解 Tez
Tez 是 Hadoop 中的一种可扩展数据处理框架，其处理速度比传统的 MapReduce 处理要快。 可以将以下文本作为 Hive 查询的一部分，以便启用 Tez：

    set hive.execution.engine=tez;

Tez 会创建一个有向无环图 (DAG)，用于描述作业所需操作的执行顺序。 单独的操作称为顶点，每个顶点执行完整作业的一部分。 实际执行顶点所描述的工作称为完成任务，任务可以分布在群集的多个节点中。

### <a name="understanding-the-tez-ui"></a>了解 Tez UI
Tez UI 是一个网页，提供使用 Tez 的进程的信息。 它可以为以下情况提供有用的信息：

* 监视长时间运行的进程、查看映射的进度以及精简任务。
* 分析成功进程或失败进程的历史数据，了解处理过程的改进方式或其失败的原因。

## <a name="generate-a-dag"></a>生成 DAG
如果使用 Tez 引擎的作业当前正在运行或过去曾经运行过，则 Tez UI 包含数据。 通常情况下，无需使用 Tez 即可解析简单的 Hive 查询。 进行筛选、分组、排序、联接等操作的更复杂查询需要 Tez。

使用以下步骤来运行使用 Tez 的 Hive 查询。

1. 在 Web 浏览器中导航到 https://CLUSTERNAME.azurehdinsight.net，其中“CLUSTERNAME”是 HDInsight 群集的名称。
2. 从页面顶部的菜单中选择“Hive 编辑器”。 此时会显示包含以下示例性查询的页面。

        Select * from hivesampletable

    擦除示例性查询，将其替换为以下内容。

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. 选择“提交”按钮。 页面底部的“作业会话”部分会显示查询的状态。 一旦状态更改为“已完成”，即可选择“查看详细信息”链接来查看结果。 “作业输出”应如下所示：

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>使用 Tez UI
> [!NOTE]
> Tez UI 只能从群集头节点的桌面使用，因此必须使用远程桌面来连接到头节点。
>
>

1. 在 [Azure 门户](https://portal.azure.com)中，选择 HDInsight 群集。 从 HDInsight 边栏选项卡的顶部，选择“远程桌面”图标。 此链接会显示远程桌面边栏选项卡

    ![远程桌面图标](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. 在远程桌面边栏选项卡中，选择“连接”以连接到群集头节点。 出现提示时，使用群集远程桌面用户的用户名和密码来验证连接。

    ![远程桌面连接图标](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > 如果尚未启用远程桌面连接，请提供用户名、密码和到期日期，并选择“启用”来启用远程桌面。 一旦启用，即可使用前面的步骤进行连接。
   >
   >
3. 连接后，在远程桌面上打开 Internet Explorer，在浏览器的右上角选择齿轮图标，并选择“兼容性视图设置”。
4. 在“兼容性视图设置”底部，清除“在兼容性视图中显示 Intranet 站点”和“使用 Microsoft 兼容性列表”所对应的复选框，并选择“关闭”。
5. 在 Internet Explorer 中浏览到 http://headnodehost:8188/tezui/#/。 此时会显示 Tez UI

    ![Tez UI](./media/hdinsight-debug-tez-ui/tezui.png)

    在 Tez UI 加载以后，会看到群集中当前正在运行或过去曾经运行过的 DAG 的列表。 默认视图包括“DAG 名称”、“ID”、“提交者”、“状态”、“开始时间”、“结束时间”、“持续时间”、“应用程序 ID”和“队列”。 使用页面右侧的齿轮图标可以添加更多列。

    如果只有一个条目，则该条目是前一部分运行的查询的结果。 如果有多个条目，可在 DAG 上方的字段中输入搜索条件，并按 **Enter** 进行搜索。
6. 选择最新的 DAG 条目的 **DAG 名称**。 此链接会显示有关 DAG 的信息，以及用于下载 JSON Zip 文件（其中包含有关 DAG 的信息）的选项。

    ![DAG 详细信息](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. “DAG 详细信息”上方是多个可用于显示 DAG 相关信息的链接。

   * **DAG 计数器**显示此 DAG 的计数器信息。
   * **图形视图**显示此 DAG 的图形表示方式。
   * “所有顶点”显示此 DAG 中顶点的列表。
   * “所有任务”显示此 DAG 中所有顶点的任务的列表。
   * **所有 TaskAttempts** 显示尝试针对此 DAG 运行任务的信息。

     > [!NOTE]
     > 如果滚动“顶点”、“任务”和“TaskAttempts”的列显示，则会注意到存在查看“计数器”的链接，以及每个行的“查看或下载日志”链接。
     >
     >

     如果作业失败，“DAG 详细信息”会显示状态“已失败”，同时会显示有关已失败任务的信息链接。 DAG 详细信息下方会显示诊断信息。
8. 选择“图形视图”。 此视图显示 DAG 的图形表示方式。 将鼠标放在视图中的单个顶点上即可显示其信息。

    ![图形视图](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. 单击某个顶点会加载该项的“顶点详细信息”。 单击“映射 1”顶点可显示该项的详细信息。 选择“确认”进行导航确认。

    ![顶点详细信息](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. 请注意，此时页面顶部会存在与顶点和任务相关的链接。

    > [!NOTE]
    > 还可以通过以下方式访问此页：回到“DAG 详细信息”，选择“顶点详细信息”，并选择“映射 1”顶点。
    >
    >

    * “顶点计数器”显示此顶点的计数器信息。
    * “任务”显示此顶点的任务。
    * “任务尝试”显示尝试针对此顶点运行任务的信息。
    * “源和接收器”显示此顶点的数据源和接收器。

      > [!NOTE]
      > 与前一菜单一样，可以滚动“任务”、“任务尝试”、“源和接收器”的列显示，以便显示每个项的详细信息链接。
      >
      >
11. 选择“任务”，并选择名为 **00_000000** 的项。 此链接会显示此任务的“任务详细信息”。 在此屏幕中，可以查看“任务计数器”和“任务尝试”。

    ![任务详细信息](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>后续步骤
既然已了解如何使用 Tez 视图，接下来将详细了解如何[使用 Hive on HDInsight](hadoop/hdinsight-use-hive.md)。

有关 Tez 的更多详细的技术信息，请参阅 [Hortonworks 的 Tez 页](http://hortonworks.com/hadoop/tez/)。
