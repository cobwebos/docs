---
title: "R Server on HDInsight 入门 - Azure | Microsoft Docs"
description: "了解如何在包含 R Server 的 HDInsight 群集上创建 Apache Spark，并在群集上提交 R 脚本。"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.openlocfilehash: e688068efb41cdccbeb23de3c8ad7a09021e5b3f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>R Server on HDInsight 入门

Azure HDInsight 提供可集成到 HDInsight 群集中的 R Server 选项。 此选项允许 R 脚本使用 Spark 和 MapReduce 运行分布式计算。 本文介绍如何创建 R Server on HDInsight 群集， 然后介绍如何运行 R 脚本，演示如何使用 Spark 进行分布式 R 计算。


## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：在开始学习本教程之前，必须有 Azure 订阅。 有关详细信息，请参阅[获取 Microsoft Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **安全外壳 (SSH) 客户端**：SSH 客户端可用于远程连接到 HDInsight 群集，并直接在群集上运行命令。 有关详细信息，请参阅 [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。
* **SSH 密钥（可选）**：可以使用密码或公钥来保护用于连接到群集的 SSH 帐户。 使用密码会更方便，不需要创建公钥/私钥对即可开始操作。 但是，使用密钥更加安全。

  > [!NOTE]
  > 本文中的步骤假设你使用密码。


## <a name="automate-cluster-creation"></a>自动创建群集

可以使用 Azure 资源管理器模板、SDK 和 PowerShell 自动创建 HDInsight R Server 实例。

* 若要使用 Azure 资源管理器模板创建 R Server 实例，请参阅 [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/)（部署 R-server HDInsight 群集）。
* 若要使用 .NET SDK 创建 R Server 实例，请参阅[使用 .NET SDK 在 HDInsight 中创建基于 Linux 的群集](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)。
* 若要使用 PowerShell 部署 R Server，请参阅[使用 Azure PowerShell 在 HDInsight 中创建基于 Linux 的群集](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)。


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>使用 Azure 门户创建群集

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“新建” > “智能 + 分析” > “HDInsight”。

    ![创建新群集的图像](./media/r-server-get-started/newcluster.png)

3. 在“快速创建”体验的“群集名称”框中输入群集的名称。 如果有多个 Azure 订阅，请使用“订阅”框选择要使用的订阅。

    ![群集名称和订阅选项](./media/r-server-get-started/clustername.png)

4. 选择“群集类型”，打开“群集配置”窗格。 在“群集配置”窗格中选择以下选项：

    * **群集类型**：选择“R Server”。
    * **版本**：选择要在群集上安装的 R Server 版本。 当前可用的版本是 R Server 9.1 (HDI 3.6)。 可在 [MSDN](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) 上获取 R Server 可用版本的发行说明。
    * **R Server 的 R Studio 社区版**：此基于浏览器的 IDE 默认安装在边缘节点上。 如果不想安装它，则清除复选框。 如果选择安装它，则可在创建群集后，在群集的门户应用程序窗格中找到用于访问 RStudio Server 登录界面的 URL。
    * 将其他选项保留为默认值，然后使用“选择”按钮保存群集类型。

        ![“群集类型”窗格的屏幕截图](./media/r-server-get-started/clustertypeconfig.png)

5. 在“基本信息”窗格的“群集登录用户名”和“群集登录密码”框中，分别输入群集的用户名和密码。

6. 在“安全外壳(SSH)用户名”框中，指定 SSH 用户名。 SSH 用于通过 SSH 客户端远程连接到群集。 可在此框中指定 SSH 用户，也可在创建群集之后指定（在群集的“配置”选项卡上进行）。
   
   > [!NOTE] 
   > R Server 配置为要求使用 SSH 用户名“remoteuser”。 如果使用其他用户名，则必须在创建群集后执行附加步骤。

7. 将“使用与群集登录相同的密码”复选框保留为选中状态，以使用“密码”作为身份验证类型（除非更喜欢使用公钥）。 若要通过远程客户端（例如针对 Visual Studio 的 R 工具、RStudio 或其他桌面 IDE）访问群集上的 R Server，则需使用公钥/私钥对。 如果安装 RStudio Server 社区版，则需要选择 SSH 密码。     

   若要创建和使用公钥/私钥对，请清除“使用与群集登录相同的密码”。 然后选择“公钥”并继续，如下所示。 这些说明假设用户已安装包含 ssh-keygen 的 Cygwin 或同等组件。

   a. 在便携式计算机上通过命令提示符生成公钥/私钥对：

        ssh-keygen -t rsa -b 2048

   b. 按照提示对密钥文件进行命名，并输入密码以提高安全性。 屏幕应如下图所示：

      ![Windows 中的 SSH 命令行](./media/r-server-get-started/sshcmdline.png)

      此命令创建一个私钥文件，以及一个名为 <private-key-filename>.pub 的公钥文件。 在此示例中，这两个文件为 furiosa 和 furiosa.pub：

      ![dir 命令的示例结果](./media/r-server-get-started/dir.png)

   c. 在分配 HDI 群集凭据时指定公钥文件 (&#42;.pub)。 确认资源组和区域，然后选择“下一步”。

      ![“凭据”窗格](./media/r-server-get-started/publickeyfile.png)  

   d.单击“下一步”。 在笔记本电脑上更改对私钥文件的权限：

        chmod 600 <private-key-filename>

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 结合使用私钥文件和 SSH 进行远程登录：

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      或者，使用私钥文件作为客户端上 R Server 的 Hadoop Spark 计算上下文的部分定义。 有关详细信息，请参阅[为 Spark 创建计算上下文](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)。

8. 可以通过“快速创建”转到“存储”窗格。 在该窗格中选择存储帐户设置，将其用于群集所用 HDFS 文件系统的主位置。 选择新的或现有的 Azure 存储帐户，或者选择现有的 Azure Data Lake Store 帐户。

    - 如果选择 Azure 存储帐户，则可以选择现有帐户，方法是先选择“选择存储帐户”，然后选择相关的帐户。 使用“选择存储帐户”部分中的“新建”链接创建新帐户。

      > [!NOTE]
      > 如果选择“新建”，则必须输入新存储帐户的名称。 如果接受使用该名称，会出现绿色复选标记。

      “默认容器”默认为群集名称。 请保留此默认值。

      如果选择了新的存储帐户，请使用提示窗口中的“位置”来选择其区域。  

         ![“数据源”窗格](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > 选择默认数据源位置的同时会设置 HDInsight 群集的位置。 群集和默认数据源必须位于同一区域。

    - 若要使用现有的 Data Lake Store 帐户，请选择要使用的帐户。 然后向群集添加群集 *ADD* 标识，允许访问该存储。 有关此过程的详细信息，请参阅[使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)。

    使用“选择”按钮保存数据源配置。


9. 然后会显示“摘要”窗格，用于验证所有设置。 可以在此处更改群集大小以修改群集中的服务器数。 还可以指定要运行的任何脚本操作。 除非确定需要更大的群集，否则请保留辅助角色节点数目的默认值 **4**。 此窗格还显示群集的估计成本。

    ![群集摘要](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > 以后如果需要，可以通过门户调整群集大小（“群集” > “设置” > “缩放群集”），增加或减少辅助角色节点的数目。 这种调整大小的方法可将不使用的群集置于空闲状态，或者增加容量来满足更大任务的需要。
   >
   >

   调整群集、数据节点和边缘节点的大小时，需要注意的因素包括：  

   * 如果数据较大，则 Spark 上的分布式 R Server 分析的性能与辅助角色节点数目成正比。  

   * R Server 分析的性能与所要分析的数据大小呈线性关系。 例如：  

     * 对于小到中型数据，在边缘节点的本地计算上下文中进行数据分析的性能最佳。 若要详细了解本地计算上下文和 Spark 计算上下文在哪种方案中性能最佳，请参阅 [R Server on HDInsight 的计算上下文选项](r-server-compute-contexts.md)。<br>
     * 如果登录到边缘节点并运行 R 脚本，则会在边缘节点上本地执行除 ScaleR rx-functions 外的所有函数。 因此，应相应调整边缘节点的内存和核心数。 如果通过便携式计算机使用 HDI 上的 R Server 作为远程计算上下文，则这一点同样适用。

   使用“选择”按钮保存节点定价配置。

   ![节点定价层的窗格](./media/r-server-get-started/pricingtier.png)

   还有一个下载模板和参数的链接。 选择此链接所显示的脚本可用于自动创建具有所选配置的群集。 创建群集后，这些脚本还可从群集的 Azure 门户条目获得。

   > [!NOTE]
   > 创建群集需要一些时间，通常约 20 分钟左右。 若要查看创建过程，请使用启动板上的磁贴或页面左侧的“通知”项。
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>连接到 RStudio Server

如果已选择在安装中包括 RStudio Server 社区版，则可通过两种方法访问 RStudio 登录：

- 转到以下 URL（其中的 CLUSTERNAME 是创建的群集的名称）：

    https://*CLUSTERNAME*.azurehdinsight.net/rstudio/

- 在 Azure 门户中打开群集的条目，选择“R Server 仪表板”快速链接，然后选择“R Studio 仪表板”：

  ![访问 RStudio 仪表板](./media/r-server-get-started/rstudiodashboard1.png)

  ![访问 RStudio 仪表板](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> 无论使用哪种方法，首次登录时都需进行两次身份验证。 第一次身份验证时，请提供群集管理员用户 ID 和密码。 第二次提示时，请提供 SSH 用户 ID 和密码。 后续登录只需提供 SSH 用户 ID 和密码。

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>连接到 R Server 边缘节点

使用以下命令，通过 SSH 连接到 HDInsight 群集的 R Server 边缘节点：

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> 可以在 Azure 门户中找到 `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` 地址。 选择群集，然后选择“所有设置” > “应用” > “RServer”。 此时会显示边缘节点的 SSH 终结点信息。
>
> ![边缘节点 SSH 终结点的图像](./media/r-server-get-started/sshendpoint.png)
>
>

如果使用了密码来保护 SSH 用户帐户，系统会提示输入密码。 如果使用了公钥，则可能需要使用 `-i` 参数来指定匹配的私钥。 例如：

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

连接后会看到类似于下面的提示：

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>允许多个并发用户

可以启用多个并发用户，方法是：为在其上运行 RStudio Community 版本的边缘节点添加更多用户。

在创建 HDInsight 群集时，必须提供两个用户，即一个 HTTP 用户，一个 SSH 用户。

![输入群集用户和 SSH 用户的凭据](./media/r-server-get-started/concurrent-users-1.png)

- **群集登录用户名**：一个通过 HDInsight 网关进行身份验证的 HTTP 用户，该网关用于保护所创建的 HDInsight 群集。 此 HTTP 用户用于访问 Ambari UI、YARN UI 以及其他 UI 组件。
- **安全外壳 (SSH) 用户名**：一个需通过安全外壳访问群集的 SSH 用户。 此用户是适用于所有头节点、辅助角色节点和边缘节点的 Linux 系统中的用户。 因此，可以使用 SSH 访问远程群集中的任何节点。

在 Microsoft R Server on HDInsight 类型群集中使用的 RStudio Server 社区版本仅接受 Linux 用户名和密码作为登录机制， 而不支持传递令牌。 因此，如果创建了新的群集，并且想要使用 RStudio 来访问它，则需登录两次。

1. 通过 HDInsight 网关使用 HTTP 用户凭据登录： 

    ![输入 HTTP 用户凭据](./media/r-server-get-started/concurrent-users-2a.png)

2. 使用 SSH 用户凭据登录到 RStudio：
  
    ![输入 SSH 用户凭据](./media/r-server-get-started/concurrent-users-2b.png)

目前，在预配 HDInsight 群集时只能创建一个 SSH 用户帐户。 若要允许多个用户访问 Microsoft R Server on HDInsight 群集，需在 Linux 系统中创建其他用户。

由于 RStudio Server 社区版是在群集的边缘节点上运行，需执行下述三个步骤：

1. 使用创建的 SSH 用户登录到边缘节点。
2. 向边缘节点添加更多的 Linux 用户。
3. 通过已创建的用户使用 RStudio 社区版本。

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>使用创建的 SSH 用户登录到边缘节点

下载任意 SSH 工具（例如 PuTTY），然后使用现有的 SSH 用户登录。 然后，按[使用 SSH 连接到 HDInsight (Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md) 中提供的说明访问边缘节点。 R Server on HDInsight 群集的边缘节点地址为：clustername-ed-ssh.azurehdinsight.net


### <a name="add-more-linux-users-to-the-edge-node"></a>向边缘节点添加更多的 Linux 用户

若要将用户添加到边缘节点，请运行以下命令：

    sudo useradd yournewusername -m
    sudo passwd yourusername

此时会看到以下项返回： 

![sudo 命令的输出](./media/r-server-get-started/concurrent-users-3.png)

系统提示输入当前的 Kerberos 密码时，直接选择 Enter 键将其忽略即可。 `useradd` 命令中的 `-m` 选项指示系统会为用户创建主文件夹。 该文件夹是 RStudio 社区版本所需的。


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>通过已创建的用户使用 RStudio 社区版本

使用已创建的用户登录到 RStudio：

![RStudio 登录页](./media/r-server-get-started/concurrent-users-4.png)

请注意，RStudio 指示你在使用新用户（例如，此处为 sshuser6）登录到群集： 

![RStudio 命令栏中新用户的位置](./media/r-server-get-started/concurrent-users-5.png)

也可同时使用原始凭据（默认为 sshuser）从其他浏览器窗口登录。

可以使用 ScaleR 函数提交作业。 下面是用来运行作业的命令的示例：

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder
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

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context
    mySparkCluster <- RxSpark()

    # Set the compute context
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary
    summary(modelSpark)


请注意，在 YARN UI 中，提交的作业使用其他用户名：

![YARN UI 中的用户列表](./media/r-server-get-started/concurrent-users-6.png)

另请注意，新添加的用户在 Linux 系统中没有根权限， 但在访问远程 HDFS 文件系统和 Blob 存储中的所有文件时，其权限是相同的。


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>使用 R 控制台

1. 在 SSH 会话中，使用以下命令启动 R 控制台：  

        R

2. 应该会看到与下面类似的输出：
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.
        >

3. 可以通过 `>` 提示符输入 R 代码。 R Server 包含可以轻松地与 Hadoop 交互并运行分布式计算的包。 例如，若要查看 HDInsight 群集的默认文件系统根目录，可使用以下命令：

        rxHadoopListFiles("/")

4. 也可使用 Blob 存储样式的寻址：

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>从 Microsoft R Server 或 Microsoft R Client 的远程实例使用 R Server on HDI

可从台式机或笔记本电脑上运行的 Microsoft R Server 或 Microsoft R Client 远程实例设置对 HDI Hadoop Spark 计算上下文的访问。 有关详细信息，请参阅[创建 Spark 计算上下文](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md)中的“使用 Microsoft R Server 作为 Hadoop 客户端”部分。 为此，请在笔记本电脑上定义 RxSpark 计算上下文时指定以下选项：hdfsShareDir、shareDir、sshUsername、sshHostname、sshSwitches 和 sshProfileScript。 下面是一个示例：


    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )


## <a name="use-a-compute-context"></a>使用计算上下文

借助计算上下文，你可以控制是在边缘节点上本地执行计算，还是将计算分布到 HDInsight 群集的节点中。

1. 在 RStudio Server 或 R 控制台（在 SSH 会话中）中，使用以下代码将示例数据加载到 HDInsight 的默认存储：

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
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

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. 创建一些数据信息并定义两个数据源，以便使用数据：

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. 使用本地计算上下文对数据运行逻辑回归：

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    应会看到结尾类似于以下行的输出：

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. 使用 Spark 上下文运行相同的逻辑回归。 Spark 上下文将处理进程分布到 HDInsight 群集的所有辅助角色节点上。

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > 也可以使用 MapReduce 将计算分布到群集节点之间。 有关计算上下文的详细信息，请参阅[适用于 R Server on HDInsight 的计算上下文选项](r-server-compute-contexts.md)。


## <a name="distribute-r-code-to-multiple-nodes"></a>将 R 代码分布到多个节点

使用 R Server，可轻松利用现有 R 代码并使用 `rxExec` 跨多个群集节点运行代码。 执行参数扫描或模拟时，可以使用此函数。 以下代码是 `rxExec` 的用法示例：

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

如果仍在使用 Spark 或 MapReduce 上下文，此命令将针对运行代码 `(Sys.info()["nodename"])` 的辅助角色节点返回 `nodename` 值。 例如，在四节点群集上，输出应如下所示：

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="access-data-in-hive-and-parquet"></a>访问 Hive 和 Parquet 中的数据

通过 R Server 9.1 中提供的功能，可直接访问 Hive 和 Parquet 中的数据，以供 Spark 计算上下文中的 ScaleR 函数使用。 这些功能通过新的名为 RxHiveData 和 RxParquetData 的 ScaleR 数据源函数提供。 这些函数在运行时，通过 Spark SQL 将数据直接加载到 Spark DataFrame 中供 ScaleR 分析。  

以下代码通过一些示例演示了如何使用新函数：

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


有关这些新函数的详细信息，请使用 `?RxHivedata` 和 `?RxParquetData` 命令查看 R Server 的联机帮助。  


## <a name="install-additional-r-packages-on-the-edge-node"></a>在边缘节点上安装其他 R 包

若要在边缘节点上安装其他 R 包，可以在通过 SSH 连接到边缘节点时，直接从 R 控制台内部使用 `install.packages()`。 但是，如果需要在群集的辅助角色节点上安装 R 包，则必须使用脚本操作。

脚本操作为 Bash 脚本，用于更改 HDInsight 群集的配置或安装其他软件（例如其他 R 包）。 若要使用脚本操作安装其他包，请按以下步骤操作。

> [!IMPORTANT]
> 只有在创建群集之后，才可以使用脚本操作来安装其他 R 包。 请勿在群集创建期间使用此过程，因为脚本依赖于已安装并配置完毕的 R Server。
>
>

1. 在 [Azure 门户](https://portal.azure.com)中，选择 HDInsight 群集上的 R Server。

2. 在“设置”窗格中，选择“脚本操作” > “提交新项”。

   ![脚本操作窗格的图像](./media/r-server-get-started/scriptaction.png)

3. 在“提交脚本操作”窗格中提供以下信息：

   * **名称**：用于标识此脚本的友好名称。

   * **Bash 脚本 URI**：`http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **头**：此项应清除。

   * **辅助角色**：此项应清除。

   * **Zookeeper**：此项应清除。

   * **边缘节点**：此项应选中。

   * **参数**：要安装的 R 包，例如 `bitops stringr arules`。

   * **保留此脚本**：此项应选中。  

   > [!NOTE]
   > 默认情况下，将从与安装的 R Server 版本一致的 Microsoft R 应用程序网络存储库快照中安装所有 R 包。 若要安装较新版的包，则存在不兼容的风险。 不过，将 `useCRAN` 指定为包列表的第一个元素（例如 `useCRAN bitops, stringr, arules`）即可完成此类安装。  
   > 
   > 某些 R 包需要额外的 Linux 系统库。 为方便起见，我们已预先安装了最流行的 100 个 R 包所需的依赖项。 如果安装的 R 包需要除此之外的库，则必须下载此处使用的基本脚本，并添加安装系统库的步骤。 接下来，必须将修改的脚本上传到 Azure 存储中的公共 Blob 容器，并使用修改的脚本来安装包。
   >
   > 有关开发脚本操作的详细信息，请参阅[脚本操作开发](../hdinsight-hadoop-script-actions-linux.md)。  
   >
   >

   ![添加脚本操作](./media/r-server-get-started/submitscriptaction.png)

4. 选择“创建”运行脚本。 脚本完成后，可在所有辅助角色节点上使用 R 包。


## <a name="configure-microsoft-r-server-operationalization"></a>配置 Microsoft R Server 操作化

完成数据建模后，可以操作模型以进行预测。 若要配置 Microsoft R Server 操作化，请执行以下步骤：

1. 对边缘节点使用 `ssh` 命令，例如： 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. 更改相关版本的目录，对 .dll 文件使用 `sudo dotnet` 命令： 

   对于 Microsoft R Server 9.1：

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   对于 Microsoft R Server 9.0：

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. 若要使用单机配置配置 Microsoft R Server 操作化，请执行以下操作：

   a. 选择 `Configure R Server for Operationalization`。

   b. 选择 `A. One-box (web + compute nodes)`。

   c. 输入 `admin` 用户的密码。

   ![单击操作化](./media/r-server-get-started/admin-util-one-box-.png)

4. （可选步骤）可以执行诊断测试，如下所示：

   a. 选择 `6. Run diagnostic tests`。

   b. 选择 `A. Test configuration`。

   c. 输入 `admin` 作为用户名，然后输入以前的配置步骤中的密码。

   d.单击“下一步”。 确认 `Overall Health = pass`。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 退出管理员实用程序。

   f. 退出 SSH。

   ![针对操作化的诊断](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>如果在尝试使用 Web 服务时遇到长时间的延迟，而这些服务是在 Spark 计算上下文中使用 mrsdeploy 函数创建的，则可能需要添加一些缺失的文件夹。 Spark 应用程序属于名为“rserve2”的用户，不论何时通过 mrsdeploy 函数从 Web 服务调用它。 若要解决此问题，请执行以下操作：

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


在此阶段，操作化的配置已完成。 现在可以使用 R Client 上的 mrsdeploy 包连接到边缘节点上的操作化。 然后即可使用其功能，例如[远程执行](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution)和 [Web 服务](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette)。 可能需要通过 SSH 登录设置端口转发隧道，具体取决于群集是否设置在虚拟网络上。

### <a name="r-server-cluster-on-a-virtual-network"></a>虚拟网络上的 R Server 群集

请确保允许流量通过端口 12800 到达边缘节点。 这样，便可以使用边缘节点连接到操作化功能。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


如果 `remoteLogin()` 无法连接到边缘节点，但你可以使用 SSH 连接到边缘节点，则需检查是否已正确设置允许端口 12800 上的流量的规则。 如果仍遇到此问题，则解决方法是通过 SSH 设置端口转发隧道。 有关说明，请参阅以下部分。

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>R Server 群集未设置在虚拟网络上

如果群集未设置在虚拟网络上，或者通过虚拟网络连接时遇到问题，可以使用 SSH 端口转发隧道：

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

也可在 PuTTY 上设置它：

![PuTTY SSH 连接](./media/r-server-get-started/putty.png)

SSH 会话处于活动状态后，来自计算机端口 12800 的流量会通过 SSH 会话转发到边缘节点的端口 12800。 请确保在 `remoteLogin()` 方法中使用 `127.0.0.1:12800`。 此方法通过端口转发登录到边缘节点的操作化。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>在 HDInsight 辅助角色节点上缩放 Microsoft R Server 操作化计算节点

### <a name="decommission-the-worker-nodes"></a>解除辅助角色节点的授权

Microsoft R Server 当前未通过 Yarn 管理。 如果辅助角色节点未解除授权，Yarn ResourceManager 将无法正常工作，因为它不会知道服务器所使用的资源。 为了避免这种情况，建议在扩大计算节点前解除辅助角色节点的授权。

若要解除辅助角色节点的授权，请执行以下操作：

1. 登录到 HDI 群集的 Ambari 控制台，然后选择“主机”选项卡。
2. 选择要解除授权的辅助角色节点，然后选择“操作” > “所选主机” > “主机” > “打开维护模式”。 例如，在下图中，选择了对 wn3 和 wn4 解除授权。  

   ![打开维护模式的命令的屏幕截图](./media/r-server-get-started/get-started-operationalization.png)  

3. 选择“操作” > “所选主机” > “DataNodes” > “解除授权”。
4. 选择“操作” > “所选主机” > “NodeManagers” > “解除授权”。
5. 选择“操作” > “所选主机” > “DataNodes” > “停止”。
6. 选择“操作” > “所选主机” > “NodeManagers” > “停止”。
7. 选择“操作” > “所选主机” > “主机” > “停止所有组件”。
8. 取消选择辅助角色节点并选择头节点。
9. 选择“操作” > “所选主机” > “主机” > “重启所有组件”。

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>在每个已解除授权的辅助角色节点上配置计算节点

1. 使用 SSH 连接到每个已解除授权的辅助角色节点。
2. 使用 `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll` 运行管理员实用程序。
3. 输入 `1`，选择选项 `Configure R Server for Operationalization`。
4. 输入 `c`，选择选项 `C. Compute node`。 此步骤在辅助角色节点上配置计算节点。
5. 退出管理员实用程序。

### <a name="add-compute-nodes-details-on-the-web-node"></a>在 Web 节点上添加计算节点的详细信息

将所有已解除授权的辅助角色节点配置为在计算节点上运行后，回到边缘节点，在 Microsoft R Server Web 节点的配置中添加已解除授权的辅助角色节点的 IP 地址：

1. 使用 SSH 连接到边缘节点。
2. 运行 `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`。
3. 找到 `URIs` 部分，然后添加辅助角色节点的 IP 和端口详细信息。

    ![边缘节点的命令行](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](../hdinsight-administer-use-portal-linux.md#create-clusters)。


## <a name="next-steps"></a>后续步骤

现在，你应该已了解如何创建其中包括 R Server 的 HDInsight 群集。 你还应该已了解通过 SSH 会话使用 R 控制台的基本知识。 以下主题介绍管理和使用 R Server on HDInsight 的其他方式：

* [适用于 HDInsight 上的 R Server 的计算上下文选项](r-server-compute-contexts.md)
* [适用于 R Server on HDInsight 的 Azure 存储选项](r-server-storage.md)
