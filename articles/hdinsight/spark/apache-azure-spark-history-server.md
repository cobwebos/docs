---
title: 使用 Apache Spark History Server 中的扩展功能调试应用 - Azure HDInsight
description: 使用 Apache Spark History Server 中的扩展功能调试和诊断 Spark 应用程序 - Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: d8dd9aaeaadf13fa48577cf2853e7bcf58badb41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86079286"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>使用 Apache Spark History Server 的扩展功能调试和诊断 Spark 应用程序

本文介绍如何使用 Apache Spark History Server 的扩展功能来调试和诊断已完成的或正在运行的 Spark 应用程序。 扩展包括“数据”选项卡、“图形”选项卡和“诊断”选项卡。    在“数据”选项卡中，可以检查 Spark 作业的输入和输出数据。  在“图形”选项卡中，可以检查数据流和重放作业图形。  在“诊断”选项卡中，可以参考“数据倾斜”、“时间倾斜”和“执行程序使用情况分析”功能。    

## <a name="get-access-to-the-spark-history-server"></a>访问 Spark History Server

Spark History Server 是已完成和正在运行的 Spark 应用程序的 Web UI。 可以从 Azure 门户或 URL 打开它。

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>从 Azure 门户打开 Spark History Server Web UI

1. 从 [Azure 门户](https://portal.azure.com/)打开 Spark 群集。 有关详细信息，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。
2. 在**群集仪表板**中选择“Spark History Server”。  出现提示时，输入 Spark 群集的管理员凭据。

    ![从 Azure 门户启动 Spark History Server。](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark History Server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>按 URL 打开 Spark History Server web UI

通过浏览到打开 Spark 历史记录服务器 `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` ，其中 **CLUSTERNAME** 是 Spark 群集的名称。

Spark History Server Web UI 可能类似于下图：

![Spark History Server 页。](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>使用 Spark History Server 中的“数据”选项卡

选择作业 ID，然后选择工具菜单中的“数据”查看数据视图。 

+ 选择“输入”、“输出”和“表操作”选项卡查看其中的信息。   

    ![“Spark 应用程序数据”页上的“数据”选项卡。](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ 选择“复制”按钮复制所有行。 

    ![复制 Spark 应用程序页上的数据。](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 选择“csv”按钮将所有数据保存为 .CSV 文件。 

    ![在“Spark 应用程序数据”页中将数据保存为 .CSV 文件。](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ 在“搜索”字段中输入关键字来搜索数据。  搜索结果会立即显示。

    ![在“Spark 应用程序数据”页上搜索数据。](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 选择列标题以将表排序。 选择加号图标可展开某行，以显示更多详细信息。 选择减号图标可折叠某行。

    ![“Spark 应用程序数据”页上的数据表。](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 选择右侧的“部分下载”按钮下载单个文件。  选定的文件将下载到本地。 如果该文件不存在，则打开一个新选项卡以显示错误消息。

    ![“Spark 应用程序数据”页上的数据下载行。](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 选择下载菜单中展开的“复制完整路径”或“复制相对路径”选项，来复制完整路径或相对路径。   对于 Azure Data Lake Storage 文件，请选择“在 Azure 存储资源管理器中打开”以启动 Azure 存储资源管理器，并在登录后定位到相应的文件夹。 

    ![“Spark 应用程序数据”页上的“复制完整路径”和“复制相对路径”选项。](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 如果一个页面中显示的行数过多，选择表底部的页码可以浏览不同的页面。

    ![“Spark 应用程序数据”页上的页码。](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 如需查看更多信息，请将鼠标悬停在“Spark 应用程序数据”旁边的问号上以显示工具提示。 

    ![从“Spark 应用程序数据”页获取更多信息。](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  若要发送有关问题的反馈，请选择“向我们提供反馈”。 

    ![从“Spark 应用程序数据”页提供反馈。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>使用 Spark History Server 中的“图形”选项卡

+ 选择作业 ID，然后选择工具菜单中的“图形”查看作业图形。  默认情况下，图形将显示所有作业。 使用“作业 ID”下拉菜单筛选结果。 

    ![“Spark 应用程序和作业图形”页上的“作业 ID”下拉菜单。](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ 默认已选择“进度”。  在“显示”下拉菜单中选择“已读取”或“已写入”来检查数据流。   

    ![在“Spark 应用程序和作业图形”页上检查数据流。](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ 每个任务的背景色对应于一个热度地图。

   ![“Spark 应用程序和作业图形”页上的热度地图。](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |颜色 |说明 |
    |---|---|
    |绿色|作业已成功完成。|
    |橙色|任务失败，但不影响作业的最终结果。 这些任务包括可能稍后会成功的重复或重试实例。|
    |蓝色|任务正在运行。|
    |白色|任务正在等待运行，或已跳过执行阶段。|
    |红色|任务已失败。|

     ![在“Spark 应用程序和作业图形”页上运行任务。](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     跳过的阶段以白色显示。
    ![“Spark 应用程序和作业图形”页上已跳过的任务。](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![“Spark 应用程序和作业图形”页上失败的任务。](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > 对于已完成的作业，可以使用播放。 选择“播放”按钮播放作业。  随时可以选择停止按钮来停止作业。 播放作业时，每个任务将以各种颜色显示其状态。 未完成的作业不支持播放。

+ 滚动鼠标滚轮以放大或缩小作业图形，或者选择“缩放到合适大小”使图形适合屏幕大小。 

    ![在“Spark 应用程序和作业图形”页上选择“缩放到合适大小”。](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 任务失败时，请将鼠标悬停在图形节点上以查看工具提示，然后选择阶段以在新页中将其打开。

    ![在“Spark 应用程序和作业图形”页上查看工具提示。](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 在“Spark 应用程序和作业图形”页上，如果任务满足以下条件，则阶段会显示工具提示和小图标：
  + 数据倾斜：数据读取大小 > 此阶段内所有任务的平均数据读取大小 * 2 且数据读取大小 > 10 MB。 
  + 时间倾斜：执行时间 > 此阶段内所有任务的平均执行时间 * 2 且执行时间 > 2 分钟。 

    ![“Spark 应用程序和作业图形”页上的倾斜任务图标。](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 作业图形节点将显示有关每个阶段的以下信息：
  + ID
  + 名称或说明
  + 任务总数
  + 读取的数据：输入大小和随机读取大小之和
  + 写入的数据：输出大小和随机写入大小之和
  + 执行时间：从第一次尝试的开始时间到最后一次尝试的完成时间的时间和
  + 行计数：输入记录、输出记录、随机读取记录和随机写入记录的总和
  + 进度

    > [!NOTE]  
    > 默认情况下，作业图形节点显示每个阶段的最后一次尝试的信息（阶段执行时间除外）。 但在播放期间，作业图形节点将显示有关每次尝试的信息。

    > [!NOTE]  
    > 对于读取和写入的数据大小，我们以 1 MB = 1000 KB = 1000 * 1000 字节为单位。

+ 选择“向我们提供反馈”发送有关问题的反馈。 

    ![“Spark 应用程序和作业图形”页上的反馈选项。](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>使用 Spark History Server 中的“诊断”选项卡

选择作业 ID，然后选择工具菜单中的“诊断”查看作业诊断视图。  “诊断”选项卡包括“数据倾斜”、“时间倾斜”和“执行程序使用情况分析”。    

+ 通过选择相应的选项卡来查看“数据偏斜”、“时间偏斜”和“执行程序使用情况分析”。   

    ![“诊断”选项卡中的“数据倾斜”选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>数据倾斜

选择“数据倾斜”选项卡。  相应的倾斜任务根据指定的参数显示。

#### <a name="specify-parameters"></a>指定参数

“指定参数”部分显示用于检测数据倾斜的参数。  默认规则为：任务数据读取大于 3 倍任务数据读取平均值，且任务数据读取大于 10 MB。 若要为倾斜任务定义自己的规则，可以选择参数。 “倾斜阶段”和“倾斜图表”部分会相应地更新。  

#### <a name="skewed-stage"></a>倾斜阶段

“倾斜阶段”部分显示存在符合指定条件的倾斜任务的阶段。  如果某个阶段有多个倾斜任务，则“倾斜阶段”部分仅显示最倾斜的任务（即，数据倾斜对应的最大数据）。 

![“诊断”选项卡中的“数据倾斜”选项卡的较大视图。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>倾斜图表

在“倾斜阶段”表中选择某行时，“倾斜图表”会根据数据读取和执行时间显示更多任务分布详细信息。   倾斜任务标记为红色，普通任务标记为蓝色。 出于性能方面的考虑，图表最多显示 100 个示例任务。 任务详细信息显示在右下窗格中。

![Spark UI 中“阶段 10”的倾斜图表。](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>时间倾斜

“时间倾斜”  选项卡根据任务执行时间显示倾斜任务。

#### <a name="specify-parameters"></a>指定参数

“指定参数”部分显示用于检测时间倾斜的参数。  默认规则为：任务执行时间长于平均执行时间的 3 倍，且任务执行时间长于 30 秒。 可以按需更改相关参数。 与“数据倾斜”选项卡一样，“倾斜阶段”和“倾斜图表”显示相应的阶段和任务信息。   

如果选择“时间倾斜”，将会根据“指定参数”部分设置的参数，在“倾斜阶段”部分显示筛选的结果。    在“倾斜阶段”部分选择一项时，将在第三个部分绘制相应的图表，任务详细信息显示在右下窗格中。 

![“诊断”选项卡中的“时间倾斜”选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>执行程序使用情况分析图形

“执行程序使用情况图形”显示作业的实际执行程序分配和运行状态。   

选择“执行程序使用情况分析”时，会绘制有关执行程序使用情况的四条不同曲线：  “分配的执行程序”、“正在运行的执行程序”、“空闲执行程序”和“最大执行程序实例数目”。     每个“已添加执行程序”或“已删除执行程序”事件都将增加或减少分配的执行程序。   可以检查“作业”选项卡中的“事件时间线”进行更多比较。  

![“诊断”选项卡中的“执行程序使用情况分析”选项卡。](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

选择彩色图标即可选中或取消选中所有草稿中的相应内容。

 ![在“执行程序使用情况分析”选项卡中选择图表。](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>常见问题

### <a name="how-do-i-revert-to-the-community-version"></a>如何还原到社区版？

若要还原到社区版，请执行以下步骤。

1. 在 Ambari 中打开群集。
1. 导航到“Spark2”   > “配置”  。
1. 选择“自定义 spark2-defaults”。 
1. 选择“添加属性...”。 
1. 添加 **spark.ui.enhancement.enabled=false** 并保存。
1. 现在，该属性设置为 **false**。
1. 选择“保存”  以保存配置。

    ![在 Apache Ambari 中禁用功能。](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 在左侧面板中选择“Spark2”。  然后在“摘要”选项卡中选择“Spark2 History Server”。  

    ![Apache Ambari 中的摘要视图。](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. 若要重启 Spark History Server，请选择“Spark2 History Server”右侧的“已启动”按钮，然后从下拉菜单中选择“重启”。   

    ![在 Apache Ambari 中重启 Spark History Server。](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. 刷新 Spark History Server Web UI。 该服务将还原到社区版。

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>如何上传 Spark History Server 事件以将其报告为问题？

如果在 Spark History Server 中遇到错误，请执行以下步骤来报告事件。

1. 在 Spark History Server Web UI 中选择“下载”以下载事件。 

    ![在 Spark History Server UI 中下载事件。](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 在“Spark 应用程序和作业图形”页中选择“向我们提供反馈”。  

    ![“Spark 应用程序和作业图形”页上的“向我们提供反馈”](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供相关错误的标题和说明。 然后，将 .zip 文件拖放到编辑字段中，并选择“提交新问题”。 

    ![上传和提交新问题。](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>如何在修补方案中升级 .jar 文件？

若要使用修补程序进行升级，请使用以下脚本，该脚本会升级 `spark-enhancement.jar*`。

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

#### <a name="use-the-bash-file-from-the-azure-portal"></a>在 Azure 门户中使用 bash 文件

1. 启动 [Azure 门户](https://ms.portal.azure.com)，然后选择你的群集。
2. 使用以下参数完成 [脚本操作](../hdinsight-hadoop-customize-cluster-linux.md) 。

    |属性 |Value |
    |---|---|
    |脚本类型|- Custom|
    |名称|UpgradeJar|
    |Bash 脚本 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |节点类型|头节点、工作器节点|
    |parameters|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![在 Azure 门户中提交脚本操作](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>已知问题

+ 目前，Spark History Server 仅适用于 Spark 2.3 和2.4。

+ 使用 RDD 的输入和输出数据不会显示在“数据”选项卡中。 

## <a name="next-steps"></a>后续步骤

+ [管理 HDInsight 上 Apache Spark 群集的资源](apache-spark-resource-manager.md)
+ [配置 Apache Spark 设置](apache-spark-settings.md)

## <a name="suggestions"></a>建议

如有任何反馈，或使用此工具时遇到任何问题，请向 [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) 发送电子邮件。
