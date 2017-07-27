---
title: "在 Azure HDInsight 中创建 Apache Spark 群集 | Microsoft Docs"
description: "此 HDInsight Spark 快速入门介绍如何在 HDInsight 中创建 Apache Spark 群集。"
keywords: "spark 快速入门,交互式 spark,交互式查询,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/20/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 0625984bf10588fe50a2632285f565eb79b66ab7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/22/2017

---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>在 Azure HDInsight 中创建 Apache Spark 群集

本文介绍如何在 Azure HDInsight 中创建 Apache Spark 群集。

   ![快速入门示意图，其中描述了在 Azure HDInsight 上创建 Apache Spark 群集的步骤](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "有关在 HDInsight 中使用 Apache Spark 的 Spark 快速入门。示意图中的步骤：创建群集；运行 Spark 交互式查询")

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 在开始学习本教程之前，你必须有一个 Azure 订阅。 请参阅[立即创建免费 Azure 帐户](https://azure.microsoft.com/free)。

## <a name="create-hdinsight-spark-cluster"></a>创建 HDInsight Spark 群集

在本部分中，你将使用 [Azure Resource Manager 模板](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/)创建 HDInsight Spark 群集。 有关其他群集创建方法，请参阅 [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md)（创建 HDInsight 群集）。

1. 单击下面的图像即可在 Azure 门户中打开该模板。         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 输入以下值：

    ![使用 Azure Resource Manager 模板创建 HDInsight Spark 群集](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "使用 Azure Resource Manager 模板在 HDInsight 中创建 Spark 群集")

    * **订阅**：为此群集选择你的 Azure 订阅。
    * **资源组**：创建资源组，或选择现有的资源组。 资源组用于管理项目的 Azure 资源。
    * **位置**：选择资源组的位置。 模板将此位置用于创建群集，以及用于默认群集存储。
    * ClusterName：为要创建的 HDInsight 群集输入名称。
    * Spark 版本：选择 2.0 作为要安装在群集上的版本。
    * **群集登录名和密码**：默认登录名是 admin。
    * **SSH 用户名和密码**。

   记下这些值。  本教程后面会用到它们。

3. 选择“我同意上述条款和条件”，选择“固定到仪表板”，然后单击“购买”。 此时会出现一个标题为“为模板部署提交部署”的新磁贴。 创建群集大约需要 20 分钟时间。

如果在创建 HDInsight 群集时遇到问题，可能是因为没有这样做的适当权限。 有关详细信息，请参阅[访问控制要求](hdinsight-administer-use-portal-linux.md#create-clusters)。

> [!NOTE]
> 本文创建的 Spark 群集使用 [Azure 存储 Blob 作为群集存储](hdinsight-hadoop-use-blob-storage.md)。 还可以创建使用 [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) 作为默认存储的 Spark 群集。 有关说明，请参阅 [Create an HDInsight cluster with Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)（创建包含 Data Lake Store 的 HDInsight 群集）。
>
>

## <a name="run-a-hive-query-using-spark-sql"></a>使用 Spark SQL 运行 Hive 查询

使用为 HDInsight Spark 群集配置的 Jupyter Notebook 时，会获得一个预设的 `sqlContext`，可以使用它通过 Spark SQL 来运行 Hive 查询。 本部分介绍如何启动 Jupyter Notebook 并运行基本的 Hive 查询。

1. 打开 [Azure 门户](https://portal.azure.com/)。

2. 如果已选择将群集固定到仪表板，请单击仪表板中的群集磁贴，启动群集边栏选项卡。

    如果未将群集固定到仪表板，则请单击左窗格中的“HDInsight 群集”，然后单击已创建的群集。

3. 在“快速链接”中，单击“群集仪表板”，然后单击“Jupyter 笔记本”。 出现提示时，请输入群集的管理员凭据。

   ![打开 Jupyter notebook 来运行交互式 Spark SQL 查询](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "打开 Jupyter notebook 来运行交互式 Spark SQL 查询")

   > [!NOTE]
   > 也可以在浏览器中打开以下 URL 来访问群集的 Jupyter notebook。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. 创建笔记本。 单击“新建”，然后单击“PySpark”。

   ![创建 Jupyter notebook 来运行交互式 Spark SQL 查询](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "创建 Jupyter notebook 来运行交互式 Spark SQL 查询")

   新笔记本随即已创建，并以 Untitled(Untitled.pynb) 名称打开。

4. 在顶部单击笔记本名称，然后根据需要输入友好名称。

    ![为要从中运行交互式 Spark 查询的 Jupter notebook 提供一个名称](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "为要从中运行交互式 Spark 查询的 Jupter notebook 提供一个名称")

5.  将以下代码粘贴到一个空单元格中，然后按 **SHIFT + ENTER** 来运行这些代码。 在下面的代码中，`%%sql`（称为 sql magic）指示 Jupyter Notebook 使用预设的 `sqlContext` 来运行 Hive 查询。 该查询从默认情况下可以在所有 HDInsight 群集上使用的 Hive 表 (hivesampletable) 中检索前 10 行。

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![HDInsight Spark 中的 Hive 查询](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark 中的 Hive 查询")

    有关 `%%sql` magic 和预设上下文的详细信息，请参阅[适用于 HDInsight 群集的 Jupyter 内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)。

    > [!NOTE]
    > 每次在 Jupyter 中运行查询时，Web 浏览器窗口标题中都会显示“(繁忙)”状态和 Notebook 标题。 右上角“PySpark”文本的旁边还会出现一个实心圆。 作业完成后，实心圆将变成空心圆。
    >
    >
    
6. 屏幕在刷新后会显示查询输出。

    ![HDInsight Spark 中的 Hive 查询输出](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark 中的 Hive 查询输出")

7. 运行完应用程序后，可以关闭 Notebook 以释放资源。 为此，请在笔记本的“文件”菜单中，单击“关闭并停止”。

## <a name="next-step"></a>后续步骤

本文介绍了如何创建 HDInsight Spark 群集并运行基本的 Spark SQL 查询。 现在请学习如何[将数据加载到 HDInsight Spark 中并运行交互式查询](hdinsight-apache-spark-load-data-run-query.md)。

如果计划稍后再完成后续步骤，请确保删除 HDInsight 群集。 

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
