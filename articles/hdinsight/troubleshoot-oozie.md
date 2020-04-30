---
title: 排查 Azure HDInsight 中的 Apache Oozie
description: 排查 Azure HDInsight 中的某些 Apache Oozie 错误。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 18831832f82cdbc8cec69e368f006f7acd4836c1
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205256"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>排查 Azure HDInsight 中的 Apache Oozie

借助 Apache Oozie UI，你可以查看 Oozie 日志。 Oozie UI 还包含一些链接，指向工作流启动的 MapReduce 任务的 JobTracker 日志。 故障排除的模式应为：

1. 在 Oozie Web UI 中查看作业。

2. 如果特定操作存在错误或失败，请选择该操作，查看**错误消息**字段是否提供了有关失败的详细信息。

3. 如果已提供，请使用操作中的 URL 查看该操作的更多详细信息（例如 JobTracker 日志）。

下面是你可能遇到的特定错误及其解决方法。

## <a name="ja009-cant-initialize-cluster"></a>JA009：无法初始化群集

### <a name="issue"></a>问题

作业状态变为“SUSPENDED”****。 作业详细信息中的 `RunHiveScript` 状态显示为“START_MANUAL”。**** 选择该操作会显示以下错误消息：

    JA009: Cannot initialize Cluster. Please check your configuration for map

### <a name="cause"></a>原因

**job.xml** 文件中使用的 Azure Blob 存储地址不包含存储容器或存储帐户名。 Blob 存储地址格式必须是 `wasbs://containername@storageaccountname.blob.core.windows.net`。

### <a name="resolution"></a>解决方法

更改作业使用的 Blob 存储地址。

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002：不允许 Oozie 模拟&lt;用户&gt;

### <a name="issue"></a>问题

作业状态变为“SUSPENDED”****。 作业详细信息中的 `RunHiveScript` 状态显示为“START_MANUAL”。**** 选择操作会显示以下错误消息：

    JA002: User: oozie is not allowed to impersonate <USER>

### <a name="cause"></a>原因

当前的权限设置不允许 Oozie 模拟指定的用户帐户。

### <a name="resolution"></a>解决方法

Oozie 可以模拟组中的**`users`** 用户。 使用 `groups USERNAME` 查看用户帐户所属的组。 如果该用户不是**`users`** 该组的成员，请使用以下命令将该用户添加到该组中：

    sudo adduser USERNAME users

> [!NOTE]  
> 可能需要几分钟，HDInsight 才能识别用户已添加到该组。

---

## <a name="launcher-error-sqoop"></a>启动器错误 (Sqoop)

### <a name="issue"></a>问题

作业状态变为“KILLED”****。 作业详细信息中的 `RunSqoopExport` 状态显示为“ERROR”。**** 选择操作会显示以下错误消息：

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

### <a name="cause"></a>原因

Sqoop 无法加载访问数据库时所需的数据库驱动程序。

### <a name="resolution"></a>解决方法

从 Oozie 作业使用 Sqoop 时，必须同时包含数据库驱动程序和作业使用的其他资源（例如 workflow.xml）。 此外，通过 workflow.xml 的 `<sqoop>...</sqoop>` 节引用包含数据库驱动程序的存档。

例如，对于[使用 Hadoop Oozie 工作流](hdinsight-use-oozie-linux-mac.md)中的作业示例，你将使用以下步骤：

1. 将该`mssql-jdbc-7.0.0.jre8.jar`文件复制到 **/tutorials/useoozie**目录：

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. 修改 `workflow.xml`，在 `</sqoop>` 上方的新行中添加以下 XML：

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
