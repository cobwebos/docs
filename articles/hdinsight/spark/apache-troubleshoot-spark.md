---
title: 排查 Azure HDInsight 中 Apache Spark 的问题
description: 获取有关使用 Apache Spark 和 Azure HDInsight 的常见问题的解答。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 143a941b8266734a8415c71daafc07681bc13b80
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70049546"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 Apache Spark 进行故障排除

了解在[Apache Ambari](https://ambari.apache.org/)中使用 Apache Spark 负载时的最常见问题及其解决方法。

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>如何在群集上使用 Apache Ambari 配置 Apache Spark 应用程序？

可以优化 Spark 配置值, 帮助避免 Apache Spark 应用程序`OutofMemoryError`异常。 以下步骤显示了 Azure HDInsight 中的默认 Spark 配置值：

1. 以群集凭据登录到`https://CLUSTERNAME.azurehdidnsight.net` Ambari。 初始屏幕显示 "概述" 仪表板。 HDInsight 3.6 与4.0 之间略有不同。

1. 导航到**custom-spark2-defaults** > 配置。

    ![选择“配置”选项卡](./media/apache-troubleshoot-spark/update-config-2.png)

1. 在配置列表中, 选择并展开 " **custom-spark2-defaults-默认值**"。

1. 找到需要调整的值设置，例如 **spark.executor.memory**。 在这种情况下, **9728m**的值过高。

    ![选择 custom-spark-defaults](./media/apache-troubleshoot-spark/update-config-4.png)

1. 将值设置为建议的设置。 建议为此设置使用值 **2048m**。

1. 保存值，并保存配置。 选择**保存**。

    ![将值更改为 2048m](./media/apache-troubleshoot-spark/update-config-6a.png)

    编写有关配置更改的注释，并选择“保存”。

    ![输入有关所做更改的注释](./media/apache-troubleshoot-spark/update-config-6c.png)

    如果有任何配置需要引以注意，系统会发出通知。 记下这些项，并选择“仍然继续”。

    ![选择“仍然继续”](./media/apache-troubleshoot-spark/update-config-6b.png)

1. 每次保存配置时，系统都会提示重启服务。 选择“重启”。

    ![选择“重启”](./media/apache-troubleshoot-spark/update-config-7a.png)

    确认重启。

    ![选择“确认全部重启”](./media/apache-troubleshoot-spark/update-config-7b.png)

    可以查看正在运行的进程。

    ![查看正在运行的进程](./media/apache-troubleshoot-spark/update-config-7c.png)

1. 可以添加配置。 在配置列表中，依次选择“Custom-spark2-defaults”、“添加属性”。

    ![选择“添加属性”](./media/apache-troubleshoot-spark/update-config-8.png)

1. 定义新属性。 可以使用对话框为特定的设置（例如数据类型）定义单个属性。 或者，可以定义多个属性（每行定义一个属性）。

    在本示例中，使用值 **4g** 定义了 **spark.driver.memory** 属性。

    ![定义新属性](./media/apache-troubleshoot-spark/update-config-9.png)

1. 根据步骤 6 和 7 中所述保存配置并重启服务。

这些更改会应用到整个群集，但可以在提交 Spark 作业时将其覆盖。

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>如何在群集上使用 Jupyter Notebook 配置 Apache Spark 应用程序？

在 Jupyter Notebook 的第一个单元格中的 **%%configure** 指令后面，使用有效的 JSON 格式指定 Spark 配置。 根据需要更改实际值：

![添加配置](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>如何在群集上使用 Apache Livy 配置 Apache Spark 应用程序？

使用 cURL 等 REST 客户端将 Spark 应用程序提交到 Livy。 使用如下所示的命令。 根据需要更改实际值：

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>如何在群集上使用 spark-submit 配置 Apache Spark 应用程序？

使用如下所示的命令启动 spark-shell。 根据需要更改实际配置值：

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>其他阅读材料

[在 HDInsight 群集上提交 Apache Spark 作业](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* [Spark 内存管理概述](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [调试 HDInsight 群集上的 Spark 应用程序](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)。

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
