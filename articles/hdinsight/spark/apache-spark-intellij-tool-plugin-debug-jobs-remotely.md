---
title: "用于 IntelliJ 的 Azure 工具包：在 HDInsight Spark 中远程调试应用程序 | Microsoft Docs"
description: "了解如何使用用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具通过 VPN 远程调试 HDInsight 群集上运行的 Spark 应用程序。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 3a673d2ad58f85f5437c378a1d05aa4963893df1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>使用用于 IntelliJ 的 Azure 工具包通过 VPN 在 HDInsight 中远程调试 Spark 应用程序

我们建议通过 SSH 远程调试 Spark 应用程序。 有关说明，请参阅[使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 HDInsight 群集上的 Spark 应用程序](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)。

本文提供有关如何在 HDInsight Spark 群集上使用用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具插件提交 Spark 作业，并从台式机远程调试该作业的逐步指导。 若要完成这些任务，必须执行以下概要步骤：

1. 创建站点到站点或点到站点 Azure 虚拟网络。 本文档中的步骤假设你使用站点到站点网络。
2. 在 HDInsight 中创建属于站点到站点虚拟网络一部分的 Spark 群集。
3. 验证群集头节点与台式机之间的连接。
4. 在 IntelliJ IDEA 中创建 Scala 应用程序，并对它进行配置以进行远程调试。
5. 运行和调试应用程序。

## <a name="prerequisites"></a>先决条件
* **一个 Azure 订阅**。 有关详细信息，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **HDInsight 中的 Apache Spark 群集**。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* **Oracle Java 开发工具包**。 可以从 [Oracle 网站](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)安装它。
* **IntelliJ IDEA**。 本文使用版本 2017.1。 可以从 [JetBrains 网站](https://www.jetbrains.com/idea/download/)安装它。
* **Azure Toolkit for IntelliJ 中的 HDInsight 工具**。 用于 IntelliJ 的 Azure 工具包随附了用于 IntelliJ 的 HDInsight 工具。 有关 Azure 工具包的安装方法说明，请参阅[安装用于 IntelliJ 的 Azure 工具包](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation)。
* **从 IntelliJ IDEA 登录到 Azure 订阅**。 遵照[使用用于 IntelliJ 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序](apache-spark-intellij-tool-plugin.md)中的说明操作。
* **异常解决方法**。 在 Windows 计算机上运行 Spark Scala 应用程序进行远程调试时，可能会出现异常。 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中解释了此异常，其原因是 Windows 中缺少 WinUtils.exe 文件。 若要解决此错误，必须[下载该可执行文件](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)到某个位置（例如 **C:\WinUtils\bin**）。 添加环境变量 **HADOOP_HOME**，并将其值设置为 **C\WinUtils**。

## <a name="step-1-create-an-azure-virtual-network"></a>步骤 1：创建 Azure 虚拟网络
遵照以下链接中的说明创建 Azure 虚拟网络，并验证台式机与虚拟网络之间的连接。

* [使用 Azure 门户创建具有站点到站点 VPN 连接的 VNet](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [使用 PowerShell 创建具有站点到站点 VPN 连接的 VNet](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [使用 PowerShell 配置与虚拟网络的点到站点连接](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>步骤 2：创建 HDInsight Spark 群集
我们建议额外在 Azure HDInsight 上创建属于所创建 Azure 虚拟网络一部分的 Apache Spark 群集。 请参考[在 HDInsight 中创建基于 Linux 的群集](../hdinsight-hadoop-provision-linux-clusters.md)中提供的信息。 选择在上一步骤中创建的 Azure 虚拟网络作为可选配置的一部分。

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>步骤 3：验证群集头节点与台式机之间的连接。
1. 获取头节点的 IP 地址。 打开群集的 Ambari UI。 在群集边栏选项卡中，选择“仪表板”。

    ![在 Ambari 中选择“仪表板”](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. 在 Ambari UI 中，选择“主机”。

    ![在 Ambari 中选择“主机”](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. 此时会显示头节点、工作节点和 zookeeper 节点的列表。 头节点带有 **hn*** 前缀。 选择第一个头节点。

    ![在 Ambari 中查找头节点](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. 在打开的页面底部的“摘要”窗格中，复制头节点的“IP 地址”和“主机名”。

    ![在 Ambari 中查找 IP 地址](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. 将头节点的 IP 地址和主机名添加到要从中运行和远程调试 Spark 作业的计算机上的 **hosts** 文件中。 这样，便可以使用 IP 地址和主机名来与头节点通信。

   a. 以提升的权限打开一个记事本文件。 在“文件”菜单中选择“打开”，并找到 hosts 文件的位置。 在 Windows 计算机上，该位置为 **C:\Windows\System32\Drivers\etc\hosts**。

   b. 将以下信息添加到 **hosts** 文件：

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. 在连接到 HDInsight 群集所用 Azure 虚拟网络的计算机中，验证是否能够使用该 IP 地址和主机名 ping 通两个头节点。
7. 遵照[使用 SSH 连接到 HDInsight 群集](../hdinsight-hadoop-linux-use-ssh-unix.md)中的说明，使用 SSH 连接到群集头节点。 从群集头节点，对台式机的 IP 地址执行 ping 操作。 测试是否能够连接到分配给计算机的两个 IP 地址：

    - 一个是网络连接的地址
    - 另一个是 Azure 虚拟网络的地址

8. 针对另一个头节点重复上述步骤。

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>步骤 4：使用用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具创建 Spark Scala 应用程序，并对其进行配置以远程调试
1. 打开 IntelliJ IDEA 并创建一个新项目。 在“新建项目”对话框中执行以下操作：

    ![在于 IntelliJ IDEA 中选择新建项目模板](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. 选择“HDInsight” > “Spark on HDInsight (Scala)”

    b. 选择“下一步”。
2. 在接下来显示的“新建项目”对话框中执行以下操作，并选择“完成”：

    - 输入项目名称和位置。

    - 在“项目 SDK”下拉列表中，选择适用于 Spark 2.x 群集的“Java 1.8”，或选择适用于 Spark 1.x 群集的“Java 1.7”。

    - 在“Spark 版本”下拉列表中，Scala 项目创建向导集成了 Spark SDK 和 Scala SDK 的适当版本。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”。 否则，请选择“Spark 2.x”。 本示例使用“Spark 2.0.2 (Scala 2.11.8)”。
  
   ![选择项目 SDK 和 Spark 版本](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
3. Spark 项目会自动创建一个项目。 若要查看项目，请执行以下操作：

    a. 在“文件”菜单中，选择“项目结构”。

    b. 在“项目结构”对话框中，选择“项目”查看创建的默认项目。 也可以选择加号 (**+**) 图标创建自己的项目。

   ![创建 JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


4. 将库添加到项目。 若要添加库，请执行以下操作：

    a. 在项目树中右键单击项目名称，并单击“打开模块设置”。 

    b. 在“项目结构”对话框中选择“库”，选择 (**+**) 符号，并选择“从 Maven”。

    ![添加库](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. 在“从 Maven 存储库下载库”对话框中，搜索并添加以下库：

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. 从群集头节点复制 `yarn-site.xml` 和 `core-site.xml` 并将其添加到项目。 使用以下命令来复制文件。 可以使用 [Cygwin](https://cygwin.com/install.html) 运行以下 `scp` 命令，以便从群集头节点复制文件：

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    由于已将群集头节点 IP 地址和主机名添加到台式机上的 hosts 文件，因此可按以下方式使用 `scp` 命令：

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    若要将这些文件添加到项目，请将这些文件复制到项目树中的 **/src** 文件夹下（例如 `<your project directory>\src`）。
6. 更新 `core-site.xml` 文件以进行以下更改：

   a. 替换加密的密钥。 `core-site.xml` 文件包含与群集关联的存储帐户的已加密密钥。 在已添加到项目的 `core-site.xml` 文件中，将已加密密钥替换为与默认存储帐户关联的实际存储密钥。 有关详细信息，请参阅[管理存储访问密钥](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)。

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. 从 `core-site.xml` 中删除以下条目：

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   c. 保存文件。
7. 添加应用程序的 main 类。 在“项目资源管理器”中，右键单击“src”，指向“新建”，并选择“Scala 类”。

    ![选择 main 类](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
8. 在“新建 Scala 类”对话框中提供名称，在“种类”对话框中选择“对象”，并选择“确定”。

    ![新建 Scala 类](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
9. 在 `MyClusterAppMain.scala` 文件中粘贴以下代码。 此代码创建 Spark 上下文，并从 `SparkSample` 对象打开 `executeJob` 方法。

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasb:///HVACOut")
          }
        }

10. 重复步骤 8 和 9，添加名为 `*SparkSample` 的新 Scala 对象。 将以下代码添加到此类。 此代码从 HVAC.csv（用于所有 HDInsight Spark 群集）中读取数据。 它会检索在 CSV 文件的第七列中只有一个数字的行，并将输出写入群集的默认存储容器下的 **/HVACOut**。

        import org.apache.spark.SparkContext

        object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
        }
11. 重复步骤 8 和 9，添加名为 `RemoteClusterDebugging` 的新类。 此类实现用于调试应用程序的 Spark 测试框架。 将以下代码添加到 `RemoteClusterDebugging` 类：

        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }

     需要注意几个要点：

      * 对于.`.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`，请确保 Spark 程序集 JAR 可在指定路径上的群集存储中使用。
      * 对于 `setJars`，请指定项目 JAR 的创建位置。 这通常是 `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`。
12. 在 `*RemoteClusterDebugging` 类中，右键单击 `test` 关键字，并选择“创建 RemoteClusterDebugging 配置”。

    ![创建远程配置](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

13. 在“创建 RemoteClusterDebugging 配置”对话框中，为配置提供名称，并选择选择“测试种类”作为“测试名称”。 将其他所有值保留默认设置。 依次选择“应用”、“确定”。

    ![添加配置详细信息](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
14. 现在，菜单栏中应会显示“远程运行”配置下拉列表。

    ![“远程运行”下拉列表](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>步骤 5：在调试模式下运行应用程序
1. 在 IntelliJ IDEA 项目中打开 `SparkSample.scala` 并在 `val rdd1` 旁边创建一个断点。 在“在为以下对象创建断点”弹出菜单中，选择“line in function executeJob”。

    ![添加断点](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. 若要运行应用程序，请选择“远程运行”配置下拉列表旁边的“调试运行”按钮。

    ![选择“调试运行”按钮](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. 程序执行步骤到达断点时，底部窗格中应会显示“调试程序”选项卡。

    ![查看“调试程序”选项卡](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. 若要添加监视，请选择 (**+**) 图标。

    ![选择“+”图标](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    在此示例中，应用程序在创建变量 `rdd1` 之前已中断。 使用此监视可查看变量 `rdd` 中的前五行。 按 **Enter**。

    ![在调试模式下运行程序](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    从上图可以看到，在运行时，可以查询 TB 量级的数据，并可以逐步调试应用程序。 例如，在上图显示的输出中，可以看到输出的第一行是标头。 基于此输出，可以修改应用程序代码，以根据需要跳过标头行。
5. 现在可以选择“恢复程序”图标继续运行应用程序。

    ![选择“恢复程序”](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. 如果应用程序成功完成，应会显示类似于下面的输出：

    ![控制台输出](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>后续步骤
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>演示
* 创建 Scala 项目（视频）：[Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Spark Scala 应用程序）
* 远程调试（视频）：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包远程调试 HDInsight 群集上的 Spark 应用程序）

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](../hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用用于 IntelliJ 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin Notebook](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业](apache-spark-job-debugging.md)
