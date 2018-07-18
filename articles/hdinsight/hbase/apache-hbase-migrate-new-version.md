---
title: 将 HBase 群集迁移到新版本 - Azure HDInsight | Microsoft Docs
description: 如何将 HBase 群集迁移到新版本。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 2ab0bc792777b73f878bc1728c0d8310ecf41167
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165088"
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>将 HBase 群集迁移到新版本

可以直接升级基于作业的群集，例如 Spark 和 Hadoop - 具体请参阅[将 HDInsight 群集升级到更新版本](../hdinsight-upgrade-cluster.md)：

1. 备份暂时性（本地存储的）数据。
2. 删除现有的群集。
3. 在同一个 VNET 子网中创建新群集。
4. 导入暂时性数据。
5. 启动作业并在新群集上继续处理。

若要升级 HBase 群集，需要执行本文所述的一些附加步骤。

> [!NOTE]
> 升级造成的停机时间应该很短，以分钟计。 停机是执行刷新所有内存中数据的步骤，然后在新群集上配置和重启服务造成的。 根据节点数目、数据量和其他变数，结果会有所不同。

## <a name="review-hbase-compatibility"></a>检查 HBase 兼容性

在升级 HBase 之前，请确保源和目标群集上的 HBase 版本兼容。 有关详细信息，请参阅 [HDInsight 提供的 Hadoop 组件和版本](../hdinsight-component-versioning.md)。

> [!NOTE]
> 我们强烈建议查看 [HBase 书册](https://hbase.apache.org/book.html#upgrading)中的版本兼容性矩阵。

下面是一个示例版本兼容性矩阵，其中的 Y 表示兼容，N 表示潜在的不兼容：

| 兼容性类型 | 主版本| 次版本 | 修补程序 |
| --- | --- | --- | --- |
| 客户端-服务器网络兼容性 | N | Y | Y |
| 服务器-服务器兼容性 | N | Y | Y |
| 文件格式兼容性 | N | Y | Y |
| 客户端 API 兼容性 | N | Y | Y |
| 客户端二进制文件兼容性 | N | N | Y |
| **服务器端受限的 API 兼容性** |  |  |  |
| Stable | N | Y | Y |
| 不断变化 | N | N | Y |
| 不稳定 | N | N | N |
| 依赖项兼容性 | N | Y | Y |
| 操作兼容性 | N | N | Y |

> [!NOTE]
> HBase 版本发行说明中应会阐述任何重大的不兼容性。

## <a name="upgrade-with-same-hbase-major-version"></a>使用相同的 HBase 主版本升级

以下方案适用于使用相同的 HBase 主版本从 HDInsight 3.4 升级到 3.6（Apache HBase 1.1.2 已随附这两个版本）。 只要源和目标版本之间不存在兼容性问题，其他版本升级的过程都是类似的。

1. 请确保应用程序与新版本兼容，如 HBase 兼容性矩阵和发行说明中所述。 在运行 HDInsight 和 HBase 目标版本的群集中测试应用程序。

2. 使用相同的存储帐户、不同的容器名称[设置新的目标 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)：

    ![使用相同的存储帐户，但创建不同的容器](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. 刷新源 HBase 群集。 这是要升级的源群集。 HBase 将传入的数据写入名为 _memstore_ 的内存中存储。 memstore 达到特定的大小后，会刷新到群集存储帐户中用作长期存储的磁盘中。 删除旧群集时，将回收 memstores，这可能会丢失数据。 若要将每个表的 memstore 手动刷新到磁盘，请运行以下脚本。 Azure 的 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) 中提供了此脚本的最新版本。

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. 停止引入到旧 HBase 群集。
5. 为确保刷新 memstore 中的所有最新数据，请再次运行前面的脚本。
6. 在旧群集 (https://OLDCLUSTERNAME.azurehdidnsight.net) 上登录到 Ambari，并停止 HBase 服务。 当系统提示确认想要停止服务时，请选中为 HBase 启用维护模式的框。 有关连接和使用 Ambari 的详细信息，请参阅[使用 Ambari Web UI 管理 HDInsight 群集](../hdinsight-hadoop-manage-ambari.md)。

    ![在 Ambari 中单击“服务”选项卡，在左侧菜单中选择“HBase”，然后在“服务操作”下选择“停止”](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![选中“为 HBase 启用维护模式”复选框，然后确认](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. 在新 HDInsight 群集上登录到 Ambari。 将 `fs.defaultFS` HDFS 设置更改为指向原始群集所用的容器名称。 此设置位于“HDFS”>“配置”>“高级”>“高级 core-site”下。

    ![在 Ambari 中单击“服务”选项卡，然后依次选择左侧菜单中的“HDFS”、“配置”选项卡及其下的“高级”选项卡](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![在 Ambari 中更改容器名称](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. 保存所做更改。
9. 根据 Ambari 中的指示重启全部所需的服务。
10. 将应用程序指向新群集。

    > [!NOTE]
    > 升级时，应用程序的静态 DNS 会更改。 不要硬编码此 DNS，可以在域名的 DNS 设置中配置一个指向群集名称的 CNAME。 另一种做法是使用应用程序的、无需重新部署即可更新的配置文件。

11. 启动引入，确定一切是否按预期正常运行。
12. 如果新群集符合预期，请删除原始群集。

## <a name="next-steps"></a>后续步骤

有关 HBase 和升级 HDInsight 群集的详细信息，请参阅以下文章：

* [将 HDInsight 群集升级到更新版本](../hdinsight-upgrade-cluster.md)
* [使用 Ambari Web UI 监视和管理 Azure HDInsight](../hdinsight-hadoop-manage-ambari.md)
* [Hadoop 组件和版本](../hdinsight-component-versioning.md)
* [使用 Ambari 优化配置](../hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)
