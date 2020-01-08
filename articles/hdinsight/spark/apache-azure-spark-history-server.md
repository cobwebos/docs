---
title: 使用 Apache Spark 历史记录服务器中的扩展功能调试应用-Azure HDInsight
description: 使用 Apache Spark 历史记录服务器中的扩展功能来调试和诊断 Spark 应用程序-Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 8e0fcdc97475053dcf285b562b28784deffff28c
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552723"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>使用 Apache Spark 历史记录服务器的扩展功能来调试和诊断 Spark 应用程序

本文介绍如何使用 Apache Spark 历史记录服务器的扩展功能来调试和诊断已完成或正在运行的 Spark 应用程序。 该扩展插件包括 "**数据**" 选项卡、"**图形**" 选项卡和 "**诊断**" 选项卡。在 "**数据**" 选项卡上，可以查看 Spark 作业的输入和输出数据。 在 "**关系图**" 选项卡上，您可以检查数据流并重播作业图。 在 "**诊断**" 选项卡上，可以引用**数据倾斜**、**时间偏差**和**执行器使用情况分析**功能。

## <a name="get-access-to-the-spark-history-server"></a>获取对 Spark 历史记录服务器的访问权限

Spark 历史记录服务器是已完成和正在运行的 Spark 应用程序的 web UI。 可以从 Azure 门户或 URL 打开它。

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>从 Azure 门户打开 Spark History Server web UI

1. 从 [Azure 门户](https://portal.azure.com/)打开 Spark 群集。 有关详细信息，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。
2. 从**群集仪表板**中，选择 " **Spark history server**"。 出现提示时，输入 Spark 群集的管理员凭据。

    ![从 Azure 门户启动 Spark 历史记录服务器。](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark History Server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>按 URL 打开 Spark History Server web UI

通过浏览到 `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`打开 Spark 历史记录服务器，其中**CLUSTERNAME**是 Spark 群集的名称。

Spark History Server web UI 可能如下图所示：

![Spark History Server 页。](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>使用 Spark History Server 中的 "数据" 选项卡

选择作业 ID，然后在 "工具" 菜单上选择 "**数据**"，以查看数据视图。

+ 通过选择各个选项卡来查看**输入**、**输出**和**表操作**。

    ![Spark 应用程序的数据选项卡页。](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ 通过选择 "**复制**" 按钮复制所有行。

    ![复制 Spark 应用程序页上的数据。](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 将所有数据保存为。CSV 文件，方法是选择**csv**按钮。

    ![将数据另存为。Spark 应用程序的数据页中的 CSV 文件。](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ 在**搜索**字段中输入关键字，以搜索数据。 搜索结果将立即显示。

    ![在数据上搜索 Spark 应用程序页。](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 选择要对表进行排序的列标题。 选择加号以展开行以显示更多详细信息。 选择减号以折叠行。

    ![Spark 应用程序的数据页上的数据表。](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 通过选择右侧的**部分下载**按钮下载单个文件。 选择的文件将在本地下载。 如果文件不再存在，则会打开一个新选项卡以显示错误消息。

    ![Spark 应用程序的数据页上的数据下载行。](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 通过选择 "**复制完整路径**" 或 "**复制相对路径**" 选项（可从 "下载" 菜单展开）复制完整路径或相对路径。 对于 Azure Data Lake Storage 文件，请选择 "**打开" Azure 存储资源管理器**启动 Azure 存储资源管理器，并在登录后找到该文件夹。

    !["复制 Spark 应用程序的数据" 页上的 "完整路径" 和 "复制相对路径" 选项。](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 如果在单个页面上显示的行过多，请选择要在表底部导航的页码。

    ![Spark 应用程序的数据页上的页码。](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 有关详细信息，请将鼠标悬停在 " **Spark 应用程序的数据**" 旁边或选中问号，以显示工具提示。

    ![从数据 for Spark 应用程序页获取详细信息。](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  若要发送有关问题的反馈，请选择 "**提供我们的反馈**"。

    ![从 "Spark 应用程序的数据" 页提供反馈。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>使用 Spark 历史记录服务器中的 "图形" 选项卡

+ 选择作业 ID，然后在 "工具" 菜单上选择 "**关系图**" 以查看作业图形。 默认情况下，该图将显示所有作业。 使用 "**作业 ID** " 下拉菜单来筛选结果。

    ![Spark 应用程序 & 作业图形 "页上的" 作业 ID "下拉菜单。](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ 默认情况下，选择 "**进度**"。 通过选择 "**显示**" 下拉菜单中的 "**读取**" 或 "**写入**" 来检查数据流。

    ![检查 Spark 应用程序 & 作业图形 "页上的数据流。](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ 每个任务的背景色都对应一个热度地图。

   ![Spark 应用程序 & 作业图形 "页上的热度地图。](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |颜色 |Description |
    |---|---|
    |绿色|作业已成功完成。|
    |Orange|任务失败，但这不会影响作业的最终结果。 这些任务有重复或重试实例，稍后可能会成功。|
    |蓝色|任务正在运行。|
    |白色|任务正在等待运行或已跳过该阶段。|
    |红色|任务失败。|

     ![在 Spark 应用程序 & 作业图形 "页上运行任务。](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     跳过的阶段显示为白色。
    ![在 Spark 应用程序 & 作业图形 "页上跳过的任务。](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Spark 应用程序 & 作业图形 "页上的失败任务。](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > 播放可用于已完成的作业。 选择 "**播放**" 按钮以播放作业。 通过选择 "停止" 按钮随时停止作业。 播放作业时，每个任务都将按颜色显示其状态。 不完整的作业不支持播放。

+ 滚动到作业图形上的 "放大" 或 "缩小"，或选择 "**缩放到合适大小**" 以使其适合屏幕大小。

    ![选择 "缩放以适合 Spark 应用程序 & 作业图形" 页。](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 当任务失败时，将鼠标悬停在图形节点上以查看工具提示，然后选择该阶段以在新页面中打开它。

    ![查看 Spark 应用程序 & 作业图形 "页上的工具提示。](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 在 Spark 应用程序 & 作业图形 "页上，如果任务满足以下条件，则会显示工具提示和小图标：
  + 数据歪斜：数据读取大小 > 此阶段内所有任务的平均数据读取大小 * 2*和*数据读取大小 > 10 MB。
  + 时间偏差：此阶段内所有任务的执行时间 > 平均执行时间 * 2*和*执行时间 > 2 分钟。

    ![Spark 应用程序 & 作业图形 "页上的扭曲任务图标。](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 作业图形节点将显示有关每个阶段的下列信息：
  + ID
  + 名称或说明
  + 任务总数
  + 读取的数据：输入大小和无序读取大小
  + 数据写入：输出大小和无序写入大小的总和
  + 执行时间：第一次尝试开始时间与上次尝试完成时间之间的时间
  + 行计数：输入记录、输出记录、无序读取记录和无序写入记录的总和
  + 进度

    > [!NOTE]  
    > 默认情况下，"作业图形" 节点将显示每个阶段的最后一次尝试（阶段执行时间除外）的信息。 但在播放期间，作业图形节点会显示有关每次尝试的信息。

    > [!NOTE]  
    > 对于数据读取和数据写入大小，我们使用 1MB = 1000 KB = 1000 * 1000 字节。

+ 通过选择 "**提供反馈**" 来发送有关问题的反馈。

    ![Spark 应用程序 & 作业图形 "页上的" 反馈 "选项。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>使用 Spark 历史记录服务器中的 "诊断" 选项卡

选择作业 ID，然后在 "工具" 菜单上选择 "**诊断**"，查看 "作业诊断" 视图。 "**诊断**" 选项卡包括**数据倾斜**、**时间偏差**和**执行器使用情况分析**。

+ 分别选择选项卡，查看**数据倾斜**、**时间偏差**和**执行器使用情况分析**。

    !["诊断" 选项卡中的 "数据倾斜" 选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>数据倾斜

选择 "**数据倾斜**" 选项卡。相应的歪斜任务基于指定的参数显示。

#### <a name="specify-parameters"></a>指定参数

"**指定参数**" 部分显示用于检测数据倾斜的参数。 默认规则是：读取的任务数据超过平均任务数据的三倍，任务数据读取量超过 10 MB。 如果要为歪斜任务定义自己的规则，可以选择参数。 **倾斜的阶段**和**扭曲字符**部分将相应地进行更新。

#### <a name="skewed-stage"></a>歪斜阶段

"**歪斜阶段**" 部分显示具有符合指定条件的歪斜任务的阶段。 如果某个阶段中存在多个歪斜的任务，则 "**歪斜阶段**" 部分只显示最歪斜的任务（即数据倾斜的最大数据）。

!["诊断" 选项卡中的 "数据倾斜" 选项卡的放大视图。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>倾斜图

当您在 "**倾斜阶段**" 表中选择某一行时，**扭曲图**会根据数据读取和执行时间显示更多的任务分布详细信息。 歪斜的任务标记为红色，普通任务标记为蓝色。 出于性能方面的考虑，图表最多可显示100个示例任务。 任务详细信息显示在右下窗格中。

![Spark UI 中阶段10的扭曲图。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>时间倾斜

“时间倾斜”选项卡根据任务执行时间显示倾斜任务。

#### <a name="specify-parameters"></a>指定参数

"**指定参数**" 部分显示用于检测时间偏差的参数。 默认规则是：任务执行时间大于平均执行时间的三倍，任务执行时间大于30秒。 可以按需更改相关参数。 **倾斜阶段**和**扭曲图**显示相应的阶段和任务信息，就像在 "**数据倾斜**" 选项卡中一样。

如果选择 "**时间偏差**"，则根据在 "**指定参数**" 部分中设置的参数，在 "**倾斜阶段**" 部分显示筛选结果。 当你在 "**倾斜阶段**" 部分中选择一项时，相应的图表将在第三部分中进行绘制，并且任务详细信息显示在右下窗格中。

!["诊断" 选项卡中的 "时间偏差" 选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>执行器使用情况分析关系图

**执行器使用情况图**显示作业的实际执行器分配和运行状态。  

选择 "**执行程序使用情况分析**" 时，会草拟四条有关执行器使用情况的不同曲线：已**分配**的执行程序、**运行**执行器、**空闲**执行器和**最大执行器实例** 添加的每个执行器或**执行器删除**事件将增加或减少分配的**执行**程序。 您可以在 "**作业**" 选项卡中检查**事件时间线**，以便进行更多比较。

!["诊断" 选项卡中的 "执行程序使用情况分析" 选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

选择颜色图标以选择或取消选择所有草稿中的相应内容。

 ![在 "执行程序使用情况分析" 选项卡中选择该图表。](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>常见问题

### <a name="how-do-i-revert-to-the-community-version"></a>如何实现还原到社区版本？

若要恢复到社区版本，请执行以下步骤。

1. 在 Ambari 中打开群集。
1. 导航到**custom-spark2-defaults** > **配置**。
1. 选择 "**自定义 custom-spark2-defaults**"。
1. 选择 "**添加属性 ...** "。
1. 添加**spark。 enabled = false**，然后保存它。
1. 现在，该属性设置为 **false**。
1. 选择“保存”以保存配置。

    ![关闭 Apache Ambari 中的功能。](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 在左侧面板中选择 " **custom-spark2-defaults** "。 然后，在 "**摘要**" 选项卡上，选择**custom-spark2-defaults History Server**。

    ![Apache Ambari 中的 "摘要" 视图。](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. 若要重新启动 Spark History Server，请选择**Custom-spark2-defaults History server**右侧的 "**已启动**" 按钮，然后从下拉菜单中选择 "**重新启动**"。

    ![在 Apache Ambari 中重启 Spark 历史记录服务器。](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. 刷新 Spark History Server web UI。 它将恢复到社区版本。

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>如何实现上传 Spark History Server 事件以将其报告为问题？

如果在 Spark History Server 中遇到错误，请执行以下步骤来报告该事件。

1. 在 Spark History Server web UI 中选择 "**下载**" 以下载事件。

    ![在 Spark 历史记录服务器 UI 中下载事件。](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 选择 "通过**Spark 应用程序 & 作业图形**" 页面**提供反馈**。

    ![& 作业图形 "页上提供 Spark 应用程序反馈](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供错误的标题和说明。 然后，将 .zip 文件拖到 "编辑" 字段，然后选择 "**提交新问题**"。

    ![上载并提交新问题。](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>如何实现升级修补程序方案中的 .jar 文件？

如果要使用修补程序进行升级，请使用以下脚本（将 `spark-enhancement.jar*`升级）。

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

#### <a name="use-the-bash-file-from-the-azure-portal"></a>使用 Azure 门户的 bash 文件

1. 启动[Azure 门户](https://ms.portal.azure.com)，然后选择群集。
2. 使用以下参数完成[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)。

    |属性 |值 |
    |---|---|
    |脚本类型|- Custom|
    |名称|UpgradeJar|
    |Bash 脚本 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |节点类型|Head，辅助角色|
    |参数|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure 门户提交脚本操作](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>已知问题

+ 目前，Spark 历史记录服务器仅适用于 Spark 2.3 和2.4。

+ 使用 RDD 的输入和输出数据不会显示在 "**数据**" 选项卡中。

## <a name="next-steps"></a>后续步骤

+ [管理 HDInsight 上 Apache Spark 群集的资源](apache-spark-resource-manager.md)
+ [配置 Apache Spark 设置](apache-spark-settings.md)

## <a name="feedback"></a>反馈

如果你有任何反馈，或使用此工具时遇到任何问题，请发送电子邮件至（[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)）。