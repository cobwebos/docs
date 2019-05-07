---
title: 在 Azure HDInsight Linux 群集上安装 Presto
description: 了解如何使用脚本操作在基于 Linux 的 HDInsight Hadoop 群集上安装 Presto 和 Airpal。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 7ce2cf40dafc09d86d0c424a3954a334ae83143a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145485"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>在基于 Hadoop 的 HDInsight 群集上安装并使用 Presto

> [!IMPORTANT]  
> 请阅读：以下说明现已弃用。 中的最新指南，请参阅[星爆图案数据](http://docs.starburstdata.com/latest/azure.html)安装和运行在 Azure HDInsight 上的 Presto。  

本文介绍如何使用脚本操作在基于 Hadoop 的 Azure HDInsight 群集上安装 Presto。 此外，还介绍如何在现有的 Presto HDInsight 群集上安装 Airpal。

HDInsight 还为 Apache Hadoop 群集提供了 Starburst Presto 应用程序。 有关详细信息，请参阅[在 Azure HDInsight 上安装第三方 Apache Hadoop 应用程序](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications)。

> [!IMPORTANT]  
> 本文中的步骤需要使用 Linux 的 HDInsight 3.5 Hadoop 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本](hdinsight-component-versioning.md)。

## <a name="what-is-presto"></a>什么是 Presto？
[Presto](https://prestosql.io) 是适用于大数据的快速分布式 SQL 查询引擎。 Presto 适合用于对 PB 量级的数据进行交互式查询。 有关 Presto 的组件及其如何配合工作的详细信息，请参阅 [Presto 的概念](https://prestosql.io/docs/current/overview/concepts.html)。

> [!WARNING]  
> HDInsight 群集提供的组件受到完全支持。 Microsoft 支持部门会帮助找出并解决与这些组件相关的问题。
> 
> 自定义组件（例如 Presto）可获得合理范围的支持，以便进一步排查问题。 此支持也许能够解决问题。 支持人员可能要求你参与可用的开源技术渠道，在该处可以找到该技术的深入专业知识。 有许多可以使用的社区站点。 例如[有关 HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [Stack Overflow](https://stackoverflow.com)。 
>
> Apache 项目还在 [Apache 网站](https://apache.org)上提供了项目站点， 例如 [Hadoop](https://hadoop.apache.org/)。


## <a name="install-presto-by-using-script-actions"></a>使用脚本操作安装 Presto

本部分说明如何在通过 Azure 门户创建新群集时使用示例脚本： 

1. 执行[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)中的步骤，开始预配群集。 请确保使用**自定义**群集创建流创建群集。 群集必须满足以下要求：

   * 它必须是装有 HDInsight 3.6 版的 Hadoop 群集。

   * 它必须使用 Azure 存储作为数据存储。 目前尚不支持在使用 Azure Data Lake Storage 作为存储选项的群集上使用 Presto。

     ![HDInsight、自定义（大小、设置、应用）](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. 在“高级设置”区域中，选择“脚本操作”。 提供以下信息。 对于脚本类型，还可以选择“安装 Presto”选项：
   
   * **NAME**。 输入脚本操作的友好名称。
   * **Bash 脚本 URI**。 `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`。
   * **标头**。 选择此选项。
   * **辅助角色**。 选择此选项。
   * **ZOOKEEPER**。 将此复选框留空。
   * **参数**。 将此字段留空。


3. 在“脚本操作”区域底部，选择“选择”按钮以保存配置。 最后，选择“高级设置”区域底部的“选择”按钮来保存配置信息。

4. 根据[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)中的说明，继续预配群集。

    > [!NOTE]  
    > Azure PowerShell、Azure 经典 CLI、HDInsight .NET SDK 或 Azure 资源管理器模板也可用于应用脚本操作。 也可以将脚本操作应用于已在运行的群集。 有关详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>在 HDInsight 中使用 Presto

若要在 HDInsight 群集中使用 Presto，请执行以下步骤：

1. 使用 SSH 连接到 HDInsight 群集：
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。
     

2. 运行以下命令，启动 Presto shell：
   
    `presto --schema default`

3. 针对所有 HDInsight 群集默认提供的示例表 **hivesampletable** 运行查询：
   
    `select count (*) from hivesampletable;`
   
    默认情况下，已配置适用于 Presto 的 [Apache Hive](https://prestosql.io/docs/current/connector/hive.html) 和 [TPCH](https://prestosql.io/docs/current/connector/tpch.html) 连接器。 Hive 连接器配置为使用默认的 Hive 安装， 因此 Hive 中的所有表会自动在 Presto 中显示。

    有关详细信息，请参阅 [Presto 文档](https://prestosql.io/docs/current/index.html)。

## <a name="use-airpal-with-presto"></a>将 Airpal 与 Presto 配合使用

[Airpal](https://github.com/airbnb/airpal#airpal) 是适用于 Presto 的基于 Web 的开源查询接口。 有关 Airpal 的详细信息，请参阅 [Airpal 文档](https://github.com/airbnb/airpal#airpal)。

执行以下步骤，在边缘节点上安装 Airpal：

1. 使用 SSH 连接到 Presto 已安装的 HDInsight Hadoop 群集边缘节点：
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 连接后，运行以下命令：

    `sudo slider registry  --name presto1 --getexp presto` 
   
    将显示类似于以下 JSON 的输出：

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. 在输出中，记下 **value** 属性的值。 在群集边缘节点上安装 Airpal 时需要此值。 在上面的输出中，所需的值为 **10.0.0.12:9090**。

4. 使用[此模板](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)创建 HDInsight 群集边缘节点。 按以下屏幕截图所示提供值。

    ![自定义部署](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. 选择“购买”。

6. 将更改应用到群集配置后，请执行以下步骤，从 [Azure 门户](https://portal.azure.com)访问 Airpal Web 接口：

    1. 在左侧菜单中，选择“所有服务”。

    1. 在“ANALYTICS”下，选择“HDInsight 群集”。

    1. 从列表中选择你的群集。此时会打开默认视图。

    1. 在默认视图中的“设置”下，选择“应用程序”。

        ![HDInsight，应用程序](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. 在“已安装应用”页中，找到“airpal”的表条目。 选择“门户”。

        ![已安装的应用](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. 出现提示时，请输入在创建基于 Hadoop 的 HDInsight 群集时指定的管理员凭据。

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>自定义 HDInsight 群集上的 Presto 安装

若要自定义安装，请执行以下步骤：

1. 使用 SSH 连接到 Presto 已安装的 HDInsight Hadoop 群集边缘节点：
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 在 `/var/lib/presto/presto-hdinsight-master/appConfig-default.json` 文件中进行配置更改。 有关 Presto 配置的详细信息，请参阅[基于 YARN 的群集的 Presto 配置选项](https://prestosql.github.io/presto-yarn/installation-yarn-configuration-options.html)。

3. 停止和终止当前正在运行的 Presto 实例：

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. 使用自定义启动 Presto 的新实例：

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. 等待新实例准备就绪。 记下 Presto 协调器地址：

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>为运行 Presto 的 HDInsight 群集生成基准数据

TPC-DS 是有关测量多个决策支持系统（包括大数据系统）的性能的行业标准。 可以使用 Presto 生成数据，并将它与自己的 HDInsight 基准数据进行比较并评估结果。 有关更多信息，请参阅 [tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md)。



## <a name="next-steps"></a>后续步骤
* [在 HDInsight Hadoop 群集上安装并使用 Hue](hdinsight-hadoop-hue-linux.md)。 Hue 是一个 Web UI，可以使用它轻松创建、运行及保存 Apache Pig 和 Hive 作业。

* [在 HDInsight Hadoop 群集上安装 Apache Giraph 并使用 Giraph 处理大型图形](hdinsight-hadoop-giraph-install-linux.md)。 使用群集自定义在基于 Hadoop 的 HDInsight 群集上安装 Giraph。 使用 Giraph 时，可以通过 Hadoop 执行图像处理。 它也可与 Azure HDInsight 配合使用。

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
