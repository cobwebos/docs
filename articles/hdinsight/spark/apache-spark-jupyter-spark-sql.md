---
title: 快速入门：使用模板在 HDInsight 中创建 Spark 群集
description: 本快速入门演示如何使用资源管理器模板在 Azure HDInsight 中创建 Apache Spark 群集，并运行简单的 Spark SQL 查询。
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.custom: mvc
ms.openlocfilehash: 06d711c99a6aaffe85adf740d2041c9fcc35ac23
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628090"
---
# <a name="quickstart-create-a-spark-cluster-in-hdinsight-using-template"></a>快速入门：使用模板在 HDInsight 中创建 Spark 群集

了解如何在 Azure HDInsight 中创建 Apache Spark 群集，以及如何对 Hive 表运行 Spark SQL 查询。 通过 Apache Spark 可以使用内存处理进行快速数据分析和群集计算。 有关 Spark on HDInsight 的信息，请参阅[概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)。

在本快速入门教程中，可以使用资源管理器模板创建 HDInsight Spark 群集。 群集将 Azure 存储 Blob 用作群集存储。

> [!IMPORTANT]
> HDInsight 群集是基于分钟按比例收费，而不管用户是否正在使用它们。 请务必在使用完之后删除群集。 有关详细信息，请参阅本文的[清理资源](#clean-up-resources)部分。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-an-hdinsight-spark-cluster"></a>创建 HDInsight Spark 群集

使用 Azure 资源管理器模板创建 HDInsight Spark 群集。 可以在 [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/) 中找到该模板。 

1. 选择以下链接在新的浏览器选项卡中打开 Azure 门户中的模板：         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">部署到 Azure</a>

2. 输入以下值：

    | 属性 | 值 |
    |---|---|
    |**订阅**|选择用于创建此群集的 Azure 订阅。 用于此快速入门的订阅是 &lt;Azure subscription name>。 |
    | **资源组**|创建一个资源组或选择一个现有的资源组。 资源组用于管理项目的 Azure 资源。 用于此快速入门的新资源组名称为“myspark20180403rg”。|
    | **位置**|选择资源组的位置。 模板将此位置用于创建群集，以及用于默认群集存储。 用于此快速入门的位置为“美国东部 2”。|
    | **ClusterName**|为要创建的 HDInsight 群集输入名称。 用于此快速入门的新群集名称为“myspark20180403”。|
    | **群集登录名和密码**|默认登录名是“admin”。选择用于群集登录的密码。 用于此快速入门的登录名为“admin”。|
    | **SSH 用户名和密码**|选择用于 SSH 用户的密码。 用于此快速入门的 SSH 用户名为“sshuser”。|

    ![使用 Azure 资源管理器模板创建 HDInsight Spark 群集](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "使用 Azure 资源管理器模板在 HDInsight 中创建 Spark 群集")

3. 选择“我同意上述条款和条件”，选择“固定到仪表板”，并选择“购买”。 此时会出现标题为“正在部署模板”的新磁贴。 创建群集大约需要 20 分钟时间。 必须先创建群集，才能继续下一会话。

如果在创建 HDInsight 群集时遇到问题，可能是因为没有这样做的适当权限。 有关详细信息，请参阅[访问控制要求](../hdinsight-administer-use-portal-linux.md#create-clusters)。

## <a name="install-intellijeclipse-for-spark-application"></a>为 Spark 应用程序安装 IntelliJ/Eclipse
使用用于 IntelliJ/Eclipse 的 Azure 工具包插件开发以 Scala 编写的 Spark 应用程序，并直接从 IntelliJ/Eclipse 集成开发环境 (IDE) 将其提交到 Azure HDInsight Spark 群集。 有关详细信息，请参阅[使用 IntelliJ 创作/提交 Spark 应用程序](./apache-spark-intellij-tool-plugin.md)和[使用 Eclipse 创作/提交 Spark 应用程序](./apache-spark-eclipse-tool-plugin.md)。

## <a name="install-vscode-for-pysparkhive-applications"></a>为 PySpark/hive 应用程序安装 VSCode
了解如何使用用于 Visual Studio Code (VSCode) 的 Azure HDInsight 工具来创建和提交 Hive 批处理作业、交互式 Hive 查询、PySpark 批处理和 PySpark 交互式脚本。 可以在 VSCode 支持的平台上安装 Azure HDInsight 工具。 这些平台包括 Windows、Linux 和 macOS。 有关详细信息，请参阅[使用 VSCode 创作/提交 PySpark 应用程序](../hdinsight-for-vscode.md)。

## <a name="create-a-jupyter-notebook"></a>创建 Jupyter 笔记本

Jupyter Notebook 是支持各种编程语言的交互式笔记本环境。 通过此笔记本可以与数据进行交互、结合代码和 markdown 文本以及执行简单的可视化效果。 

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 选择“HDInsight 群集”，然后选择所创建的群集。

    ![在 Azure 门户中打开 HDInsight 群集](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. 在门户中，选择“群集仪表板”，然后选择“Jupyter Notebook”。 出现提示时，请输入群集的群集登录凭据。

   ![打开 Jupyter Notebook 来运行交互式 Spark SQL 查询](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "打开 Jupyter Notebook 来运行交互式 Spark SQL 查询")

4. 选择“新建” > “PySpark”，创建笔记本。 

   ![创建 Jupyter Notebook 来运行交互式 Spark SQL 查询](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "创建 Jupyter Notebook 来运行交互式 Spark SQL 查询")

   新笔记本随即已创建，并以 Untitled(Untitled.pynb) 名称打开。


## <a name="run-spark-sql-statements"></a>运行 Spark SQL 语句

SQL（结构化查询语言）是用于查询和定义数据的最常见、最广泛使用的语言。 Spark SQL 作为 Apache Spark 的扩展使用，可使用熟悉的 SQL 语法处理结构化数据。

1. 验证 kernel 已就绪。 如果在 Notebook 中的内核名称旁边看到空心圆，则内核已准备就绪。 实心圆表示内核正忙。

    ![HDInsight Spark 中的 Hive 查询](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "HDInsight Spark 中的 Hive 查询")

    首次启动 Notebook 时，内核在后台执行一些任务。 等待内核准备就绪。 
2. 将以下代码粘贴到一个空单元格中，然后按 **SHIFT + ENTER** 来运行这些代码。 此命令列出群集上的 Hive 表：

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    将 Jupyter Notebook 与 HDInsight Spark 群集配合使用时，会获得一个预设的 `sqlContext`，可以使用它通过 Spark SQL 来运行 Hive 查询。 `%%sql` 指示 Jupyter Notebook 使用预设 `sqlContext` 运行 Hive 查询。 该查询从默认情况下所有 HDInsight 群集都带有的 Hive 表 (hivesampletable) 检索前 10 行。 需要大约 30 秒才能获得结果。 输出如下所示： 

    ![HDInsight Spark 中的 Hive 查询](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark 中的 Hive 查询")

    每次在 Jupyter 中运行查询时，Web 浏览器窗口标题中都会显示“(繁忙)”状态和 Notebook 标题。 右上角“PySpark”文本的旁边还会出现一个实心圆。
    
2. 运行另一个查询，请查看 `hivesampletable` 中的数据。

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    屏幕在刷新后会显示查询输出。

    ![HDInsight Spark 中的 Hive 查询输出](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark 中的 Hive 查询输出")

2. 请在 Notebook 的“文件”菜单中选择“关闭并停止”。 关闭 Notebook 会释放群集资源。

## <a name="clean-up-resources"></a>清理资源
HDInsight 将数据保存在 Azure 存储或 Azure Data Lake Store 中，因此可以在未使用群集时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。 如果要立即开始[后续步骤](#next-steps)中所列的教程，可能需要保留群集。

切换回 Azure 门户，并选择“删除”。

![删除 HDInsight 群集](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "删除 HDInsight 群集")

还可以选择资源组名称，打开“资源组”页，然后选择“删除资源组”。 通过删除资源组，可以删除 HDInsight Spark 群集和默认存储帐户。

## <a name="next-steps"></a>后续步骤 

本快速入门介绍了如何创建 HDInsight Spark 群集并运行基本的 Spark SQL 查询。 转到下一教程，了解如何使用 HDInsight Spark 群集针对示例数据运行交互式查询。

> [!div class="nextstepaction"]
>[在 Spark 上运行交互式查询](./apache-spark-load-data-run-query.md)


