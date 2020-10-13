---
title: 如何在 Azure HDInsight 群集中使用 Apache Hive 复制
description: 了解如何在 HDInsight 群集中使用 Hive 复制来复制 Hive 元存储和 Azure Data Lake Storage 第2代 Data Lake。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: af74392b3368a25e5d238f774292c80de5f91c65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857735"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>如何在 Azure HDInsight 群集中使用 Apache Hive 复制

在数据库和仓库的上下文中，复制是将实体从一个仓库复制到另一个仓库的过程。 重复项可应用于整个数据库或更小的级别，例如表或分区。 目标是在基础实体发生更改时有一个副本发生更改。 Apache Hive 上的复制侧重于灾难恢复，并提供单向的主要副本复制。 在 HDInsight 群集中，可以使用 Hive 复制来 unidirectionally 复制 Hive 元存储和 Azure Data Lake Storage Gen2 上关联的基础 data lake。  

Hive 复制已多年来，提供更好的功能，并且消耗的资源更快且资源更少。 本文介绍了 Hive 复制 (Replv2) ，这两 4.0 3.6 种类型都受支持。

## <a name="advantages-of-replv2"></a>Replv2 的优点

与使用 Hive[导入/导出](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)的 hive 复制的第一个版本相比， [hive ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development)或 (Replv2) 具有以下优势：

- 基于事件的增量复制
- 时间点复制  
- 降低带宽要求  
- 中间副本的减少量  
- 保留复制状态
- 约束复制  
- 支持中心和辐射模型  
- HDInsight 4.0)  (支持 ACID 表

## <a name="replication-phases"></a>复制阶段

基于 Hive 事件的复制是在主群集和辅助群集之间配置的。 此复制包括两个不同的阶段：引导和增量运行。

### <a name="bootstrapping"></a>举

启动将运行一次，以便将数据库的基本状态从主数据库复制到辅助数据库。 如果需要，可以配置引导以在目标数据库中包含需要启用复制的表的子集。

### <a name="incremental-runs"></a>增量运行

引导后，在主群集上自动执行增量运行，并在辅助群集上播放这些增量运行期间生成的事件。 当辅助群集与主群集保持活动状态时，辅助群集将与主要的事件一致。

## <a name="replication-commands"></a>复制命令

Hive 提供一组复制命令（ `DUMP` 、 `LOAD` 和） `STATUS` 来协调事件的流动。 此 `DUMP` 命令生成主群集上所有 DDL/DML 事件的本地日志。 `LOAD`命令是一种惰式复制元数据和记录到提取的复制转储输出的数据，并在目标群集上执行的方法。 此 `STATUS` 命令从目标群集运行，以提供最新的事件 ID，最新的复制加载已成功复制。

### <a name="set-replication-source"></a>设置复制源

开始复制之前，请确保要复制的数据库被设置为复制源。 你可以使用 `DESC DATABASE EXTENDED <db_name>` 命令来确定是否使用 `repl.source.for` 策略名称设置参数。

如果已计划策略，但 `repl.source.for` 未设置参数，则需要首先使用设置此参数 `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` 。

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>将元数据转储到 data lake

`REPL DUMP [database name]. => location / event_id`在启动阶段使用命令将相关元数据转储到 Azure Data Lake Storage Gen2。 `event_id`指定将相关元数据放入 Azure Data Lake Storage Gen2 的最小事件。 
 
```sql
repl dump tpcds_orc; 
```
示例输出：

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>将数据加载到目标群集

`REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }`使用命令将数据加载到目标群集中，以实现复制的启动和增量阶段。 `[database name]`可以与目标群集上的源或其他名称相同。 `[location]`表示以前的命令输出中的位置 `REPL DUMP` 。 这意味着目标群集应该能够与源群集通信。 `WITH`主要添加了子句，以防止目标群集重新启动，从而允许复制。

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>输出最后复制的事件 ID

`REPL STATUS [database name]`命令在目标群集上执行并输出上次复制的 `event_id` 。 命令还允许用户了解其目标群集复制到的状态。 可以使用此命令的输出来构造增量复制的下一个 `REPL DUMP` 命令。

```sql
repl status tpcds_orc;
```

示例输出：

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>将相关数据和元数据转储到 data lake

`REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }`命令用于将相关的元数据和数据转储到 Azure Data Lake Storage。 此命令在增量阶段中使用，并在源仓库上运行。 `FROM [event-id]`增量阶段需要，的值 `event-id` 可以通过 `REPL STATUS [database name]` 在目标仓库上运行命令来派生。

```sql
repl dump tpcds_orc from 2925;
```

示例输出：

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Hive 复制过程

以下步骤是在 Hive 复制过程中发生的顺序事件。

1. 确保要复制的表设置为特定策略的复制源。

1. 此 `REPL_DUMP` 命令将颁发给具有关联的约束（如数据库名称、事件 ID 范围和 Azure Data Lake Storage Gen2 存储 URL）的主群集。

1. 系统将元存储中所有跟踪事件的转储序列化到最新版本。 此转储存储在由指定的 URL 上的主群集上的 Azure Data Lake Storage Gen2 存储帐户中 `REPL_DUMP` 。  

1. 主群集将复制元数据保存到主群集的 Azure Data Lake Storage Gen2 存储。 该路径可在 Ambari 的 Hive Config UI 中进行配置。 此过程提供存储元数据的路径，以及最新跟踪的 DML/DDL 事件的 ID。

1. 此 `REPL_LOAD` 命令是从辅助群集发出的。 命令指向步骤3中配置的路径。

1. 辅助群集通过在步骤3中创建的跟踪事件来读取元数据文件。 确保辅助群集具有与主群集的 Azure Data Lake Storage Gen2 存储的网络连接，其中存储了的跟踪事件 `REPL_DUMP` 。  

1. 辅助群集) 计算 (生成分布式复制 `DistCP` 。

1. 辅助群集从主群集的存储复制数据。  

1. 辅助群集上的元存储已更新。  

1. 最后跟踪的事件 ID 存储在主元存储中。

增量复制遵循相同的过程，并且需要最后复制的事件 ID 作为输入。 这会导致自上次复制事件以来的增量复制。 增量复制通常以预定的频率自动执行，以实现所需的恢复点目标 (RPO) 。

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Hive 复制关系图":::

## <a name="replication-patterns"></a>复制模式  

通常，复制在主要副本和辅助副本之间以单向方式进行配置，在这种情况下，主适用于读取和写入请求。 辅助群集仅适用于读取请求。 如果发生灾难，则允许在辅助数据库上执行写入，但需要将反向复制重新配置为主要副本。

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Hive 复制关系图":::

有多种模式适用于 Hive 复制，包括主要副本（辅助数据库、中心节点和分支数据库）和中继。

在 HDInsight Active 主节点中，备用辅助数据库是一种常见的业务连续性和灾难恢复 (BCDR) 模式和 HiveReplicationV2 可将此模式与使用 VNet 对等互连的突破分隔的 HDInsight Hadoop 群集配合使用。 通常，可以使用对等互连到这两个群集的公共虚拟机来托管复制自动化脚本。 有关可能的 HDInsight BCDR 模式的详细信息，请参阅 [hdinsight 业务连续性文档](../hdinsight-business-continuity.md)。  

### <a name="hive-replication-with-enterprise-security-package"></a>与企业安全性套餐的 Hive 复制  

如果在使用企业安全性套餐的 HDInsight Hadoop 群集上计划 Hive 复制，则必须考虑 Ranger 元存储的复制机制和 Azure Active Directory 域服务 (AD DS) 。  

使用 Azure AD DS 副本集功能为跨多个区域的每个 Azure AD 租户创建多个 Azure AD DS 副本集。 每个单独副本集需要在其各自区域中与 HDInsight Vnet 对等互连。 在此配置中，对 Azure AD DS 的更改（包括配置、用户标识和凭据、组、组策略对象、计算机对象以及其他更改）将应用到使用 Azure AD DS 复制的托管域中的所有副本集。
  
可以使用 Ranger Import-Export 功能定期备份 Ranger 策略，并将其从主副本复制到辅助副本。 您可以选择复制所有或 Ranger 策略的子集，具体取决于要在辅助群集上实现的授权级别。  

## <a name="sample-code"></a>示例代码  

下面的代码序列提供一个示例，说明如何在名为的示例表中实现引导和增量复制 `tpcds_orc` 。  

1. 将该表设置为复制策略的源。

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. 主群集上的启动转储。

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   示例输出：
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. 辅助群集上的启动负载。 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. 检查 `REPL` 辅助群集上的状态。

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. 主要群集上的增量转储。

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   示例输出：
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. 辅助群集上的增量负载。  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. 检查 `REPL` 辅助群集的状态。

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>后续步骤

若要详细了解本文中所述的项，请参阅：

- [Azure HDInsight 业务连续性](../hdinsight-business-continuity.md)
- [Azure HDInsight 业务连续性体系结构](../hdinsight-business-continuity-architecture.md)
- [Azure HDInsight 高度可用的解决方案体系结构案例研究](../hdinsight-high-availability-case-study.md)
- [什么是 Azure HDInsight 中的 Apache Hive 和 HiveQL？](../hadoop/hdinsight-use-hive.md)