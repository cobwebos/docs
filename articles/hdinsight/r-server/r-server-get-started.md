---
title: ML Services on HDInsight 入门 - Azure
description: 了解如何在包含 ML Services 的 HDInsight 群集上创建 Apache Spark，并在群集上提交 R 脚本。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: get-started-article
ms.date: 06/27/2018
ms.openlocfilehash: 7965a91efe58102268f4d54275e7fa3fc4ff74d3
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617779"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Azure HDInsight 上的 ML Services 入门

可以使用 Azure HDInsight 创建 ML Services 群集。 此选项允许 R 脚本使用 Spark 和 MapReduce 运行分布式计算。 在本文中，你将学习如何在 HDInsight 上创建 ML Services 群集，以及如何运行演示了使用 Spark 进行分布式 R 计算的一个 R 脚本。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：在开始学习本教程之前，必须有 Azure 订阅。 有关详细信息，请参阅[获取 Microsoft Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **安全外壳 (SSH) 客户端**：SSH 客户端可用于远程连接到 HDInsight 群集，并直接在群集上运行命令。 有关详细信息，请参阅 [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>使用 Azure 门户创建群集

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 单击“创建资源” > “数据 + 分析” > “HDInsight”。

3. 在“基本信息”中输入以下信息：

    * **群集名称**：HDInsight 群集的名称。
    * **订阅**：选择要使用的订阅。
    * **群集登录用户名**和**群集登录密码**：通过 HTTPS 访问群集时的登录凭据。 可以使用这些凭据访问 Ambari Web UI 或 REST API 等服务。
    * **安全外壳 (SSH) 用户名**：通过 SSH 访问群集时使用的登录名。 默认情况下，密码与群集登录密码相同。
    * **资源组**：要在其中创建群集的资源组。
    * **位置**：要在其中创建群集的 Azure 区域。

        ![群集基本详细信息](./media/r-server-get-started/clustername.png)

4. 选择“群集类型”，并在“群集配置”部分设置以下值：

    * **群集类型**：ML Services

    * **操作系统**：Linux

    * **版本**：ML Server 9.3 (HDI 3.6)。 [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server) 上提供了 ML Server 9.3 的发行说明。

    * **ML Server 的 R Studio 社区版**：此基于浏览器的 IDE 默认安装在边缘节点上。 如果不想安装它，请清除相应的复选框。 如果选择安装它，请在创建群集后，在群集的门户应用程序边栏选项卡上找到用于访问 RStudio Server 登录界面的 URL。

        ![群集基本详细信息](./media/r-server-get-started/clustertypeconfig.png)

4. 选择群集类型后，使用“选择”按钮设置群集类型。 接下来，使用“下一步”按钮完成基本配置。

5. 在“存储”部分，选择或创建存储帐户。 对于本文档中的步骤，请让此部分的其他字段保留默认值。 使用“下一步”按钮保存存储配置。

    ![设置 HDInsight 的存储帐户设置](./media/r-server-get-started/cluster-storage.png)

6. 在“摘要”部分，查看群集的配置。 使用“编辑”链接更改不正确的设置。 最后，使用“创建”按钮创建群集。

    ![设置 HDInsight 的存储帐户设置](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > 创建群集可能需要 20 分钟。

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>连接到 RStudio Server

如果选择安装 RStudio Server Community Edition 作为 HDInsight 群集的一部分，可以使用以下两种方法之一访问 RStudio 登录页：

* **选项 1** - 转到以下 URL（其中的 **CLUSTERNAME** 是你创建的 ML Services 群集的名称）：

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **选项 2** - 在 Azure 门户中打开 ML 服务群集，在“快速链接”下，单击“ML Services 仪表板”。

     ![设置 HDInsight 的存储帐户设置](./media/r-server-get-started/dashboard-quick-links.png)

    从“群集仪表板”中，单击“R Studio Server”。

    ![设置 HDInsight 的存储帐户设置](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > 无论使用哪种方法，首次登录时都需进行两次身份验证。  第一次出现身份验证的提示时，请提供群集管理员用户 ID 和密码。 第二次出现身份验证的提示时，请提供 SSH 用户 ID 和密码。 后续登录只需提供 SSH 凭据。

连接后，屏幕应如以下屏幕截图所示：

![连接到 RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>运行示例作业

可以使用 ScaleR 函数提交作业。 下面是用来运行作业的命令的示例：

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>连接到群集边缘节点

本部分介绍了如何使用 SSH 连接到 ML Services HDInsight 群集的边缘节点。 若要熟悉 SSH 的用法，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

用于连接 ML Services 群集边缘节点的 SSH 命令为：

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

若要查找群集的 SSH 命令，请在 Azure 门户中单击群集名称，单击“SSH + 群集登录”，然后为“主机名”选择边缘节点。 这会显示边缘节点的 SSH 终结点信息。

![边缘节点 SSH 终结点的图像](./media/r-server-get-started/sshendpoint.png)

如果使用了密码来保护 SSH 用户帐户，系统会提示输入密码。 如果使用了公钥，则可能需要使用 `-i` 参数来指定匹配的私钥。 例如：

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

连接后，会出现类似于下面的提示：

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>使用 R 控制台

1. 在 SSH 会话中，使用以下命令启动 R 控制台：  

        R

2. 此时应会显示包含 ML Server 版本以及其他信息的输出。
    
3. 可以通过 `>` 提示符输入 R 代码。 HDInsight 上的 ML Services 包含可以轻松与 Hadoop 交互并运行分布式计算的包。 例如，若要查看 HDInsight 群集的默认文件系统根目录，可使用以下命令：

        rxHadoopListFiles("/")

4. 还可以使用 WASB 样式寻址。

        rxHadoopListFiles("wasb:///")

5. 若要退出 R 控制台，请使用以下命令：

        quit()

## <a name="automated-cluster-creation"></a>自动创建群集

可以使用 SDK 和 PowerShell 自动为 HDInsight 创建 ML Services 群集。

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* 若要使用 .NET SDK 创建 ML Services 群集，请参阅[使用 .NET SDK 在 HDInsight 中创建基于 Linux 的群集](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)。
* 若要使用 PowerShell 创建 ML Services 群集，请参阅有关[使用 Azure PowerShell 创建 HDInsight 群集](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)的文章。

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](../hdinsight-administer-use-portal-linux.md#create-clusters)。

## <a name="next-steps"></a>后续步骤

在本文中，你已学习了如何在 Azure HDInsight 中创建新的 ML Services 群集，以及有关从 SSH 会话使用 R 控制台的基础知识。 以下文章介绍了管理和使用 HDInsight 上的 ML Services 的其他方式：

* [从针对 Visual Studio 的 R 工具提交作业](r-server-submit-jobs-r-tools-vs.md)
* [管理 HDInsight 上的 ML Services 群集](r-server-hdinsight-manage.md)
* [使 HDInsight 上的 ML Services 群集开始运转](r-server-operationalize.md)
* [适用于 HDInsight 上的 ML Services 群集的计算上下文选项](r-server-compute-contexts.md)
* [适用于 HDInsight 上的 ML Services 群集的 Azure 存储选项](r-server-storage.md)
