---
title: 在 Azure HDInsight Linux 群集上安装 Presto
description: 了解如何使用脚本操作在基于 Linux 的 HDInsight Hadoop 群集上安装 Presto 和 Airpal。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: ea777b13348b84aaeb7cb7628a4d0aac9f5705bd
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591415"
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>在 HDInsight Hadoop 群集上安装并使用 Presto

本文档介绍如何使用脚本操作在 HDInsight Hadoop 群集上安装 Presto。 此外，还介绍如何在现有的 Presto HDInsight 群集上安装 Airpal。

> [!IMPORTANT]
> 本文档中的步骤需要使用 Linux 的 **HDInsight 3.5 Hadoop 群集**。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本](hdinsight-component-versioning.md)。

## <a name="what-is-presto"></a>什么是 Presto？
[Presto](https://prestodb.io/overview.html) 是适用于大数据的快速分布式 SQL 查询引擎。 Presto 适合用于对 PB 量级的数据进行交互式查询。 有关 Presto 的组件及其如何配合工作的详细信息，请参阅 [Presto 的概念](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst)。

> [!WARNING]
> 完全支持通过 HDInsight 群集提供的组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。
> 
> 自定义组件（如 Presto）可获得合理范围的支持，以帮助进一步排查问题。 这可能导致问题解决，或要求参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [http://stackoverflow.com](http://stackoverflow.com)。 此外，Apache 项目在 [http://apache.org](http://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/)。
> 
> 


## <a name="install-presto-using-script-action"></a>使用脚本操作安装 Presto

本部分提供有关如何在通过使用 Azure 门户创建新群集时使用示例脚本的说明。 

1. 使用[预配基于 Linux 的 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-portal.md)中的步骤开始预配群集。 请确保使用**自定义**群集创建流创建群集。 群集必须满足以下要求：

    * 它必须是装有 HDInsight 3.6 版的 Hadoop 群集。

    * 它必须使用 Azure 存储作为数据存储。 目前不支持在将 Azure Data Lake Store 用作存储选项的群集上使用 Presto。 

    ![使用自定义选项创建 HDInsight 群集](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. 在“高级设置”区域中，选择“脚本操作”，并提供以下信息：
   
   * **名称**：输入脚本操作的友好名称。
   * **Bash 脚本 URI**：`https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **标头**：选中此选项
   * **辅助角色**：选中此选项
   * **ZOOKEEPER**：清除此复选框
   * **参数**：将此字段留空


3. 在“脚本操作”区域底部，单击“选择”按钮以保存配置。 最后，单击“高级设置”区域底部的“选择”按钮来保存配置信息。

4. 根据[预配基于 Linux 的 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-portal.md)中所述继续预配群集。

    > [!NOTE]
    > Azure PowerShell、Azure CLI、HDInsight .NET SDK 或 Azure 资源管理器模板也可用于应用脚本操作。 也可以将脚本操作应用于已在运行的群集。 有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>在 HDInsight 中使用 Presto

若要在 HDInsight 群集中使用 Presto，请使用以下步骤：

1. 使用 SSH 连接到 HDInsight 群集：
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。
     

2. 使用以下命令启动 Presto shell。
   
        presto --schema default

3. 针对所有 HDInsight 群集默认提供的示例表 **hivesampletable** 运行查询。
   
        select count (*) from hivesampletable;
   
    默认情况下，已配置适用于 Presto 的 [Hive](https://prestodb.io/docs/current/connector/hive.html) 和 [TPCH](https://prestodb.io/docs/current/connector/tpch.html) 连接器。 Hive 连接器配置为使用默认安装的 Hive 安装，因此 Hive 中的所有表会自动在 Presto 中显示。

    有关详细信息，请参阅 [Presto 文档](https://prestodb.io/docs/current/index.html)。

## <a name="use-airpal-with-presto"></a>将 Airpal 与 Presto 配合使用

[Airpal](https://github.com/airbnb/airpal#airpal) 是适用于 Presto 的基于 Web 的开源查询接口。 有关 Airpal 的详细信息，请参阅 [Airpal 文档](https://github.com/airbnb/airpal#airpal)。

使用以下步骤在边缘节点上安装 Airpal：

1. 使用 SSH 连接到 Presto 已安装的 HDInsight Hadoop 群集边缘节点：
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 连接后，运行以下命令。

        sudo slider registry  --name presto1 --getexp presto 
   
    将显示类似于以下 JSON 的输出：

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. 在输出中，记下 **value** 属性的值。 在群集边缘节点上安装 Airpal 时需要此值。 从上面的输出中，所需的值为 **10.0.0.12:9090**。

4. 使用**[此处](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** 的模板创建 HDInsight 群集边缘节点并提供值，如以下屏幕截图中所示。

    ![HDInsight 在 Presto 群集上安装 Airpal](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. 单击“购买”。

6. 将更改应用到群集配置后，可以使用以下步骤访问 Airpal Web 接口。

    1. 在群集对话框中，单击“应用程序”。

        ![HDInsight 在 Presto 群集上启动 Airpal](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. 在“已安装的应用”区域中，单击 Airpal 对应的“门户”。

        ![HDInsight 在 Presto 群集上启动 Airpal](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. 出现提示时，请输入创建 HDInsight Hadoop 群集时指定的管理员凭据。

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>自定义 HDInsight 群集上的 Presto 安装

若要自定义安装，请使用以下步骤：

1. 使用 SSH 连接到 Presto 已安装的 HDInsight Hadoop 群集边缘节点：
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 在 `/var/lib/presto/presto-hdinsight-master/appConfig-default.json` 文件中进行配置更改。 有关 Presto 配置的详细信息，请参阅[基于 YARN 的群集的 Presto 配置](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html)。

3. 停止并终止当前正在运行的 Presto 实例。

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. 使用自定义启动 Presto 的新实例。

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. 等到新实例准备就绪，并记下 Presto 的协调器地址。


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>为运行 Presto 的 HDInsight 群集生成基准数据

TPC-DS 是有关测量多个决策支持系统（包括大数据系统）的性能的行业标准。 可以使用 Presto 生成数据，并将它与自己的 HDInsight 基准数据进行比较并评估结果。 有关详细信息，请参阅[此文](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md)。



## <a name="see-also"></a>另请参阅
* [在 HDInsight 群集上安装并使用 Hue](hdinsight-hadoop-hue-linux.md)。 Hue 是一个 Web UI，可以使用它轻松创建、运行及保存 Pig 和 Hive 作业。

* [在 HDInsight 群集上安装 Giraph](hdinsight-hadoop-giraph-install-linux.md)。 使用群集自定义在 HDInsight Hadoop 群集上安装 Giraph。 Giraph 可让你通过使用 Hadoop 执行图形处理，并可以在 Azure HDInsight 上使用。

* [在 HDInsight 群集上安装 Solr](hdinsight-hadoop-solr-install-linux.md)。 使用群集自定义在 HDInsight Hadoop 群集上安装 Solr。 Solr 允许对存储的数据执行功能强大的搜索操作。

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
