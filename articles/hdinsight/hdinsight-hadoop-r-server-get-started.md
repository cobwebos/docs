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
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 14e2a14c74e00709e18a80325fbdd3cbcd71da37
ms.contentlocale: zh-cn
ms.lasthandoff: 08/15/2017

---
# <a name="get-started-using-r-server-on-hdinsight"></a>开始使用 R Server on HDInsight

HDInsight 提供可集成到 HDInsight 群集中的 R Server 选项。 此选项允许 R 脚本使用 Spark 和 MapReduce 运行分布式计算。 在本文档中，可以了解如何在 HDInsight 群集上创建 R Server，并运行 R 脚本，以演示如何使用 Spark 进行分布式 R 计算。


## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：在开始学习本教程之前，必须有 Azure 订阅。 请参阅[获取 Microsoft Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)一文了解详细信息。
* **安全外壳 (SSH) 客户端**：SSH 客户端可用于远程连接到 HDInsight 群集，并直接在群集上运行命令。 有关详细信息，请参阅[对 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。
* **SSH 密钥（可选）**：可以使用密码或公钥来保护用于连接群集的 SSH 帐户。 使用密码会更方便，因为不需要创建公钥/私钥对即可着手。 但是，使用密钥更加安全。

> [!NOTE]
> 本文档中的步骤假设使用密码。


## <a name="automated-cluster-creation"></a>自动创建群集

可以使用 Azure Resource Manager 模板、SDK 和 PowerShell 自动创建 HDInsight R Server。

* 若要使用 Azure 资源管理模板创建 R Server，请参阅 [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/)（部署 R-server HDInsight 群集）。
* 若要使用 .NET SDK 创建 R Server，请参阅[使用 .NET SDK 在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)。
* 若要使用 PowerShell 部署 R Server，请参阅[使用 PowerShell 在创建 R Server on HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) 一文。


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>使用 Azure 门户创建群集

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“新建” -> “智能 + 分析”->“HDInsight”。

    ![创建新群集的图像](./media/hdinsight-hadoop-r-server-get-started/newcluster.png)

3. 在“快速创建”体验中，在“群集名称”字段中输入群集的名称。 如果有多个 Azure 订阅，请使用“订阅”条目选择要使用的订阅。

    ![群集名称和订阅选项](./media/hdinsight-hadoop-r-server-get-started/clustername.png)

4. 选择“群集类型”以打开“群集配置”边栏选项卡。 在“群集配置”边栏选项卡上选择以下选项：

    * **群集类型**：R Server
    * **版本**：选择要在群集上安装的 R Server 版本。 当前可用的版本是 R Server 9.1 (HDI 3.6)。 可在[此处](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes)获取 R Server 可用版本的发行说明。
    * **R Server 的 R Studio 社区版**：此基于浏览器的 IDE 默认安装在边缘节点上。 如果不想安装它，则取消勾选复选框。 如果选择安装它，则可在创建群集后，在群集的门户应用程序边栏选项卡上找到用于访问 RStudio Server 登录界面的 URL。
    * 将其他选项保留为默认值，并使用“选择”按钮保存群集类型。

        ![群集类型边栏选项卡截图](./media/hdinsight-hadoop-r-server-get-started/clustertypeconfig.png)

5. 输入“群集登录用户名”和“群集登录密码”。

    指定“SSH 用户名”。 SSH 用于通过**安全外壳 (SSH)** 客户端远程连接到群集。 可在此对话框中指定 SSH 用户，也可在创建群集之后指定（在群集的“配置”选项卡中进行）。 R Server 配置为要求使用 **SSH 用户名**“remoteuser”。  如果使用其他用户名，则必须在创建群集后执行附加步骤。

    将“使用与群集登录相同的密码”对应的框保留为选中状态，以使用“密码”作为身份验证类型（除非更喜欢使用公钥）。  若要通过远程客户端（例如 RTVS、RStudio 或其他桌面 IDE）访问群集上的 R Server，则需使用公钥/私钥对。 如果安装 RStudio Server 社区版，则需要选择 SSH 密码。     

    若要创建和使用公钥/私钥对，请取消选中“使用与群集登录相同的密码”，然后选择“公钥”并继续，如下所示。 这些说明假设用户已安装包含 ssh-keygen 的 Cygwin 或同等组件。

    * 在便携式计算机上通过命令提示符生成公钥/私钥对：

        ssh-keygen -t rsa -b 2048

    * 按照提示对密钥文件进行命名，并输入密码以提高安全性。 屏幕应如下图所示：

        ![Windows 中的 SSH 命令行](./media/hdinsight-hadoop-r-server-get-started/sshcmdline.png)

    * 此命令创建一个私钥文件，以及一个名为 <private-key-filename>.pub 的公钥文件，例如 furiosa 和 furiosa.pub。

        ![SSH dir](./media/hdinsight-hadoop-r-server-get-started/dir.png)

    * 然后在分配 HDI 群集凭据时指定公钥文件 (*.pub)，最后确认资源组和区域并选择“下一步”。

        ![凭据边栏选项卡](./media/hdinsight-hadoop-r-server-get-started/publickeyfile.png)  

   * 在笔记本电脑上更改对私钥文件的权限：

        chmod 600 <private-key-filename>

   * 结合使用私钥文件和 SSH 进行远程登录：

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      或者，作为客户端上 R Server 的 Hadoop Spark 计算上下文的部分定义。 请参阅[创建 Spark 计算上下文](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark)中的**使用 Microsoft R Server 作为 Hadoop 客户端**小节。

6. 快速创建会转换到“存储”边栏选项卡以选择要用于群集使用的 HDFS 文件系统主位置的存储帐户设置。 选择新的或现有的 Azure 存储帐户或者现有的 Data Lake Storage 帐户。

    - 如果选择 Azure 存储帐户，可通过选择“选择存储帐户”，然后选择相关帐户来选择现有存储帐户。 使用“选择存储帐户”部分中的“新建”链接创建新帐户。

      > [!NOTE]
      > 如果选择“新建”，则必须输入新存储帐户的名称。 如果接受使用该名称，会出现绿色复选标记。

      “默认容器”默认为群集名称。 请保留此默认值。

      如果选择了“新建存储帐户”选项，系统将提示选择“位置”，即选择要创建存储帐户的区域。  

         ![数据源边栏选项卡](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > 选择默认数据源的位置还会设置 HDInsight 群集的位置。 群集和默认数据源必须位于同一区域。

    - 如果要使用现有 Data Lake Store，则选择要使用的 ADLS 存储帐户，并将群集 ADD 标识添加到群集以允许访问存储。 有关此过程的详细信息，请参阅[使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)。

    使用“选择”按钮保存数据源配置。


7. 随后会显示“摘要”边栏选项卡，验证所有设置。 在此处可以更改“群集大小”以修改群集中的服务器数，还可指定要运行的任何“脚本操作”。 除非确定需要更大的群集，否则请保留辅助角色节点数目的默认值 `4`。 该群集的预估成本将显示在边栏选项卡内。

    ![群集摘要](./media/hdinsight-hadoop-r-server-get-started/clustersummary.png)

   > [!NOTE]
   > 以后如果需要，可以通过门户调整群集大小（“群集” -> “设置” -> “缩放群集”），增加或减少辅助角色节点的数目。  这种调整大小的方法可将不再使用的群集置于空闲状态，或者增加容量来满足更大任务的需要。
   >
   >

   调整群集、数据节点和边缘节点的大小时，需要注意的一些因素包括：  

   * 如果数据较大，则 Spark 上的分布式 R Server 分析的性能与辅助角色节点数目成正比。  

   * R Server 分析的性能与所要分析的数据大小呈线性关系。 例如：  

     * 对于小型到中等的数据，如果在边缘节点上的本地计算上下文中执行分析，则性能最佳。  若要深入了解本地上下文和 Spark 计算上下文在哪种方案中可获得最佳工作性能，请参阅“R Server on HDInsight 的计算上下文选项”。<br>
     * 如果登录到边缘节点并运行 R 脚本，则将在边缘节点上本地<strong></strong>执行除 ScaleR rx-functions 外的所有函数。 因此，应相应调整边缘节点的内存和核心数。 如果通过便携式计算机使用 HDI 上的 R Server 作为远程计算上下文，则这一点同样适用。

     ![节点定价层边栏选项卡](./media/hdinsight-hadoop-r-server-get-started/pricingtier.png)

     使用“选择”按钮保存节点定价配置。

   还有一个下载模板和参数的链接。 单击此链接可显示用于自动创建具有所选配置的群集的脚本。 创建群集后，这些脚本还可从群集的 Azure 门户条目获得。

   > [!NOTE]
   > 创建群集需要一些时间，通常约 20 分钟左右。 使用启动板上的磁贴或页面左侧的“通知”项检查创建过程。
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>连接到 RStudio Server

如果已选择在安装中包括 RStudio Server 社区版，则可通过两种不同方法访问 RStudio 登录。

1. 转到以下 URL（其中 CLUSTERNAME 是创建的群集的名称）：

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. 在 Azure 门户中打开群集的条目，选择“R Server 仪表板”快速链接，然后选择“R Studio 仪表板”：

     ![访问 R Studio 仪表板](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![访问 R Studio 仪表板](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > 无论使用哪种方法，首次登录时都需进行两次身份验证。  第一次身份验证时，提供群集管理员用户 ID 和密码。 第二次提示时，提供 SSH 用户 ID 和密码。 后续登录只需要 SSH 密码和用户 ID。

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>连接到 R Server 边缘节点

使用以下命令，通过 SSH 连接到 HDInsight 群集的 R Server 边缘节点：

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> 可以先选择群集，然后选择“所有设置” -> “应用” -> “RServer”，在 Azure 门户中找到 `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` 地址。 这会显示边缘节点的 SSH 终结点信息。
>
> ![边缘节点 SSH 终结点的图像](./media/hdinsight-hadoop-r-server-get-started/sshendpoint.png)
>
>

如果使用了密码来保护 SSH 帐户，系统会提示输入密码。 如果使用了公钥，则可能需要使用 `-i` 参数来指定匹配的私钥。 例如：

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

连接后将看到类似于下面的提示：

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>允许多个并发用户

可以启用多个并发用户，方法是：为在其上运行 RStudio Community 版本的边缘节点添加更多用户。

在创建 HDInsight 群集时，必须提供两个用户，即一个 HTTP 用户，一个 SSH 用户：

![并发用户 1](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-1.png)

- 群集登录用户名：一个通过 HDInsight 网关进行身份验证的 HTTP 用户，该网关用于保护所创建的 HDInsight 群集。 此 HTTP 用户用于访问 Ambari UI、YARN UI 以及其他 UI 组件。
- 安全外壳 (SSH) 用户名：一个需通过安全外壳访问群集的 SSH 用户。 此用户是适用于所有头节点、辅助角色节点和边缘节点的 Linux 系统中的用户。 因此，可以使用安全外壳访问远程群集中的任何节点。

在基于 HDInsight 类型群集的 Microsoft R Server 中使用的 R Studio Server Community 版本仅接受 Linux 用户名和密码作为登录机制， 而不支持传递令牌。 因此，如果你已创建新的群集，并且想要使用 R Studio 来访问它，则需登录两次。

- 首先，通过 HDInsight 网关使用 HTTP 用户凭据登录： 

    ![并发用户 2a](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2a.png)

- 然后，使用 SSH 用户凭据登录到 RStudio：
  
    ![并发用户 2b](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2b.png)

目前，在预配 HDInsight 群集时只能创建一个 SSH 用户帐户。 因此，若要允许多个用户访问基于 HDInsight 群集的 Microsoft R Server，需在 Linux 系统中创建其他用户。

由于 RStudio Server Community 是在群集的边缘节点上运行，需执行下述多个步骤：

1. 使用创建的 SSH 用户登录到边缘节点
2. 在边缘节点中添加更多的 Linux 用户
3. 通过已创建的用户使用 RStudio Community 版本

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>步骤 1：使用创建的 SSH 用户登录到边缘节点

下载任意 SSH 工具（例如 Putty），然后使用现有的 SSH 用户登录。 然后，按[使用 SSH 连接到 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md) 中提供的说明访问边缘节点。 基于 HDInsight 群集的 R Server 的边缘节点地址为：clustername-ed-ssh.azurehdinsight.net


### <a name="step-2-add-more-linux-users-in-edge-node"></a>步骤 2：在边缘节点中添加更多的 Linux 用户

若要将用户添加到边缘节点，请执行以下命令：

    sudo useradd yournewusername -m
    sudo passwd yourusername

此时会看到以下项返回： 

![并发用户 3](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-3.png)

当系统提示输入“当前 Kerberos 密码:”时，只需按 Enter 将其忽略即可。 `useradd` 命令中的 `-m` 选项指示系统会为用户创建主文件夹，该文件夹是 RStudio Community 版本所需的。


### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>步骤 3：通过已创建的用户使用 RStudio Community 版本

使用已创建的用户登录到 RStudio：

![并发用户 4](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-4.png)

请注意，RStudio 指示你在使用新用户（例如，此处为 sshuser6）登录到群集： 

![并发用户 5](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-5.png)

也可同时使用原始凭据（默认为 sshuser）从其他浏览器窗口登录。

可以使用 scaleR 函数提交作业。 下面是用来运行作业的命令的示例：

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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


请注意，在 YARN UI 中，提交的作业使用其他用户名：

![并发用户 6](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-6.png)

另请注意，在 Linux 系统中，新添加的用户没有根权限，但对远程 HDFS 和 WASB 存储中的所有文件具有相同的访问权限。


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>使用 R 控制台

1. 在 SSH 会话中，使用以下命令启动 R 控制台：  

        R

2. 应该会看到与下面类似的输出：
    
    R 版本 3.2.2 (2015-08-14) - "Fire Safety" 版权所有 (C) 2015 用于统计计算的 R Foundation 平台: x86_64-pc-linux-gnu（64 位）

    R 是免费软件，不附带任何保证。
    可以在某些情况下对其进行重新分发。
    键入 "license()" 或 "licence()" 了解分发详细信息。

    支持自然语言，但只在英语区域设置中运行

    R 是具有很多参与者的协作项目。
    键入 "contributors()" 了解详细信息，键入 "citation()" 了解如何在发布中引用 R 或 R 包。

    键入 "demo()" 获取相关演示，键入 "help()" 获取联机帮助，或键入 "help.start()" 进入 HTML 浏览器界面获取帮助。
    键入 "q()" 退出 R。

    Microsoft R Server 8.0 版：R 的增强分发 Microsoft 包版权所有 (C) 2016 Microsoft Corporation

    键入 "readme()" 了解发行说明。
    >

3. 可以通过 `>` 提示符输入 R 代码。 R Server 包含可以轻松地与 Hadoop 交互并运行分布式计算的包。 例如，若要查看 HDInsight 群集的默认文件系统根目录，可使用以下命令：

    rxHadoopListFiles("/")

4. 还可以使用 WASB 样式寻址。

    rxHadoopListFiles("wasb:///")


## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>从 Microsoft R Server 或 Microsoft R Client 的远程实例使用 HDI 上的 R Server

可从台式机或笔记本电脑上运行的 Microsoft R Server 或 Microsoft R Client 远程实例设置对 HDI Hadoop Spark 计算上下文的访问。 请参阅[创建 Spark 计算上下文](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md)中的**使用 Microsoft R Server 作为 Hadoop 客户端**小节。 为此，需在笔记本电脑上定义 RxSpark 计算上下文时指定以下选项：hdfsShareDir、shareDir、sshUsername、sshHostname、sshSwitches 和 sshProfileScript。 例如：


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

借助计算上下文，用户可控制是在边缘节点上本地执行计算，还是将计算分布到 HDInsight 群集的节点之间。

1. 在 RStudio Server 或 R 控制台（在 SSH 会话中）中，使用以下代码将示例数据加载到 HDInsight 的默认存储：

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. 接下来，我们要创建一些数据信息并定义两个数据源，以便使用数据。

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. 现在，我们使用本地计算上下文对数据运行逻辑回归。

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

4. 然后，我们使用 Spark 上下文来运行相同的逻辑回归。 Spark 上下文将处理进程分布到 HDInsight 群集的所有辅助角色节点上。

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
   > 也可以使用 MapReduce 将计算分布到群集节点之间。 有关计算上下文的详细信息，请参阅[适用于 R Server on HDInsight 的计算上下文选项](hdinsight-hadoop-r-server-compute-contexts.md)。


## <a name="distribute-r-code-to-multiple-nodes"></a>将 R 代码分布到多个节点

使用 R Server，可轻松利用现有 R 代码并使用 `rxExec` 跨多个群集节点运行代码。 执行参数扫描或模拟时，可以使用此函数。 以下代码是 `rxExec` 的用法示例：

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

如果仍在使用 Spark 或 MapReduce 上下文，此命令将针对运行代码 `(Sys.info()["nodename"])` 的辅助角色节点返回 nodename 值。 例如，在四节点群集上，输出应如下所示：

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


## <a name="accessing-data-in-hive-and-parquet"></a>访问 Hive 和 Parquet 中的数据

通过 R Server 9.1 中提供的功能，可直接访问 Hive 和 Parquet 中的数据，以供 Spark 计算上下文中的 ScaleR 函数使用。 这些功能通过名为 RxHiveData 和 RxParquetData 的新 ScaleR 数据源函数提供，它们通过使用 Spark SQL 将数据直接加载到 Spark DataFrame 中，供 ScaleR 进行分析。  

以下代码是一些示例代码，演示了如何使用新函数：

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
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

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


有关如何使用这些新函数的其他信息，请通过使用 `?RxHivedata` 和 `?RxParquetData` 命令查看 R Server 中的联机帮助。  


## <a name="install-additional-r-packages-on-the-edge-node"></a>在边缘节点上安装其他 R 包

如果要在边缘节点上安装其他 R 包，可以在通过 SSH 连接到边缘节点时，直接从 R 控制台内部使用 `install.packages()`。 但是，如果需要在群集的辅助角色节点上安装 R 包，则必须使用脚本操作。

脚本操作为 Bash 脚本，用于更改 HDInsight 群集的配置或安装其他软件（例如其他 R 包）。 若要使用脚本操作安装其他包，请按以下步骤操作：

> [!IMPORTANT]
> 只有在创建群集之后，才可以使用脚本操作来安装其他 R 包。 请勿在群集创建期间使用此过程，因为脚本依赖于已完全安装并配置完毕的 R Server。
>
>

1. 在 [Azure 门户](https://portal.azure.com)中，选择 HDInsight 群集上的 R Server。

2. 在“设置”边栏选项卡中，依次选择“脚本操作”、“提交新项”，即可提交新的脚本操作。

   ![脚本操作边栏选项卡的图像](./media/hdinsight-hadoop-r-server-get-started/scriptaction.png)

3. 在“提交脚本操作”边栏选项卡中提供以下信息：

   * **名称**：用于标识此脚本的友好名称

   * **Bash 脚本 URI**：`http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * 头：此项应为“未选中”状态

   * 辅助角色：此项应为“选中”状态

   * 边缘节点：此项应为“未选中”状态。

   * Zookeeper：此项应为“未选中”状态

   * **参数**：要安装的 R 包。 例如： `bitops stringr arules`

   * 保留此脚本...：此项应为“选中”状态  

   > [!NOTE]
   > 1. 默认情况下，将从与安装的 R Server 版本一致的 Microsoft MRAN 存储库快照中安装所有 R 包。 若要安装较新版的包，则可能存在不兼容的风险。 不过，将 `useCRAN` 指定为包列表的第一个元素（例如 `useCRAN bitops, stringr, arules`）即可完成此类安装。  
   > 2. 某些 R 包需要额外的 Linux 系统库。 为方便起见，我们已预先安装了最流行的 100 个 R 包所需的依赖项。 但是，如果安装的 R 包需要除此之外的库，则必须下载此处使用的基本脚本，并添加安装系统库的步骤。 接下来，必须将修改的脚本上传到 Azure 存储中的公共 Blob 容器，并使用修改的脚本来安装包。
   >    有关开发脚本操作的详细信息，请参阅[脚本操作开发](hdinsight-hadoop-script-actions-linux.md)。  
   >
   >

   ![添加脚本操作](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. 选择“创建”运行脚本。 脚本完成后，可在所有辅助角色节点上使用 R 包。


## <a name="using-microsoft-r-server-operationalization"></a>使用 Microsoft R Server 操作化

完成数据建模后，可以操作模型以进行预测。 若要配置 Microsoft R Server 操作化，请执行以下步骤：

首先，通过 ssh 登录到边缘节点。 例如， 

    ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

使用 ssh 之后，请更改相关版本的目录并使用 sudo 运行 dotnet dll： 

- 对于 Microsoft R Server 9.1：

    cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0   sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

- 对于 Microsoft R Server 9.0：

    cd /usr/lib64/microsoft-deployr/9.0.1   sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

若要使用单机配置配置 Microsoft R Server 操作化，请执行以下操作：

1. 选择“配置 R Server 的操作化”
2. 选择“A. 单机（Web + 计算节点）”
3. 输入 **admin** 用户的密码

![单机操作](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

（可选步骤）通过运行诊断测试来执行诊断检查，如下所示：

1. 选择“6. 运行诊断测试”
2. 选择“A. 测试配置”
3. 输入 Username = “admin” 和之前配置步骤中输入的密码
4. 确认总体运行状况 = 通过
5. 退出管理实用工具
6. 退出 SSH

![操作诊断](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>在 Spark 上使用 Web 服务时延迟时间很长
>
>如果在尝试使用 Web 服务时遇到长时间的延迟，而这些服务是在 Spark 计算上下文中使用 mrsdeploy 函数创建的，则可能需要添加一些缺失的文件夹。 Spark 应用程序属于名为“rserve2”的用户，不论何时使用 mrsdeploy 函数从 Web 服务调用它。 若要解决此问题，请执行以下操作：

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


在此阶段，操作化的配置已完成。 现在，可以使用 RClient 上的“mrsdeploy”包连接到边缘节点上的操作化，并开始使用其功能（如[远程执行](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution)和 [Web 服务](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette)）。 可能需要通过 SSH 登录设置端口转发隧道，具体取决于群集是否设置在虚拟网络上。 以下部分介绍如何设置此隧道。

### <a name="rserver-cluster-on-virtual-network"></a>虚拟网络上的 RServer 群集

请确保允许流量通过端口 12800 到达边缘节点。 这样，便可以使用边缘节点连接到操作化功能。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


如果 `remoteLogin()` 无法连接到边缘节点，但你可以使用 SSH 连接到边缘节点，则需验证是否已正确设置允许端口 12800 上的流量的规则。 如果仍遇到此问题，则解决方法是通过 SSH 设置端口转发隧道。 有关说明，请参阅以下部分。

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>RServer 群集未设置在虚拟网络上

如果群集未设置在 vnet 上，或者如果通过 vnet 连接时遇到问题，可以使用 SSH 端口转发隧道：

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

在 Putty 上，也可以设置它。

![putty ssh 连接](./media/hdinsight-hadoop-r-server-get-started/putty.png)

SSH 会话处于活动状态后，来自计算机端口 12800 的流量将通过 SSH 会话转发到边缘节点的端口 12800。 请确保在 `remoteLogin()` 方法中使用 `127.0.0.1:12800`。 这将通过端口转发登录到边缘节点的操作化。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>如何在 HDInsight 辅助节点上缩放 Microsoft R Server 操作化计算节点

### <a name="decommission-the-worker-nodes"></a>解除辅助节点的授权

Microsoft R Server 当前未通过 Yarn 管理。 如果辅助角色节点未解除授权，Yarn 资源管理器将无法正常工作，因为它不知道服务器所占用的资源。 为了避免这种情况，建议在扩大计算节点前解除辅助角色节点的授权。

解除辅助节点授权的步骤：

* 登录到 HDI 群集的 Ambari 控制台，单击“主机”选项卡
* 选择（要解除授权的）辅助节点，单击“操作”>“选择主机”>“主机”> 单击“打开维护模式”。 例如，下图中，选择了对 wn3 和 wn4 解除授权。  

   ![解除辅助节点的授权](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* 选择“操作” > “所选主机” > “DataNodes”> 单击“解除授权”
* 选择“操作” > “所选主机” > “NodeManagers”> 单击“解除授权”
* 选择“操作” > “所选主机” > “DataNodes”> 单击“停止”
* 选择“操作” > “所选主机” > “NodeManagers”> 单击“停止”
* 选择“操作” > “所选主机” > “主机”> 单击“停止所有组件”
* 取消选择辅助角色节点并选择头节点
* 选择“操作” > “所选主机”>“主机” > “重启所有组件”

### <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>在每个已解除授权的辅助节点上配置计算节点

1. 通过 SSH 登录到每个已解除授权的辅助角色节点。
2. 使用 `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll` 运行管理实用工具。
3. 输入“1”，选择“配置 R Server 的操作化”选项。
4. 输入“c”以选择选项“C. 计算节点”。 这将在辅助角色节点上配置计算节点。
5. 退出管理实用工具。

### <a name="add-compute-nodes-details-on-web-node"></a>在 Web 节点上添加计算节点详细信息

将所有已解除授权的辅助角色节点配置为运行计算节点后，回到边缘节点，在 Microsoft R Server Web 节点的配置中添加已解除授权的辅助角色节点的 IP 地址：

* 通过 SSH 登录到边缘节点。
* 运行 `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`。
* 查看“URI”部分，并添加辅助节点的 IP 和端口详细信息。

    ![通过命令行解除辅助节点的授权](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](hdinsight-administer-use-portal-linux.md#create-clusters)。


## <a name="next-steps"></a>后续步骤

现在，应了解如何创建包括 R Server 的新 HDInsight 群集，以及从 SSH 会话使用 R 控制台的基础知识。 以下主题介绍管理和使用 R Server on HDInsight 的其他方式：

* [将 RStudio Server 添加到 HDInsight（如果未在群集创建过程中安装）](hdinsight-hadoop-r-server-install-r-studio.md)
* [适用于 HDInsight 上的 R Server 的计算上下文选项](hdinsight-hadoop-r-server-compute-contexts.md)
* [适用于 R Server on HDInsight 的 Azure 存储选项](hdinsight-hadoop-r-server-storage.md)

