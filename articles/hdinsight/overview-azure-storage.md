---
title: HDInsight 中的 Azure 存储概述
description: HDInsight 中的 Azure 存储空间概述。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fc0af331dc6cb604847be9173c836e0b46ca40ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195173"
---
# <a name="azure-storage-overview-in-hdinsight"></a>HDInsight 中的 Azure 存储概述

Azure 存储是一种稳健、通用的存储解决方案，它与 HDInsight 无缝集成。 HDInsight 可将 Azure 存储中的 Blob 容器用作群集的默认文件系统。 HDInsight 中的整套组件可以通过 HDFS 界面直接操作以 Blob 形式存储的结构化或非结构化数据。

对于默认群集存储和业务数据，我们建议使用单独的存储容器。 分离是将 HDInsight 日志和临时文件与你自己的业务数据隔离开来。 我们还建议在每次使用后删除包含应用程序和系统日志的默认 blob 容器以降低存储成本。 请确保在删除该容器之前检索日志。

如果选择使用**所选网络**的**防火墙和虚拟网络**限制来保护存储帐户，请确保启用 "**允许受信任的 Microsoft 服务**" 例外。例外情况是，HDInsight 可以访问你的存储帐户。

## <a name="hdinsight-storage-architecture"></a>HDInsight 存储体系结构

下图提供了 Azure 存储的 HDInsight 存储体系结构的抽象视图：

!["HDInsight 存储体系结构"](./media/overview-azure-storage/storage-architecture.png "HDInsight 存储体系结构")

HDInsight 提供对在本地附加到计算节点的分布式文件系统的访问。 可使用完全限定 URI 访问该文件系统，例如：

    hdfs://<namenodehost>/<path>

通过 HDInsight，你还可以访问 Azure 存储中的数据。 语法如下所示：

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

将 Azure 存储帐户与 HDInsight 群集配合使用时，请注意以下原则：

* **连接到群集的存储帐户中的容器：** 由于在创建过程中帐户名称和密钥将与群集相关联，因此，对这些容器中的 Blob 具有完全访问权限。

* **未连接到群集的存储帐户中的公共容器或公共 blob：** 你对容器中的 blob 具有只读权限。
  
  > [!NOTE]  
  > 利用公共容器，可以获得该容器中可用的所有 Blob 的列表以及容器元数据。 利用公共 Blob，仅在知道正确 URL 时才可访问 Blob。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../storage/blobs/storage-manage-access-to-resources.md)。

* **未连接到群集的存储帐户中的私有容器：** 不能访问容器中的 blob，除非在提交 WebHCat 作业时定义存储帐户。

创建过程中定义的存储帐户及其密钥存储在群集节点上的 %HADOOP/_HOME%/conf/core-site.xml 中。 HDInsight 默认使用 core-site.xml 文件中定义的存储帐户。 可以使用 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 修改此设置。

多个 WebHCat 作业，包括 Apache Hive。 和 MapReduce、Apache Hadoop 流式处理和 Apache Pig，以及存储帐户和元数据的说明。 （对于具有存储帐户但不是元数据的 Pig，此方面目前为 true。）有关详细信息，请参阅[将 HDInsight 群集与备用存储帐户和元存储配合使用](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 可用于结构化和非结构化数据。 Blob 容器将数据存储为键值对，没有目录层次结构。 不过，键名称可以包含斜杠字符 (/)，使其看起来像存储在目录结构中的文件。 例如，Blob 的键可以是 `input/log1.txt`。 不存在实际的 `input` 目录，但由于键名称中包含斜杠字符，键看起来像一个文件路径。

## <a name="benefits-of-azure-storage"></a>Azure 存储的优点

未共置在一起的计算群集和存储资源存在隐含的性能成本。 通过在 Azure 区域中的存储帐户资源附近创建计算群集可以减少这些成本。 在此区域中，计算节点可以通过高速网络高效访问 Azure 存储中的数据。

在 Azure 存储而非 HDFS 中存储数据可带来多项优势：

* **数据重用和共享：** HDFS 中的数据位于计算群集内。 仅有权访问计算群集的应用程序才能通过 HDFS API 使用数据。 相比之下，Azure 存储中的数据可通过 HDFS API 或 Blob 存储 REST API 进行访问。 因此，可以使用更多的应用程序（包括其他 HDInsight 群集）和工具来生成和使用此类数据。

* **数据存档：** 在 Azure 存储中存储数据时，可以安全地删除用于计算的 HDInsight 群集而不会丢失用户数据。

* **数据存储成本：** 与在 Azure 存储中存储数据相比，长期将数据存储在 DFS 中的代价更高。 因为计算群集的成本高于 Azure 存储的成本。 此外，由于数据无需在每次生成计算群集时重新加载，也节省了数据加载成本。

* **弹性向外扩展：** 尽管 HDFS 提供了向外扩展文件系统，但缩放由你为群集创建的节点数量决定。 更改规模可能比在 Azure 存储中自动获得的弹性缩放功能更复杂。

* **异地复制：** 可对 Azure 存储进行异地复制。 尽管异地复制可提供地理恢复和数据冗余功能，但针对异地复制位置的故障转移将大大影响性能，并且可能会产生额外成本。 因此，请谨慎选择异地复制，并仅在数据的价值值得支付额外成本时才选择它。

某些 MapReduce 作业和包可能会产生中间结果，并不想在 Azure 存储中存储这些结果。 在此情况下，仍可以选择将数据存储在本地 HDFS 中。 HDInsight 在 Hive 作业和其他过程中会为其中某些中间结果使用 DFS。

> [!NOTE]  
> 大多数 HDFS 命令（例如 `ls`、`copyFromLocal` 和 `mkdir`）在 Azure 存储中可按预期方式工作。 只有特定于本机 HDFS 实现（称作 DFS）的命令在 Azure 存储上会显示不同的行为，例如 `fschk` 和 `dfsadmin`。

## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Storage Gen2 简介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 存储简介](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1 概述](./overview-data-lake-storage-gen1.md)