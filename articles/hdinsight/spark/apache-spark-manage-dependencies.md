---
title: 管理 Azure HDInsight 上的 Spark 应用程序依赖项
description: 本文介绍如何在 HDInsight Spark 群集中管理适用于 PySpark 和 Scala 应用程序的 Spark 依赖项。
author: yanancai
ms.author: yanacai
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: dafb4485ae9b10d89fa36bd790dcf3a799054de3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064101"
---
# <a name="manage-spark-application-dependencies"></a>管理 Spark 应用程序依赖关系

本文介绍如何管理 HDInsight 上运行的 Spark 应用程序的依赖关系。 我们涵盖 Spark 应用程序和群集范围内的 Scala 和 PySpark。

使用快速链接跳转到基于用户案例的部分：
* [使用 Jupyter 笔记本设置 Spark 作业 jar 依赖项](#use-jupyter-notebook)
* [使用 Use Azure Toolkit for IntelliJ 设置 Spark 作业 jar 依赖项](#use-azure-toolkit-for-intellij)
* [为 Spark 群集配置 jar 依赖项](#jar-libs-for-cluster)
* [安全管理 jar 依赖项](#safely-manage-jar-dependencies)
* [使用 Jupyter 笔记本设置 Spark 作业 Python 包](#use-jupyter-notebook-1)
* [安全管理 Spark 群集的 Python 包](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>用于一个 Spark 作业的 Jar 库
### <a name="use-jupyter-notebook"></a>使用 Jupyter 笔记本
当 Spark 会话在 Scala 的 Spark 内核 Jupyter Notebook 上启动时，可以从以下项配置包：

* [Spark 包](https://spark-packages.org/)上的[Maven 存储库](https://search.maven.org/)或社区提供的包。
* 存储在群集的主存储上的 Jar 文件。

我们将使用 `%%configure` magic 将笔记本配置为使用外部包。 在使用外部包的笔记本中，确保在第一个代码单元中调用 `%%configure` magic。 这可以确保将内核配置为在启动会话之前使用该包。

>
>[!IMPORTANT]  
>如果忘记了在第一个单元中配置内核，可以结合 `-f` 参数使用 `%%configure`，但这会重新启动会话，导致所有进度都会丢失。

**Maven 存储库中的包或 Spark 包的示例**

从 Maven 存储库中找到包后，收集 **GroupId**、 **ArtifactId**和 **Version**的值。 串连这三个值并以冒号分隔 ( **:** )。

   ![连接包架构](./media/apache-spark-manage-dependencies/spark-package-schema.png "连接包架构")

请确保收集的值与群集相匹配。 在这种情况下，我们将使用 Spark Cosmos DB connector package for Scala 2.11，将 spark 2.3 for HDInsight 3.6 Spark 群集。 如果不确定，请 `scala.util.Properties.versionString` 在 Spark 内核上的代码单元中运行以获取群集 Scala 版本。 运行 `sc.version` 以获取群集 Spark 版本。

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**主存储上存储的 Jar 示例**

在群集的主存储上使用用于 jar 文件的 [URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 。 对于 Azure 存储，这将是 `wasb://`，对于 Azure Data Lake Storage Gen2，这将是 `abfs://`，对于 Azure Data Lake Storage Gen1，这将是 `adl://`。 如果为 Azure 存储或 Data Lake Storage Gen2 启用安全传输，则 URI 将为 `wasbs://` 或 `abfss://` 。 请参阅 [安全传输](../../storage/common/storage-require-secure-transfer.md)。

使用逗号分隔的多个 jar 文件的 jar 路径列表，允许 Glob。 这些 jar 包含在驱动程序和执行器类路径中。

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

配置外部包后，可以在代码单元中运行导入，以验证是否已正确放置包。

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>使用 Azure Toolkit for IntelliJ
[Azure Toolkit for IntelliJ 插件](./apache-spark-intellij-tool-plugin.md) 提供 UI 体验，以便将 Spark Scala 应用程序提交到 HDInsight 群集。 它提供 `Referenced Jars` 和 `Referenced Files` 属性以在提交 Spark 应用程序时配置 jar 库路径。 有关 [如何使用 HDInsight Azure Toolkit for IntelliJ 插件的](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)更多详细信息，请参阅。

![“Spark 提交”对话框](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>群集的 Jar 库
在某些情况下，你可能希望在群集级别配置 jar 依赖项，以便默认情况下每个应用程序都可以使用相同的依赖项进行设置。 方法是将 jar 路径添加到 Spark 驱动程序和执行程序类路径。

1. 运行下面的示例脚本操作，将 jar 文件从主存储复制 `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` 到群集本地文件系统 `/usr/libs/sparklibs` 。 在 linux 使用 `:` 来分隔类路径列表时，需要执行此步骤，但 HDInsight 只支持使用类似的方案的存储路径 `wasb://` 。 如果将远程存储路径直接添加到类路径，则该路径将无法正常运行。

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. 更改 Ambari 中的 Spark 服务配置，以更新类路径。 请参阅 **Ambari > Spark > 配置 > 自定义 custom-spark2-defaults 默认值**。 按如下所示**添加属性**。 如果要添加多个路径，请使用 `:` 分隔路径。 允许使用 glob。

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![更改 Spark 默认配置](./media/apache-spark-manage-dependencies/change-spark-default-config.png "更改 Spark 默认配置")

3. 保存更改的配置并重新启动受影响的服务。

   ![重新启动受影响的服务](./media/apache-spark-manage-dependencies/restart-impacted-services.png "重新启动受影响的服务")

可以使用 [脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)自动执行这些步骤。 用于 [添加 Hive 自定义库](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) 的脚本操作是一个很好的参考。 更改 Spark 服务配置时，请确保使用 Ambari Api，而不是直接修改配置文件。 

## <a name="safely-manage-jar-dependencies"></a>安全管理 jar 依赖项
HDInsight 群集具有内置的 jar 依赖项，并且这些 jar 版本的更新会随时发生。 若要避免内置 jar 和所需的 jar 的版本冲突，请考虑为 [应用程序依赖关系着色](./safely-manage-jar-dependency.md)。

## <a name="python-packages-for-one-spark-job"></a>一个 Spark 作业的 Python 包
### <a name="use-jupyter-notebook"></a>使用 Jupyter 笔记本
HDInsight Jupyter 笔记本 PySpark 内核不支持直接从 PyPi 或 Anaconda 包存储库安装 Python 包。 如果有 `.zip` 、 `.egg` 或 `.py` 依赖项，并且想要为一个 Spark 会话引用它们，请执行以下步骤：

1. 运行下面的示例脚本操作 `.zip` ，将 `.egg` 主存储中的文件复制 `.py` `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` 到群集本地文件系统 `/usr/libs/pylibs` 。 在 linux 使用 `:` 来分隔搜索路径列表时，需要执行此步骤，但 HDInsight 只支持使用类似的方案的存储路径 `wasb://` 。 使用时，远程存储路径将无法正常运行 `sys.path.insert` 。

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. 在笔记本中，使用 PySpark 内核在代码单元中运行以下代码：

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. 运行 `import` 以检查是否已成功包含包。  

## <a name="python-packages-for-cluster"></a>群集的 Python 包
可以使用 conda 命令通过脚本操作将 Python 包从 Anaconda 安装到群集。 安装的包位于群集级别，适用于所有应用程序。 

HDInsight Spark 群集提供了两个内置的 Python 安装，Anaconda Python 2.7 和 Anaconda Python 3.5。 若要详细了解服务的默认 Python 设置，以及如何在不中断群集的情况下安全地安装外部 Python 包，请参阅 [安全管理群集的 Python 依赖项](./apache-spark-python-package-installation.md)中的更多详细信息。
