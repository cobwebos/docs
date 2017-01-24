---
title: "使用 Mahout 和基于 WIndows 的 HDInsight 生成推荐 | Microsoft Docs"
description: "了解如何使用 Apache Mahout 机器学习库通过基于 Windows 的 HDInsight (Hadoop) 生成电影推荐。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: fc79b8017f2184091f2473a0ff9cdfbd0a4cbdf8
ms.openlocfilehash: c7499dba829e24e957cae47dae88599013c3de95


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>将 Apache Mahout 与 HDInsight 中的 Hadoop 配合使用以生成电影推荐
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

了解如何使用 [Apache Mahout](http://mahout.apache.org) 机器学习库通过 Azure HDInsight 生成电影推荐。

> [!NOTE]
> 本文档中的步骤要求使用 Windows 客户端和基于 Windows 的 HDInsight 群集。 有关从 Linux、 OS X 或 Unix 客户端将 Mahout 与基于 Linux 的 HDInsight 群集配合使用的信息，请参阅[将 Apache Mahout 与 HDInsight 中基于 Linux 的 Hadoop 配合使用以生成电影推荐](hdinsight-hadoop-mahout-linux-mac.md)

## <a name="a-namelearnawhat-you-will-learn"></a><a name="learn"></a>学习内容
Mahout 是适用于 Apache Hadoop 的[机器学习][ml]库。 Mahout 包含用于处理数据的算法，例如筛选、分类和群集。 本文介绍了如何使用推荐引擎根据用户的好友看过的电影为用户生成电影推荐。 还介绍了如何使用决策林执行分类。 本文将传授以下知识：

* 如何通过使用 Windows PowerShell 运行 Mahout 作业
* 如何从 Hadoop 命令行运行 Mahout 作业
* 如何在 HDInsight 3.0 和 HDInsight 2.0 群集上安装 Mahout

  > [!NOTE]
  > Mahout 是随 HDInsight 3.1 版本的群集一起提供的。 如果使用早期版本的 HDInsight，请在继续操作之前参阅[安装 Mahout](#install)。

## <a name="prerequisites"></a>先决条件
* **HDInsight 中基于 Windows 的 Hadoop 群集**。 有关创建该群集的信息，请参阅[开始使用 HDInsight 中的 Hadoop][getstarted]
* **配备 Azure PowerShell 的工作站**。

    > [!IMPORTANT]
    > Azure PowerShell 支持使用 Azure Service Manager 管理 HDInsight 资源，但**不建议使用**，而且将于 2017 年 1 月 1 日前删除。 本文档中的步骤使用的是与 Azure Resource Manager 兼容的新 HDInsight cmdlet。
    >
    > 请按照 [安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 中的步骤安装最新版本的 Azure PowerShell。 如果你的脚本需要修改后才能使用与 Azure Resource Manager 兼容的新 cmdlet，请参阅 [迁移到适用于 HDInsight 群集的基于 Azure Resource Manager 的开发工具](hdinsight-hadoop-development-using-azure-resource-manager.md) ，了解详细信息。

## <a name="a-namerecommendationsagenerate-recommendations-by-using-windows-powershell"></a><a name="recommendations"></a>使用 Windows PowerShell 生成推荐
> [!NOTE]
> 尽管在本部分中使用的作业使用 Windows PowerShell 执行，但是，随 Mahout 一起提供的很多类当前不使用 Windows PowerShell，并且它们必须通过使用 Hadoop 命令行来运行。 有关不使用 Windows PowerShell 的类的列表，请参阅[故障排除](#troubleshooting)部分。
>
> 有关使用 Hadoop 命令行运行 Mahout 作业的示例，请参阅[使用 Hadoop 命令行对数据分类](#classify)。

由 Mahout 提供的功能之一是推荐引擎。 此引擎接受 `userID`、`itemId` 和 `prefValue` 格式（项的用户首选项）的数据。 然后，Mahout 将执行共现分析以确定：偏好某个项的用户也偏好其他类似项。 Mahout 然后确定拥有类似项首选项的用户，这些首选项可用于推荐。

下面是使用电影的极其简单的示例：

* **共现**：Joe、Alice 和 Bob 都喜欢电影《星球大战》、《帝国反击战》和《绝地归来》。 Mahout 可确定喜欢以上电影之一的用户也喜欢其他两部。
* **共现**：Bob 和 Alice 还喜欢电影《幽灵的威胁》、《克隆人的进攻》和《西斯的复仇》。 Mahout 可确定喜欢前面三部电影的用户也喜欢这三部电影。
* **类似性推荐**：由于 Joe 喜欢前三部电影，Mahout 会查看具有类似首选项的其他人喜欢的电影，但是 Joe 还未观看过（喜欢/评价）。 在这种情况下，Mahout 推荐《幽灵的威胁》、《克隆人的进攻》和《西斯的复仇》。

### <a name="understanding-the-data"></a>了解数据
为方便起见，[GroupLens 研究][movielens]以兼容 Mahout 的格式提供电影的评价数据。 此数据在 `/HdiSamples/MahoutMovieData` 中你的群集的默认存储中可用。

存在两个文件，`moviedb.txt`（有关影片的信息）和 `user-ratings.txt`。 user-ratings.txt 文件用于分析过程中，而 moviedb.txt 用于在显示分析结果时，提供用户友好的文本信息。

user-ratings.txt 中包含的数据具有 `userID`、`movieID`、`userRating` 和 `timestamp` 结构，它将告诉我们每个用户对电影评级的情况。 下面是数据的示例：

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>运行作业
使用以下 Windows PowerShell 脚本运行作业，将 Mahout 推荐引擎用于电影数据：

```powershell
# The HDInsight cluster name.
$clusterName = "the cluster name"

#Get HTTPS/Admin credentials for submitting the job later
$creds = Get-Credential
#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

#Create a storage content and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# NOTE: The version number in the file path
# may change in future versions of HDInsight.
$jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
#
# If you are using an earlier version of HDInsight,
# set $jarFile to the jar file you
# uploaded.
# For example,
# $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

# The arguments for this job
# * input - the path to the data uploaded to HDInsight
# * output - the path to store output data
# * tempDir - the directory for temp files
$jobArguments = "--similarityClassname", "recommenditembased", `
                "-s", "SIMILARITY_COOCCURRENCE", `
                "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                "--output", "wasbs:///example/out",
                "--tempDir", "wasbs:///example/temp"

# Create the job definition
$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
    -JarFile $jarFile `
    -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
    -Arguments $jobArguments

# Start the job
$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Wait on the job to complete
Write-Host "Wait for the job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
# Download the output
Get-AzureStorageBlobContent `
        -Blob example/out/part-r-00000 `
        -Container $container `
        -Destination output.txt `
        -Context $context

# Write out any error information
Write-Host "STDERR"
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

> [!NOTE]
> Mahout 作业不删除在处理作业时创建的临时数据。 在示例作业中指定 `--tempDir` 参数，将临时文件隔离到特定目录中。

Mahout 作业不会将输出返回到 STDOUT。 而是会将其作为 **part-r-00000** 存储在指定的输出目录中。 该脚本将此文件下载到你工作站上的当前目录中的 **output.txt** 中。

下面是此文件内容的示例：

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

第一列是 `userID`。 “[”和“]”中包含的值为 `movieId`:`recommendationScore`。

### <a name="view-the-output"></a>查看输出
尽管生成的输出也许能够正常地在应用程序中使用，但它的用户可读性并不太好。 服务器的 `moviedb.txt` 可用于将 `movieId` 解析为电影名称，但是必须首先使用以下脚本从服务器下载它并对文件评级：

```powershell
# The HDInsight cluster name.
$clusterName = "the cluster name"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value
#Create a storage content and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey
#Download the files
Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
-Container $container `
-Destination moviedb.txt `
-Context $context
Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
-Container $container `
-Destination user-ratings.txt `
-Context $context
```

下载文件后，使用以下 PowerShell 脚本通过影片名称显示推荐：

```powershell
<#
.SYNOPSIS
    Displays recommendations for movies.
.DESCRIPTION
    Displays recommendations generated by Mahout
    with HDInsight example in a human readable format.
.EXAMPLE
    .\Show-Recommendation -userId 4
        -userDataFile "user-ratings.txt"
        -movieFile "moviedb.txt"
        -recommendationFile "output.txt"
#>

[CmdletBinding(SupportsShouldProcess = $true)]
param(
    #The user ID
    [Parameter(Mandatory = $true)]
    [String]$userId,

    [Parameter(Mandatory = $true)]
    [String]$userDataFile,

    [Parameter(Mandatory = $true)]
    [String]$movieFile,

    [Parameter(Mandatory = $true)]
    [String]$recommendationFile
)
# Read movie ID & description into hash table
Write-Host "Reading movies descriptions" -ForegroundColor Green
$movieById = @{}
foreach($line in Get-Content $movieFile)
{
    $tokens = $line.Split("|")
    $movieById[$tokens[0]] = $tokens[1]
}
# Load movies user has already seen (rated)
# into a hash table
Write-Host "Reading rated movies" -ForegroundColor Green
$ratedMovieIds = @{}
foreach($line in Get-Content $userDataFile)
{
    $tokens = $line.Split("`t")
    if($tokens[0] -eq $userId)
    {
        # Resolve the ID to the movie name
        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
    }
}
# Read recommendations generated by Mahout
Write-Host "Reading recommendations" -ForegroundColor Green
$recommendations = @{}
foreach($line in get-content $recommendationFile)
{
    $tokens = $line.Split("`t")
    if($tokens[0] -eq $userId)
    {
        #Trim leading/treailing [] and split at ,
        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
        foreach($movieIdAndScore in $movieIdAndScores)
        {
            #Split at : and store title and score in a hash table
            $idAndScore = $movieIdAndScore.Split(":")
            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
        }
        break
    }
}

Write-Host "Rated movies" -ForegroundColor Green
Write-Host "---------------------------" -ForegroundColor Green
$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                @{Expression={$_.Value};Label="Rating"}
$ratedMovieIds | format-table $ratedFormat
Write-Host "---------------------------" -ForegroundColor Green

write-host "Recommended movies" -ForegroundColor Green
Write-Host "---------------------------" -ForegroundColor Green
$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                        @{Expression={$_.Value};Label="Score"}
$recommendations | format-table $recommendationFormat
```

下面是运行脚本的示例：

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

输出应如下所示：

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="a-nameclassifyaclassify-data-by-using-the-hadoop-command-line"></a><a name="classify"></a>通过使用 Hadoop 命令行对数据进行分类
Mahout 提供的其中一个分类方法是生成[随机林][forest]。 这是一个多步骤过程，涉及到使用训练数据来生成决策树，然后使用决策树对数据进行分类。 此过程使用 Mahout 提供的 **org.apache.mahout.classifier.df.tools.Describe** 类。 它当前必须通过使用 Hadoop 命令行来运行。

### <a name="load-the-data"></a>加载数据
1. 从 [NSL-KDD 数据集](http://nsl.cs.unb.ca/NSL-KDD/)下载以下文件。

   * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff)：训练文件
   * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff)：测试数据
2. 打开每个文件，删除顶部以“'@',”开头的行，然后保存文件。 如果未删除这些行，则在 Mahout 中使用数据时会收到错误消息。
3. 将文件上传到 **example/data**。 为此，可以使用以下脚本。 将 **CLUSTERNAME** 替换为 HDInsight 群集的名称。 将 FILENAME 替换为要上传的文件的名称。

    ```powershell
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterName="CLUSTERNAME"
    $fileToUpload="FILENAME"
    $blobPath="example/data/FILENAME"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    Set-AzureStorageBlobContent `
        -File $fileToUpload `
        -Blob $blobPath `
        -Container $container `
        -Context $context
    ```

### <a name="run-the-job"></a>运行作业
1. 此作业需要 Hadoop 命令行。 为 HDInsight 群集启用远程桌面，然后根据[使用 RDP 连接到 HDInsight 群集](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)中的说明连接到该群集。
2. 建立连接后，使用“Hadoop 命令行”图标打开 Hadoop 命令行：

    ![hadoop cli][hadoopcli]
3. 使用以下命令生成文件描述符 (**KDDTrain+.info**)，该描述符使用 Mahout。

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` 描述文件中数据的属性。 例如，L 指示标签。
4. 通过使用以下命令生成决策树的林：

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    此操作的输出存储在 **nsl-forest** 目录中，该目录位于 HDInsight 群集的存储中的 __wasbs://user/&lt;username>/nsl-forest/nsl-forest.seq 处。 &lt;用户名> 是用于远程桌面会话的用户名。 此文件对用户不可读。
5. 通过为 **KDDTest+.arff** 数据集分类来测试该林。 请使用以下命令：

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    此命令返回如下有关分类过程的摘要信息：

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

   此作业还将在 **wasbs:///example/data/predictions/KDDTest+.arff.out** 中生成一个文件。 但是，此文件对用户不可读。

> [!NOTE]
> Mahout 作业不会覆盖文件。 如果要再次运行这些作业，则必须删除由以前的作业创建的文件。

## <a name="a-nametroubleshootingatroubleshooting"></a><a name="troubleshooting"></a>故障排除
### <a name="a-nameinstallainstall-mahout"></a><a name="install"></a>安装 Mahout
Mahout 安装在 HDInsight 3.1 群集上，可以通过使用以下步骤手动安装在 HDInsight 3.0 或 HDInsight 2.1 群集上：

1. 要使用的 Mahout 版本取决于群集的 HDInsight 版本。 可以通过在 Azure 门户中查看群集的属性找到群集版本。

   * **对于 HDInsight 2.1**，可以下载包含 [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar) 的 Java 存档 (JAR) 文件。
   * **对于 HDInsight 3.0**，必须[从源生成 Mahout][build]，并指定 HDInsight 提供的 Hadoop 版本。 安装构建页上列出的必备组件，并下载源，然后使用以下命令创建 Mahout jar 文件：

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        生成完成后，可在 **mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar** 找到 JAR 文件。

     > [!NOTE]
     > 在 Mahout 1.0 发布后，应该能够使用 HDInsight 3.0 的预构建程序包。

2. 将该 jar 文件上传到群集默认存储的 **example/jars** 中。 在以下脚本中将 CLUSTERNAME 替换为你的 HDInsight 群集的名称，并将 FILENAME 替换为 **mahout-coure-0.9-job.jar** 文件的路径。

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey

        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

### <a name="cannot-overwrite-files"></a>无法覆盖文件
Mahout 作业不清理在处理期间创建的临时文件。 此外，作业将不会覆盖现有的输出文件。

若要避免运行 Mahout 作业时出错，请在每次运行作业之前删除临时文件和输出文件，或者使用唯一的临时目录名称和输出目录名称。

### <a name="cannot-find-the-jar-file"></a>找不到 JAR 文件
HDInsight 3.1 群集提供 Mahout。 路径和文件名包括在群集上安装的 Mahout 的版本号。 本教程中的 Windows PowerShell 示例脚本使用的路径的有效截止期为 2015 年 11 月，但是，将来对 HDInsight 做出更新后，版本号将发生更改。 若要确定群集的 Mahout JAR 文件的当前路径，请使用以下 Windows PowerShell 命令，然后修改脚本以引用返回的文件路径：

```powershell
Use-AzureRmHDInsightCluster -ClusterName $clusterName
#Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value
Invoke-AzureRmHDInsightHiveJob `
        -StatusFolder "wasbs:///example/statusout" `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'
```

### <a name="a-namenopowershellaclasses-that-do-not-work-with-windows-powershell"></a><a name="nopowershell"></a>不适用于 Windows PowerShell 的类
Mahout 作业如果使用以下类，则从 Windows PowerShell 中使用这些类时，将返回各种错误消息：

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

若要运行使用这些类的作业，请连接到 HDInsight 群集，然后通过使用 Hadoop 命令行运行这些作业。 有关示例，请参阅[使用 Hadoop 命令行对数据分类](#classify)。

## <a name="next-steps"></a>后续步骤
现在，你已经学习了如何使用 Mahout，因此可以探索通过其他方式来使用 HDInsight 上的数据：

* [Hive 和 HDInsight 配合使用](hdinsight-use-hive.md)
* [Pig 和 HDInsight 配合使用](hdinsight-use-pig.md)
* [MapReduce 和 HDInsight 配合使用](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools



<!--HONumber=Dec16_HO2-->


