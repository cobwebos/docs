---
title: 从针对 Visual Studio 的 R 工具提交作业 - Azure HDInsight
description: 从本地 Visual Studio 计算机将 R 作业提交到 HDInsight 群集。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 0ae717487f1538536601c8578e744d976798bf76
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899941"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>从针对 Visual Studio 的 R 工具提交作业

[针对 Visual Studio 的 R 工具](https://www.visualstudio.com/vs/rtvs/) (RTVS) 是一个免费的开源扩展，适用于 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 和 [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) 或更高版本的社区版（免费）、专业版和企业版。 RTVS 不适用于[Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019)。

RTVS 通过提供以下工具来增强 R 工作流：[R 交互窗口](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL)、Intellisense（代码完成）、通过 ggplot2 和 ggviz 等 R 库[绘制可视化效果](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data)、[R 代码调试](https://docs.microsoft.com/visualstudio/rtvs/debugging)等等。

## <a name="set-up-your-environment"></a>设置环境

1. 安装[针对 Visual Studio 的 R 工具](https://docs.microsoft.com/visualstudio/rtvs/installation)。

    ![在 Visual Studio 2017 中安装 RTVS](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. 选择“数据科学和分析应用程序”工作负载，然后选择“R 语言支持”、“对 R 开发工具的运行时支持”和“Microsoft R Client”选项。

3. 需要有用于 SSH 身份验证的公钥和私钥。
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. 在计算机上安装 [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows)。 ML Server 提供 [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) 和 `RxSpark` 函数。

5. 安装 [PuTTY](https://www.putty.org/) 以提供计算上下文，用于从本地客户端针对 HDInsight 群集运行 `RevoScaleR` 函数。

6. 可以选择向 Visual Studio 环境应用数据科学设置，从而为 R 工具的工作区提供新的布局。
   1. 若要保存当前的 Visual Studio 设置，请使用“工具”>“导入和导出设置”命令，然后选择“导出选定的环境设置”并指定文件名。 若要还原这些设置，请使用相同的命令，并选择“导入选定的环境设置”。

   2. 转到“R 工具”菜单项，然后选择“数据科学设置...”。

       ![数据科学设置...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > 通过使用步骤 1 中的方法，也可以保存和还原个性化的数据科学家布局，而不必重复使用“数据科学设置”命令。

## <a name="execute-local-r-methods"></a>执行本地 R 方法

1. 创建 HDInsight ML 服务群集。
2. 安装 [RTVS 扩展](https://docs.microsoft.com/visualstudio/rtvs/installation)。
3. 下载[示例 zip 文件](https://github.com/Microsoft/RTVS-docs/archive/master.zip)。
4. 打开 `examples/Examples.sln` 以在 Visual Studio 中启动解决方案。
5. 在 `A first look at R` 解决方案文件夹中打开 `1-Getting Started with R.R` 文件。
6. 从文件顶部开始，按 Ctrl+Enter 将每一行发送到 R 交互窗口（一次一行）。 某些行可能需要一些时间，因为它们要安装程序包。
    * 或者，也可以选择 R 文件中的所有行 (Ctrl+A)，然后全部执行 (Ctrl+Enter) 或选择工具栏上的“交互执行”图标。
        ![交互执行](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. 在运行完脚本中的所有行以后，应该会看到类似于下面的输出：

    ![工作区](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>将作业提交到 HDInsight ML Services 群集

通过从安装有 PuTTY 的 Windows 计算机使用 Microsoft ML Server/Microsoft R Client，可以创建计算上下文，用于从本地客户端针对 HDInsight 群集运行分布式 `RevoScaleR` 函数。 使用 `RxSpark` 创建计算上下文，并指定用户名、Apache Hadoop 群集的边缘节点、SSH 交换机等等。

1. HDInsight 上的 ml 服务边缘节点地址为`CLUSTERNAME-ed-ssh.azurehdinsight.net` ， `CLUSTERNAME`其中是 ml 服务群集的名称。

1. 将以下代码粘贴到 Visual Studio 中的 R 交互窗口，并更改设置变量的值以匹配用户环境。

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
      sshHostname = mySshHostname,
      sshSwitches = mySshSwitches,
      sshProfileScript = mySshProfileScript,
      consoleOutput = TRUE,
      hdfsShareDir = myHdfsShareDir,
      shareDir = myShareDir,
      sshClientDir = mySshClientDir
    )
    
    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```
    
    ![设置 Spark 上下文](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

1. 在 R 交互窗口中执行以下命令：

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    应该会看到与下面类似的输出：

    ![rx 命令成功执行](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

1. 确保 `rxHadoopCopy` 已将 `people.json` 文件从示例数据文件夹成功复制到新创建的 `/user/RevoShare/newUser` 文件夹：

    1. 从 Azure 的 HDInsight ML Services 群集窗格中，选择左侧菜单中的“存储帐户”。

        ![存储帐户](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. 选择群集的默认存储帐户，记下容器/目录名称。

    3. 从存储帐户窗格的左侧菜单中选择“容器”。

        ![容器](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. 选择群集的容器名称，浏览到 **user** 文件夹（可能需要单击列表底部的“加载更多”），然后依次选择“RevoShare”、“newUser”。 `people.json` 文件应显示在 `newUser` 文件夹中。

        ![复制的文件](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

1. 用完当前 Apache Spark 上下文后，必须将其停止。 不能同时运行多个上下文。

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>后续步骤

* [适用于 HDInsight 上的 ML Services 的计算上下文选项](r-server-compute-contexts.md)
* [将 ScaleR 和 SparkR 合并](../hdinsight-hadoop-r-scaler-sparkr.md)提供了航班延迟预测示例。

