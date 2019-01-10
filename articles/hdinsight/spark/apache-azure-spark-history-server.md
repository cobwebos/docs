---
title: 使用扩展的 Spark History Server 调试和诊断 Spark 应用程序 - Azure HDInsight
description: 使用扩展的 Spark History Server 调试和诊断 Spark 应用程序 - Azure HDInsight。
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: a896c949e1f05a5d9ee179fa475150ad8da34283
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792775"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>使用扩展的 Apache Spark History Server 调试和诊断 Apache Spark 应用程序

本文提供有关如何使用扩展的 Apache Spark History Server 来调试和诊断已完成的和正在运行的 Spark 应用程序的指导。 该扩展包含数据选项卡、图形选项卡和诊断选项卡。在“数据”选项卡中，用户可以检查 Spark 作业的输入和输出数据。 在“图形”选项卡中，用户可以检查数据流和重播作业图形。 在“诊断”选项卡中，用户可以参阅“数据偏斜”、“时间偏斜”和“执行程序使用情况分析”。

## <a name="get-access-to-apache-spark-history-server"></a>获取 Apache Spark History Server 的访问权限

Apache Spark History Server 是已完成的和正在运行的 Spark 应用程序的 Web UI。 

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>从 Azure 门户打开 Apache Spark History Server Web UI

1. 从 [Azure 门户](https://portal.azure.com/)打开 Spark 群集。 有关详细信息，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。
2. 在“快速链接”中，依次单击“群集仪表板”、“Spark History Server”。 出现提示时，输入 Spark 群集的管理员凭据。 

    ![Spark History Server](./media/apache-azure-spark-history-server/launch-history-server.png "Spark History Server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>通过 URL 打开 Spark History Server Web UI
浏览到以下 URL（将 <ClusterName> 替换为客户的 Spark 群集名称），打开 Spark History Server。

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Spark History Server Web UI 如下所示：

![HDInsight Spark History Server](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Spark History Server 中的“数据”选项卡
选择作业 ID，然后单击工具菜单中的“数据”获取数据视图。

+ 分别选择“输入”、“输出”和“表操作”选项卡，以检查其中的信息。

    ![数据选项卡](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ 单击“复制”按钮复制所有行。

    ![数据复制](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ 单击“csv”按钮将所有数据保存为 CSV 文件。

    ![数据保存](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ 在“搜索”字段中输入关键字进行搜索，搜索结果会立即显示。

    ![数据搜索](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ 单击列标题将表排序，单击加号展开某行以显示更多详细信息，或单击减号折叠某行。

    ![数据表](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ 单击右侧的“部分下载”按钮下载单个文件；选定的文件将下载到本地，如果该文件不再存在，则会打开一个显示错误消息的新选项卡。

    ![数据下载行](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 选择下载菜单中展开的“复制完整路径”或“复制相对路径”，来复制完整路径或相对路径。 对于 Azure Data Lake Storage 文件，选择“在 Azure 存储资源管理器中打开”会启动 Azure 存储资源管理器，并在登录时定位到相应的文件夹。

    ![数据复制路径](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 如果一个页面中显示的行数过多，单击表下面的编号可以浏览不同的页面。 

    ![数据页](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ 将鼠标悬停在“数据”旁边的问号上以显示工具提示，或单击问号获取更多信息。

    ![数据 - 更多信息](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ 单击“向我们提供反馈”发送问题反馈。

    ![图形 - 反馈](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark History Server 中的“图形”选项卡
选择作业 ID，然后单击工具菜单中的“图形”获取作业图形视图。

+ 通过生成的作业图形检查作业概述。 

+ 默认情况下，图形中会显示所有作业，可按“作业 ID”筛选作业。

    ![图形 - 作业 ID](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ 默认已选择“进度”，用户可以在“显示”下拉列表中选择“已读取/已写入”来检查数据流。

    ![图形 - 显示](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    彩色图形节点视图，其中显示了热度地图。

    ![图形 - 热度地图](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ 单击“播放”按钮可播放作业，单击“停止”按钮可随时停止播放。 彩色任务视图，其中显示了播放时的不同状态：

    + 绿色表示已成功：已成功完成作业。
    + 橙色表示已重试：失败的任务实例，但不影响作业的最终结果。 这些任务具有稍后可能成功的重复实例或重试实例。
    + 蓝色表示正在运行：任务正在运行。
    + 白色表示正在等待或已跳过：任务正在等待运行，或已跳过执行阶段。
    + 红色表示已失败：任务已失败。

    ![图形颜色示例：正在运行](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    跳过的阶段显示为白色。
    ![图形颜色示例，跳过](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![图形颜色示例：失败](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]  
    > 允许播放每个作业。 不完整的作业不支持播放。


+ 滚动鼠标滚轮以缩小/放大作业图形，或者单击“缩放到合适大小”使图形适合屏幕大小。
 
    ![图形 - 缩放到合适大小](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 如果存在失败的任务，将鼠标悬停在图形节点可查看工具提示；单击阶段可打开阶段页。

    ![图形 - 工具提示](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 在作业图形选项卡中，如果阶段的任务满足以下条件，则会显示该阶段的工具提示和小图标：
    + 数据倾斜：数据读取大小 > 此阶段所有任务的平均数据读取大小 * 2 且数据读取大小 > 10 MB。
    + 时间倾斜：执行时间 > 此阶段所有任务的平均执行时间 * 2 且执行时间 > 2 分钟。

    ![图形倾斜图标](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

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

+ 单击“向我们提供反馈”发送问题反馈。

    ![图形 - 反馈](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark History Server 中的“诊断”选项卡
选择作业 ID，然后单击工具菜单中的“诊断”以获取作业的“诊断”视图。 “诊断”选项卡包括“数据偏斜”、“时间偏斜”和“执行程序使用情况分析”。
    
+ 通过选择相应的选项卡来查看“数据偏斜”、“时间偏斜”和“执行程序使用情况分析”。

    ![诊断选项卡](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>数据倾斜
单击“数据倾斜”选项卡，系统会根据指定的参数显示相应的已倾斜任务。 

+ **指定参数** - 第一部分显示用于检测数据倾斜的参数。 内置规则为：任务数据读取大于 3 倍任务数据读取平均值，且任务数据读取大于 10MB。 如需针对倾斜的任务定义自己的规则，可以选择参数，然后“倾斜的阶段”和“倾斜字符型”部分会相应地进行刷新。

+ **倾斜的阶段** - 第二部分显示阶段，这些阶段的倾斜的任务符合上面指定的条件。 如果某个阶段有多个倾斜的任务，则倾斜的阶段表仅显示最倾斜的任务（例如，数据倾斜对应的最大数据）。

    ![数据倾斜第 2 部分](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **倾斜图表** - 当倾斜阶段表中的某一行处于选中状态时，倾斜图表会根据数据读取和执行时间显示更多任务分发详细信息。 倾斜的任务标记为红色，正常任务标记为蓝色。 出于性能考虑，此图表最多只显示 100 个示例任务。 任务详细信息显示在右下面板中。

    ![数据倾斜第 3 部分](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>时间倾斜
“时间倾斜”选项卡根据任务执行时间显示倾斜的任务。 

+ **指定参数** - 第一部分显示用于检测时间倾斜的参数。 用于检测时间倾斜的默认条件是：任务执行时间长于平均执行时间的 3 倍，且任务执行时间长于 30 秒。 可以根据需要更改参数。 “倾斜的阶段”和“倾斜图表”显示相应的阶段和任务信息，就像上面的“数据倾斜”选项卡一样。

+ 单击“时间倾斜”，然后筛选的结果就会根据在“指定参数”部分设置的参数显示在“倾斜的阶段”部分。 单击“倾斜的阶段”部分的一个项目，然后相应的图表就会在第 3 部分绘制，任务详细信息显示在右下面板中。

    ![时间倾斜第 2 部分](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>执行程序使用情况分析
执行程序使用图会可视化 Spark 作业实际执行程序分配和运行状态。  

+ 单击“执行程序使用情况分析”，然后系统就会绘制四种类型的有关执行程序使用情况的曲线，包括**已分配的执行程序**、**正在运行的执行程序**、**空闲执行程序**，以及**最大执行程序实例数**。 关于分配的执行程序，每个“执行程序已添加”或“执行程序已删除”事件都会增加或减少已分配执行程序数。如需详细的比较，可查看“作业”选项卡中的“事件时间线”。

    ![“执行程序”选项卡](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ 单击彩色图标即可选中或取消选中所有草稿中的相应内容。

    ![选择图表](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>常见问题解答

### <a name="1-revert-to-community-version"></a>1.还原到社区版本

若要还原到社区版本，请执行以下步骤：

1. 在 Ambari 中打开群集。 在左面板中单击“Spark2”。
2. 单击“配置”选项卡。
3. 展开“自定义 spark2-defaults”组。
4. 单击“添加属性”，添加 **spark.ui.enhancement.enabled=false** 并保存。
5. 现在，该属性设置为 **false**。
6. 单击 **保存** 以保存配置。

    ![功能关闭](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. 在左面板中单击“Spark2”，在“摘要”选项卡下单击“Spark2 History Server”。

    ![重启 server1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. 单击“重启 Spark2 History Server”以重启 History Server。

    ![重启 server2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. 刷新 Spark History Server Web UI，该服务将还原到社区版本。

### <a name="2-upload-history-server-event"></a>2.上传 History Server 事件

如果遇到 History Server 错误，请执行以下步骤来提供事件：
1. 在 History Server Web UI 中单击“下载”以下载事件。

    ![下载事件](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 在数据/图形选项卡中单击“向我们提供反馈”。

    ![图形 - 反馈](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供遇到的错误的标题和说明，将 zip 文件拖放到编辑字段中，然后单击“提交新问题”。

    ![提出问题](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3.通过修补方案升级 jar 文件

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

1. 启动 [Azure 门户](https://ms.portal.azure.com)，并选择自己的群集。
2. 依次单击“脚本操作”、“提交新项”。 填写“提交脚本操作”表单，然后单击“创建”按钮。
    
    + **脚本类型**：选择“自定义”。
    + **名称**：指定脚本名称。
    + **Bash 脚本 URI**：将 bash 文件上传到专用群集，然后在该群集中复制 URL。 或者使用提供的 URI。
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + 选中“头节点”和“工作节点”。
    + **参数**：按照 bash 用法设置参数。

    ![上传日志或升级修补程序](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issues"></a>已知问题

1.  目前，该服务仅适用于 Spark 2.3 群集。

2.  使用 RDD 的输入/输出数据不会显示在数据选项卡中。

## <a name="next-steps"></a>后续步骤

* [管理 HDInsight 上 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [配置 Apache Spark 设置](apache-spark-settings.md)


## <a name="contact-us"></a>联系我们

如有任何反馈，或使用此工具时遇到其他任何问题，请向 [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) 发送电子邮件。
