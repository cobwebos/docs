---
title: 将 Azure Toolkit for IntelliJ 与 Hortonworks 沙盒配合使用
description: 了解如何将用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具与 Hortonworks 沙盒配合使用。
keywords: hadoop 工具,hive 查询,intellij,hortonworks 沙盒,用于 intellij 的 azure 工具包
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 986c2783f3d130114da93406bfaf70d5c038d5ed
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856143"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用

了解如何通过用于 IntelliJ 的 HDInsight 工具开发 Apache Scala 应用程序，并在计算机上运行的 [Hortonworks 沙盒](https://hortonworks.com/products/sandbox/)上测试应用程序。 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) 是一种 Java 集成开发环境 (IDE)，用于开发计算机软件。 在 Hortonworks 沙盒上开发并测试应用程序以后，即可将应用程序移至 [Azure HDInsight](apache-hadoop-introduction.md)。

## <a name="prerequisites"></a>必备条件

在开始阅读本文前，必须具有以下项目：

- 基于 Hortonworks 沙盒的 Hortonworks 数据平台 (HDP) 2.4，运行在本地计算机中。 若要设置 HDP，请参阅[通过虚拟机上的 Hadoop 沙盒了解 Apache Hadoop 生态系统](apache-hadoop-emulator-get-started.md)。 
    > [!NOTE]
    > 用于 IntelliJ 的 HDInsight 工具只使用 HDP 2.4 测试过。 若要获取 HDP 2.4，请在 **Hortonworks 沙盒下载站点**中，展开 [Hortonworks 沙盒存档](https://hortonworks.com/downloads/#sandbox)。

- [Java 开发人员工具包 (JDK) 1.8 或更高版本](https://aka.ms/azure-jdks)。 用于 IntelliJ 的 Azure 工具包需要 JDK。

- [IntelliJ IDEA 社区版](https://www.jetbrains.com/idea/download)，带 [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) 插件和[用于 IntelliJ 的 Azure 工具包](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij)插件。 用于 IntelliJ 的 HDInsight 工具作为用于 IntelliJ 的 Azure 工具包的一部分提供。

安装插件的步骤：

  1. 打开 IntelliJ IDEA。
  2. 在“欢迎”页上，依次选择“配置”、“插件”。   
  3. 选择左下角的“安装 JetBrains 插件”  。
  4. 使用搜索功能搜索 **Scala**，并选择“安装”。 
  5. 若要完成安装，请选择“重启 IntelliJ IDEA”  。
  6. 重复步骤 4 和步骤 5，安装**用于 IntelliJ 的 Azure 工具包**。 有关详细信息，请参阅[安装用于 IntelliJ 的 Azure 工具包](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)。

## <a name="create-an-apache-spark-scala-application"></a>创建 Apache Spark Scala 应用程序

本部分使用 IntelliJ IDEA 创建示例 Scala 项目。 在下一部分，用户在提交项目之前，需将 IntelliJ IDEA 链接到 Hortonworks 沙盒（仿真程序）。

1. 在计算机上打开 IntelliJ IDEA。 在“新建项目”对话框中，完成以下步骤  ：

   1. 选择“HDInsight”   >   “Spark on HDInsight (Scala)”
   2. 在“生成工具”  列表中，基于方案选择以下项之一：

      * **Maven**：用于支持 Scala 项目创建向导。
      * **SBT**：用于管理依赖项和生成 Scala 项目。

   ![Intellij 新建 scala 项目](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. 选择“**下一页**”。
3. 在接下来显示的“新建项目”对话框中，完成以下步骤  ：

   1. 在“项目名称”框中输入项目名称。 
   2. 在“项目位置”框中输入项目位置。 
   3. 在“项目 SDK”下拉列表旁边，依次选择“新建”和“JDK”，并指定 Java JDK 1.7 或更高版本的文件夹。    为 Spark 2.x 群集选择 **Java 1.8**。 为 Spark 1.x 群集选择 **Java 1.7**。 默认位置为 C:\Program Files\Java\jdk1.8.x_xxx。
   4. 在“Spark 版本”下拉列表中，Scala 项目创建向导集成了 Spark SDK 和 Scala SDK 的正确版本。  如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”  。 否则，请选择“Spark 2.x”  。 本示例使用“Spark 1.6.2 (Scala 2.10.5)”。 请确保使用标记为 Scala 2.10.x 的存储库  。 不要使用标记为 Scala 2.11.x 的存储库。
    
      ![创建 IntelliJ Scala 项目属性](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. 选择“完成”  。
5. 如果“项目”视图尚未打开，请按 **Alt+1** 将其打开。 
6. 在“项目资源管理器”  中展开项目，并选择“src”  。
7. 右键单击“src”  ，指向“新建”  ，并选择“Scala 类”  。
8. 在“名称”  框中，输入名称。 在“类型”  框中，选择“对象”  。 选择“确定”。 

    ![“新建 Scala 类”对话框](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. 在 .scala 文件中粘贴以下代码：

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. 在“生成”  菜单中，选择“生成项目”  。 确保编译成功完成。


## <a name="link-to-the-hortonworks-sandbox"></a>Hortonworks 沙盒链接

必须先有 IntelliJ 应用程序，才能链接到 Hortonworks 沙盒（仿真器）。

若要链接到模拟器，请执行以下操作：

1. 在 IntelliJ 中打开项目。
2. 在“视图”  菜单中，依次选择“工具窗口”  、“Azure 资源管理器”  。
3. 展开“Azure”  ，右键单击“HDInsight”  ，并选择“链接仿真器”  。
4. 在“链接新模拟器”  对话框中，输入为 Hortonworks 沙盒的根帐户设定的密码。 接下来，输入类似于以下屏幕截图中所使用的值。 选择“确定”。  

   ![“链接新模拟器”对话框](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. 若要配置仿真器，请选择“是”。 

成功连接模拟器后，模拟器（Hortonworks 沙盒）会列在 HDInsight 节点中。

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>将 Spark Scala 应用程序提交到 Hortonworks 沙盒

将 IntelliJ IDEA 链接到仿真器之后，即可提交项目。

若要将项目提交到模拟器，请执行以下操作：

1. 在“项目资源管理器”  中，右键单击项目，并选择“将 Spark 应用程序提交到 HDInsight”。 
2. 请完成下列步骤：

    1. 在“Spark 群集(仅 Linux)”下拉列表中，选择本地 Hortonworks 沙盒。 
    2. 在“主类名”框中，选择或输入主类名。  对于本文，名称为**GroupByTest**。

3. 选择“提交”。  作业提交日志显示在“Spark”提交工具窗口。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具为 HDInsight Spark Linux 群集创建 Apache Spark 应用程序](../spark/apache-spark-intellij-tool-plugin.md)。

- 有关适用于 IntelliJ 的 HDInsight 工具的视频，请参阅 [Introduce HDInsight Tools for IntelliJ for Apache Spark development](https://www.youtube.com/watch?v=YTZzYVgut6c)（介绍如何通过适用于 IntelliJ 的 HDInsight 工具进行 Apache Spark 开发）。

- 了解如何[使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 HDInsight 群集上的 Apache Spark 应用程序](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)。

- 了解如何[使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具在 HDInsight Spark Linux 群集上远程调试 Apache Spark 应用程序](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)。

- 了解如何[使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Apache Spark 应用程序](../spark/apache-spark-eclipse-tool-plugin.md)。

