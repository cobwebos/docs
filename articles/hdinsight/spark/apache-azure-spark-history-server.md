---
title: 用于调试应用的扩展 Spark 历史记录服务器-Azure HDInsight
description: 使用扩展的 Spark History Server 调试和诊断 Spark 应用程序 - Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 7e9ab0e41086a4c9478f95c5a56754640feeab4e
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561833"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>使用扩展的 Apache Spark History Server 调试和诊断 Apache Spark 应用程序

本文提供有关如何使用扩展的 Apache Spark History Server 来调试和诊断已完成的和正在运行的 Spark 应用程序的指导。 该扩展插件包括数据选项卡和图形选项卡和诊断选项卡。在 "**数据**" 选项卡上，用户可以检查 Spark 作业的输入和输出数据。 在“图形”选项卡中，用户可以检查数据流和重播作业图形。 在 "**诊断**" 选项卡上，用户可以引用**数据倾斜**、**时间偏差**和**执行器使用情况分析**。

## <a name="get-access-to-apache-spark-history-server"></a>获取 Apache Spark History Server 的访问权限

Apache Spark History Server 是已完成的和正在运行的 Spark 应用程序的 Web UI。

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>从 Azure 门户打开 Apache Spark History Server Web UI

1. 从 [Azure 门户](https://portal.azure.com/)打开 Spark 群集。 有关详细信息，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。
2. 从**群集仪表板**中，选择 " **Spark history server**"。 出现提示时，输入 Spark 群集的管理员凭据。

    ![门户启动 Spark 历史记录服务器](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark History Server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>通过 URL 打开 Spark History Server Web UI

通过浏览到 `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` （其中 CLUSTERNAME 是 Spark 群集的名称）打开 Spark 历史记录服务器。

Spark History Server web UI 可能类似于：

![HDInsight Spark History Server](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Spark History Server 中的“数据”选项卡

选择 "作业 ID"，然后在 "工具" 菜单上选择 "**数据**" 以获取数据视图。

+ 通过单独选择选项卡，查看**输入**、**输出**和**表操作**。

    ![Spark 应用程序选项卡的数据](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ 通过选择 "**复制**" 按钮复制所有行。

    ![Spark 应用程序副本的数据](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 通过选择 " **csv**" 按钮将所有数据保存为 csv 文件。

    ![Spark 应用程序保存数据](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ 在“搜索”字段中输入关键字进行搜索，搜索结果会立即显示。

    ![Spark 应用程序搜索的数据](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 选择要对表进行排序的列标题，选择加号以展开行以显示更多详细信息，或选择减号以折叠行。

    ![Spark 应用程序表的数据](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 通过选择右侧的 "按钮**部分下载**" 下载单个文件，然后将所选文件下载到本地; 如果文件不再存在，则将打开一个新选项卡以显示错误消息。

    ![Spark 应用程序下载行的数据](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 选择下载菜单中展开的“复制完整路径”或“复制相对路径”，来复制完整路径或相对路径。 对于 azure data lake 存储文件，**在 Azure 存储资源管理器中打开**将启动 Azure 存储资源管理器，并在登录时定位到该文件夹。

    ![Spark 应用程序复制路径的数据](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 如果要在一页中显示的行过多，请选择表下的数字来导航页面。

    ![Spark 应用程序页面的数据](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 将鼠标悬停在数据旁的问号以显示工具提示，或选择问号以获取详细信息。

    ![Spark 应用程序的数据详细信息](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ 单击“向我们提供反馈”发送问题反馈。

    ![Spark graph 再次向我们提供反馈](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark History Server 中的“图形”选项卡

选择作业 ID，然后单击工具菜单中的“图形”获取作业图形视图。

+ 通过生成的作业图形查看作业的概述。

+ 默认情况下，图形中会显示所有作业，可按“作业 ID”筛选作业。

    ![Spark 应用程序和作业图形作业 ID](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ 默认已选择“进度”，用户可以在“显示”下拉列表中选择“已读取/已写入”来检查数据流。

    ![Spark 应用程序和作业关系图显示](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    彩色图形节点视图，其中显示了热度地图。

    ![Spark 应用程序和作业图形热度地图](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ 通过选择 "**播放**" 按钮播放作业，并通过选择 "停止" 按钮随时停止。 彩色任务视图，其中显示了播放时的不同状态：

    |颜色 |描述 |
    |---|---|
    |绿色|已成功完成作业。|
    |Orange|失败但不影响作业最终结果的任务实例。 这些任务具有稍后可能成功的重复实例或重试实例。|
    |蓝色|任务正在运行。|
    |白色|任务正在等待运行，或已跳过执行阶段。|
    |红色|任务已失败。|

    ![Spark 应用程序和作业图形颜色示例，运行](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    跳过的阶段显示为白色。
    ![Spark 应用程序和作业图形颜色示例，请跳过](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Spark 应用程序和作业图形颜色示例，失败](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > 允许播放每个作业。 不完整的作业不支持播放。

+ 滚动鼠标滚轮以缩小/放大作业图形，或者单击“缩放到合适大小”使图形适合屏幕大小。

    ![Spark 应用程序和作业图形缩放到合适大小](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 如果存在失败的任务，将鼠标悬停在图形节点可查看工具提示；单击阶段可打开阶段页。

    ![Spark 应用程序和作业图形工具提示](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 在作业图形选项卡中，如果阶段的任务满足以下条件，则会显示该阶段的工具提示和小图标：
  + 数据倾斜：数据读取大小 > 此阶段所有任务的平均数据读取大小 * 2 且数据读取大小 > 10 MB。
  + 时间倾斜：执行时间 > 此阶段所有任务的平均执行时间 * 2 且执行时间 > 2 分钟。

    ![Spark 应用程序和作业关系图倾斜图标](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 作业图形节点显示每个阶段的以下信息：
  + ID。
  + 名称或说明。
  + 任务总数。
  + 读取的数据：输入大小和随机读取大小之和。
  + 写入的数据：输出大小和随机写入大小之和。
  + 执行时间：第一次尝试的开始时间与最后一次尝试的完成时间的间隔时间。
  + 行计数：输入记录数、输出记录数、随机读取记录数和随机写入记录数之和。
  + 进度。

    > [!NOTE]  
    > 默认情况下，作业图形节点显示每个阶段的最后一次尝试的信息（阶段执行时间除外），但在播放期间，图形节点显示每次尝试的信息。

    > [!NOTE]  
    > 对于读取和写入的数据大小，我们以 1 MB = 1000 KB = 1000 * 1000 字节为单位。

+ 通过选择 "**提供反馈**"，发送包含问题的反馈。

    ![Spark 应用程序和作业图形反馈](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark History Server 中的“诊断”选项卡

选择 "作业 ID"，然后在 "工具" 菜单上选择 "**诊断**" 以获取作业诊断视图。 “诊断”选项卡包括“数据偏斜”、“时间偏斜”和“执行程序使用情况分析”。

+ 分别选择选项卡，查看**数据倾斜**、**时间偏差**和**执行器使用情况分析**。

    ![再次 SparkUI 诊断数据扭曲选项卡](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>数据倾斜

选择 "**数据倾斜**" 选项卡，根据指定的参数显示相应的歪斜任务。

+ **指定参数**-第一部分显示用于检测数据歪斜的参数。 内置规则是：任务数据读取大于平均任务数据的三倍，读取的任务数据超过 10 MB。 如需针对倾斜的任务定义自己的规则，可以选择参数，然后“倾斜的阶段”和“倾斜字符型”部分会相应地进行刷新。

+ **歪斜阶段**-第二部分显示阶段，其中的任务符合上面指定的条件。 如果一个阶段中存在多个歪斜的任务，则 "倾斜的阶段" 表只显示最扭曲的任务（例如，数据偏斜的最大数据）。

    ![sparkui 诊断数据倾斜选项卡](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **倾斜图表** - 当倾斜阶段表中的某一行处于选中状态时，倾斜图表会根据数据读取和执行时间显示更多任务分发详细信息。 倾斜的任务标记为红色，正常任务标记为蓝色。 出于性能考虑，此图表最多只显示 100 个示例任务。 任务详细信息显示在右下面板中。

    ![阶段10的 sparkui 扭曲图](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>时间倾斜

“时间倾斜”选项卡根据任务执行时间显示倾斜的任务。

+ **指定参数**-第一部分显示用于检测时间偏差的参数。 检测时间偏差的默认条件是：任务执行时间大于平均执行时间的三倍，任务执行时间大于30秒。 可以根据需要更改参数。 “倾斜的阶段”和“倾斜图表”显示相应的阶段和任务信息，就像上面的“数据倾斜”选项卡一样。

+ 选择 "**时间偏差**"，然后根据在 "**指定参数**" 部分中设置的参数，在 "**倾斜阶段**" 部分显示筛选结果。 在 "**倾斜阶段**" 部分中选择一个项目，然后在 section3 中草拟相应的图表，并在右侧面板中显示任务详细信息。

    ![sparkui 诊断时间倾斜部分](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>执行程序使用情况分析

执行程序使用图会可视化 Spark 作业实际执行程序分配和运行状态。  

+ 选择 **"执行器使用情况分析**"，然后对执行器使用情况使用四种类型的曲线进行草拟，其中包括**分配**的执行器、**运行**执行器、**空闲**执行器和**最大执行** 关于分配的执行程序，每个“执行程序已添加”或“执行程序已删除”事件都会增加或减少已分配执行程序数。如需详细的比较，可查看“作业”选项卡中的“事件时间线”。

    ![sparkui "诊断执行器" 选项卡](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ 选择颜色图标以选择或取消选择所有草稿中的相应内容。

    ![sparkui 诊断选择图表](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>常见问题解答

### <a name="1-revert-to-community-version"></a>1. 还原到社区版本

若要还原到社区版本，请执行以下步骤：

1. 在 Ambari 中打开群集。
1. 导航到**custom-spark2-defaults** > **配置** > **自定义 custom-spark2-defaults-默认值**。
1. 选择 "**添加属性 ...** "、"**添加"** ，然后单击 "保存"。
1. 现在，该属性设置为 **false**。
1. 选择“保存”以保存配置。

    ![Apache Ambari 功能关闭](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 在左面板中选择 " **custom-spark2-defaults** "，在 "**摘要**" 选项卡下选择 " **custom-spark2-defaults History 服务器**"。

    ![Apache Ambari Custom-spark2-defaults 摘要视图](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. 选择 "**重新启动** **custom-spark2-defaults history server**"，重新启动历史记录服务器。

    ![Apache Ambari Custom-spark2-defaults 史重新启动](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
1. 刷新 Spark History Server Web UI，该服务将还原到社区版本。

### <a name="2-upload-history-server-event"></a>2. 上载历史记录服务器事件

如果遇到 History Server 错误，请执行以下步骤来提供事件：

1. 在历史记录服务器 web UI 中选择 "**下载**" 以下载事件。

    ![Custom-spark2-defaults 历史记录服务器下载](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 选择 "从数据/图形" 选项卡**提供反馈**。

    ![Spark graph 向我们提供反馈](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供遇到的错误的标题和说明，将 zip 文件拖放到编辑字段中，然后单击“提交新问题”。

    ![apache spark 文件问题示例](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. 升级适用于修补程序的 jar 文件方案

若要使用修补程序进行升级，请使用以下脚本，该脚本会升级 spark-enhancement.jar*。

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

**使用情况**：

`upgrade_spark_enhancement.sh https://${jar_path}`

**示例**：

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**在 Azure 门户中使用 bash 文件**

1. 启动[Azure 门户](https://ms.portal.azure.com)，并选择群集。
2. 使用以下参数完成[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)：

    |properties |Value |
    |---|---|
    |脚本类型|- Custom|
    |名称|UpgradeJar|
    |Bash 脚本 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |节点类型|Head，辅助角色|
    |parameters|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure 门户提交脚本操作](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>已知问题

+ 目前，它仅适用于 Spark 2.3 和2.4 群集。

+ 使用 RDD 的输入/输出数据不会显示在 "数据" 选项卡中。

## <a name="next-steps"></a>后续步骤

+ [管理 HDInsight 上 Apache Spark 群集的资源](apache-spark-resource-manager.md)
+ [配置 Apache Spark 设置](apache-spark-settings.md)

## <a name="contact-us"></a>联系我们

如果你有任何反馈，或使用此工具时遇到任何问题，请在（[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)）发送电子邮件。
