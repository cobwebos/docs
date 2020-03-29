---
title: 使用 Apache Spark 历史记录服务器中的扩展功能调试应用 - Azure HDInsight
description: 使用 Apache Spark 历史记录服务器中的扩展功能来调试和诊断 Spark 应用程序 - Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548928"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>使用 Apache Spark 历史记录服务器的扩展功能调试和诊断 Spark 应用程序

本文介绍如何使用 Apache Spark 历史记录服务器的扩展功能来调试和诊断已完成或运行的 Spark 应用程序。 扩展名包括 **"数据"** 选项卡、**图形**选项卡和**诊断**选项卡。在"**数据"** 选项卡上，可以检查 Spark 作业的输入和输出数据。 在 **"图形"** 选项卡上，您可以检查数据流并重播作业图。 在 **"诊断"** 选项卡上，您可以参考 **"数据倾斜**"、**时间偏差**和执行**器使用情况分析**功能。

## <a name="get-access-to-the-spark-history-server"></a>访问 Spark 历史记录服务器

Spark 历史记录服务器是已完成和运行 Spark 应用程序的 Web UI。 可以从 Azure 门户或 URL 打开它。

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>从 Azure 门户打开 Spark 历史记录服务器 Web UI

1. 从 [Azure 门户](https://portal.azure.com/)打开 Spark 群集。 有关详细信息，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。
2. 在**群集仪表板**中选择“Spark History Server”。**** 出现提示时，输入 Spark 群集的管理员凭据。

    ![从 Azure 门户启动 Spark 历史记录服务器。](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark History Server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>按 URL 打开 Spark 历史记录服务器 Web UI

通过浏览打开`https://CLUSTERNAME.azurehdinsight.net/sparkhistory`Spark 历史记录服务器，其中**CLUSTERNAME**是 Spark 群集的名称。

Spark 历史记录服务器 Web UI 可能类似于此映像：

!["火花历史记录服务器"页。](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>使用 Spark 历史记录服务器中的数据选项卡

选择作业 ID，然后选择工具菜单上**的数据**以查看数据视图。

+ 通过选择单个选项卡来查看**输入**、**输出**和**表操作**。

    !["火花应用程序的数据"页上的数据选项卡。](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ 通过选择 **"复制**"按钮复制所有行。

    ![复制 Spark 应用程序页上的数据。](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 将所有数据另存为 。通过选择**csv**按钮来 CSV 文件。

    ![将数据另存为 。来自"火花应用程序数据"页的 CSV 文件。](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ 通过在 **"搜索"** 字段中输入关键字来搜索数据。 搜索结果将立即显示。

    ![在"火花应用程序数据"页上搜索数据。](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 选择列标题对表进行排序。 选择加号以展开行以显示更多详细信息。 选择减号以折叠行。

    ![Spark 应用程序数据页上的数据表。](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 通过选择右侧的 **"部分下载"** 按钮下载单个文件。 所选文件将在本地下载。 如果该文件不再存在，这将打开一个新选项卡以显示错误消息。

    ![Spark 应用程序数据页上的数据下载行。](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 通过选择"**复制完整路径**"或 **"复制相对路径**"选项（从下载菜单展开）复制完整路径或相对路径。 对于 Azure 数据湖存储文件，请在**Azure 存储资源管理器中选择"打开**"以启动 Azure 存储资源管理器，并在登录后找到文件夹。

    ![在 Spark 应用程序的数据页面上复制完整路径和复制相对路径选项。](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 如果单个页面上显示的行太多，请选择表底部的页码以进行导航。

    !["火花应用程序数据"页上的页码。](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 有关详细信息，请将鼠标悬停在**Spark 应用程序的数据**旁边或选择问号以显示工具提示。

    ![从"火花应用数据"页面获取更多信息。](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  要发送有关问题的反馈，请选择"**向我们提供反馈**"。

    ![提供来自"火花应用数据"页面的反馈。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>使用 Spark 历史记录服务器中的图形选项卡

+ 选择作业 ID，然后在工具菜单上选择 **"图形**"以查看作业图。 默认情况下，图形将显示所有作业。 使用**作业 ID**下拉菜单筛选结果。

    !["火花应用程序"&作业图形页上的工作 ID 下拉菜单。](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ 默认情况下选择**进度**。 通过在 **"显示"** 下拉菜单中选择 **"读取**"或 **"写入"** 来检查数据流。

    ![检查 Spark 应用程序&作业图形页上的数据流。](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ 每个任务的背景颜色对应于热图。

   !["火花应用程序"&作业图页上的热图。](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Color |描述 |
    |---|---|
    |绿色|作业已成功完成。|
    |橙色|任务失败，但这不会影响作业的最终结果。 这些任务具有重复或重试实例，以后可能会成功。|
    |蓝色|任务正在运行。|
    |白色|任务正在等待运行或已跳过该阶段。|
    |Red|任务失败。|

     ![在"火花应用程序"上运行任务&作业图形页。](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     跳过的阶段以白色显示。
    ![Spark 应用程序&作业图形页上跳过的任务。](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Spark 应用程序&作业图形页上的失败任务。](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > 回放可用于已完成的工作。 选择 **"播放"** 按钮以播放作业。 通过选择停止按钮，随时停止作业。 重播作业时，每个任务将按颜色显示其状态。 不完全作业不支持播放。

+ 滚动以放大或缩小作业图，或选择 **"缩放以使其适合**"以使其适合屏幕。

    ![选择"缩放"以适合"火花应用程序&作业图"页。](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 当任务失败时，将鼠标悬停在图形节点上以查看工具提示，然后选择阶段在新页面中打开它。

    ![在"火花应用程序&作业图页上查看工具提示。](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 在 Spark 应用程序&作业图页上，如果任务满足以下条件，这些阶段将显示工具提示和小图标：
  + 数据偏斜：数据读取大小>此阶段内所有任务的平均数据读取大小 = 2*和*数据读取大小> 10 MB。
  + 时间偏差：执行时间>此阶段内所有任务的平均执行时间 = *2，* 执行时间> 2 分钟。

    ![Spark 应用程序上的倾斜任务图标&作业图页。](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 作业图节点将显示有关每个阶段的以下信息：
  + ID
  + 名称或说明
  + 任务总数
  + 数据读取：输入大小和随机读取大小的总和
  + 数据写入：输出大小和随机写入大小的总和
  + 执行时间：第一次尝试的开始时间和上次尝试的完成时间之间的时间
  + 行计数：输入记录、输出记录、随机读取记录和随机写入记录的总和
  + 进度

    > [!NOTE]  
    > 默认情况下，作业图形节点将显示每个阶段上次尝试的信息（阶段执行时间除外）。 但在播放过程中，作业图形节点将显示有关每次尝试的信息。

    > [!NOTE]  
    > 对于数据读取和数据写入大小，我们使用 1MB = 1000 KB = 1000 = 1000 字节。

+ 通过选择 **"向我们提供反馈**"发送有关问题的反馈。

    ![Spark 应用程序上的反馈选项&作业图页上。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>使用 Spark 历史记录服务器中的诊断选项卡

选择作业 ID，然后在工具菜单上选择 **"诊断"** 以查看作业诊断视图。 诊断**选项卡**包括**数据倾斜**、**时间偏差**和执行**器使用情况分析**。

+ 通过选择相应的选项卡来查看“数据偏斜”、“时间偏斜”和“执行程序使用情况分析”。************

    ![诊断选项卡中的"数据倾斜"选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>数据倾斜

选择"**数据倾斜"** 选项卡。基于指定参数显示相应的倾斜任务。

#### <a name="specify-parameters"></a>指定参数

"**指定参数"** 部分显示用于检测数据偏差的参数。 默认规则是：读取的任务数据大于平均任务数据读取的三倍，并且任务数据读取超过 10 MB。 如果要为倾斜任务定义自己的规则，可以选择参数。 **倾斜阶段**和**倾斜图**部分将相应地更新。

#### <a name="skewed-stage"></a>倾斜阶段

**"倾斜阶段"** 部分显示具有扭曲任务以达到指定条件的各个阶段。 如果一个阶段中有多个偏斜任务，**则"倾斜阶段"** 部分仅显示最偏斜的任务（即数据偏斜的最大数据）。

!["诊断"选项卡中"数据倾斜"选项卡的较大视图。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>倾斜图表

在 **"倾斜阶段"** 表中选择一行时，**偏斜图**会根据数据读取和执行时间显示更多任务分布详细信息。 倾斜的任务以红色标记，正常任务以蓝色标记。 出于性能考虑，图表最多显示 100 个示例任务。 任务详细信息显示在右下角的面板中。

![Spark UI 中阶段 10 的倾斜图。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>时间倾斜

“时间倾斜”**** 选项卡根据任务执行时间显示倾斜任务。

#### <a name="specify-parameters"></a>指定参数

"**指定参数"** 部分显示用于检测时间偏斜的参数。 默认规则是：任务执行时间大于平均执行时间的三倍，任务执行时间大于 30 秒。 可以按需更改相关参数。 **倾斜阶段**和**倾斜图**显示相应的阶段和任务信息，就像在 **"数据倾斜"** 选项卡中一样。

选择**时间倾斜**时，根据 **"指定参数"** 部分中设置的参数，筛选的结果将显示在 **"倾斜阶段"** 部分中。 当您在 **"倾斜阶段"** 部分中选择一个项目时，相应的图表将在第三部分中绘制，任务详细信息显示在右下角的面板中。

!["诊断"选项卡中的"时间倾斜"选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>执行器使用情况分析图

**执行器使用情况图**显示作业的实际执行器分配和运行状态。  

当您选择**执行器使用分析**时，将起草关于执行器用法的四条不同曲线：**已分配执行器**、**正在运行的执行器**、**空闲执行器**和**最大执行器实例**。 **每个添加执行器**或删除**执行者**的事件将增加或减少已分配执行器。 您可以在 **"作业"** 选项卡中查看**事件时间线**以进行更多比较。

![诊断选项卡中的"执行器使用情况分析"选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

选择彩色图标即可选中或取消选中所有草稿中的相应内容。

 ![在"执行器使用情况分析"选项卡中选择图表。](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>FAQ

### <a name="how-do-i-revert-to-the-community-version"></a>如何恢复社区版本？

要还原到社区版本，请执行以下步骤。

1. 打开安巴里的群集。
1. 导航到**Spark2** > **配置**。
1. 选择**自定义 spark2 默认值**。
1. 选择 **"添加属性..."。**
1. 添加**spark.ui.增强.启用\false，** 然后保存它。
1. 现在，该属性设置为 **false**。
1. 选择“保存”**** 以保存配置。

    ![关闭阿帕奇安巴里中的功能。](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 在左侧面板中选择**Spark2。** 然后，在 **"摘要"** 选项卡上，选择**Spark2 历史记录服务器**。

    ![阿帕奇·安巴里中的摘要视图。](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. 要重新启动 Spark 历史记录服务器，请选择**Spark2 历史记录服务器**右侧的 **"已启动"** 按钮，然后从下拉菜单中选择 **"重新启动**"。

    ![重新启动阿帕奇 Ambari 中的火花历史记录服务器。](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. 刷新 Spark 历史记录服务器 Web UI。 它将恢复到社区版本。

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>如何上传 Spark 历史记录服务器事件以将其报告为问题？

如果在 Spark 历史记录服务器中遇到错误，执行以下步骤来报告事件。

1. 通过在 Spark 历史记录服务器 Web UI 中选择 **"下载**"来下载事件。

    ![在 Spark 历史记录服务器 UI 中下载事件。](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 选择"从 Spark**应用程序&作业图**页面**向我们提供反馈**。

    ![在 Spark 应用程序&作业图页上提供反馈](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供错误的标题和说明。 然后，将 .zip 文件拖动到编辑字段，然后选择 **"提交新问题**"。

    ![上传并提交新问题。](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>如何在修补程序方案中升级 .jar 文件？

如果要使用修补程序进行升级，请使用以下脚本，这将升级`spark-enhancement.jar*`。

**upgrade_spark_enhancement.sh**：

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>使用情况

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>示例

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>使用 Azure 门户中的 bash 文件

1. 启动[Azure 门户](https://ms.portal.azure.com)，然后选择群集。
2. 使用以下参数完成[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)。

    |properties |“值” |
    |---|---|
    |脚本类型|- Custom|
    |“属性”|UpgradeJar|
    |Bash 脚本 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |节点类型|头节点、工作器节点|
    |参数|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![在 Azure 门户中提交脚本操作](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>已知问题

+ 目前，Spark 历史记录服务器仅适用于 Spark 2.3 和 2.4。

+ 使用 RDD 的输入和输出数据不会显示在 **"数据"** 选项卡中。

## <a name="next-steps"></a>后续步骤

+ [管理 HDInsight 上 Apache Spark 群集的资源](apache-spark-resource-manager.md)
+ [配置 Apache Spark 设置](apache-spark-settings.md)

## <a name="feedback"></a>反馈

如果您在使用此工具时有任何反馈或遇到任何问题，请发送电子邮件至 （[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)。
