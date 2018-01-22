---
title: "基于数 TB 数据的服务器工作负荷预测 - Azure | Microsoft Docs"
description: "如何使用 Azure Machine Learning Workbench 定型大数据的机器学习模型。"
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: f2482c7a47c72d192f26f3d8d9b9249af53da25d
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>基于 TB 量级的数据执行服务器工作负荷预测

本文介绍数据科学家如何使用 Azure Machine Learning Workbench 开发需要使用大数据的解决方案。 可以从大型数据集的一个示例开始，反复进行数据准备、特征工程和机器学习操作，然后将该过程扩展到整个大型数据集。 

将介绍 Machine Learning Workbench 的以下关键功能：
* 在计算目标之间轻松切换。 可以设置不同的计算目标，并在试验中使用它们。 在此示例中，请将 Ubuntu DSVM 和 Azure HDInsight 群集用作计算目标。 也可根据资源的可用性配置计算目标。 特别是在使用更多工作节点向外扩展 Spark 群集后，即可通过 Machine Learning Workbench 使用资源来加速试验运行。
* 运行历史记录跟踪。 可以使用 Machine Learning Workbench 来跟踪机器学习模型的性能以及其他感兴趣的指标。
* 机器学习模型的实施。 可以使用 Machine Learning Workbench 中的内置工具，在 Azure 容器服务上将定型的机器学习模型部署为 Web 服务。 也可使用 Web 服务通过 REST API 调用获取微批预测。 
* 支持 TB 数据。

> [!NOTE]
> 有关代码示例以及与此示例相关的其他资料，请参阅 [GitHub](https://github.com/Azure/MachineLearningSamples-BigData)。
> 

## <a name="use-case-overview"></a>用例概述

对于管理自己的基础结构的技术公司而言，预测服务器上的工作负荷是常见的业务需求。 为了减少基础结构成本，应将在未充分使用的服务器上运行的服务组合到一起，让其在更少数目的计算机上运行。 对于在过度使用的服务器上运行的服务，应为其提供更多用于运行的计算机。 

在此方案中，重点是针对每台计算机（或服务器）的工作负荷预测。 具体说来，请使用每台服务器上的会话数据来预测未来服务器的工作负荷类别。 请使用 [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html) 中的随机林分类器将每台服务器的负荷分为低、中、高三个类别。 该示例中的机器学习技术和工作流可以被轻松地延伸至其他类似问题。 


## <a name="prerequisites"></a>系统必备

运行此示例的先决条件如下所示：

* [Azure 帐户](https://azure.microsoft.com/free/)（有免费试用版可用）。
* [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的已安装副本。 若要安装此程序并创建工作区，请参阅[快速入门安装指南](./quickstart-installation.md)。 如果有多个订阅，则可以[将所需订阅设置为当前的活动订阅](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set)。
* Windows 10（对于 macOS 系统，此示例中的说明通常也一样）。
* Data Science Virtual Machine (DSVM) for Linux (Ubuntu)，最好位于数据所在的美国东部地区。 可以按照[这些说明](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)预配 Ubuntu DSVM。 也可参阅[此快速入门](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu)。 我们建议使用至少具有 8 个内核和 32 GB 内存的虚拟机。 

按照[说明](https://docs.microsoft.com/azure/machine-learning/preview/known-issues-and-troubleshooting-guide#remove-vm-execution-error-no-tty-present)在 VM 上为 AML Workbench 启用无密码 sudoer 访问。  可以选择[在 AML Workbench 中使用基于 SSH 密钥的身份验证来创建和使用 VM](https://docs.microsoft.com/azure/machine-learning/preview/experimentation-service-configuration#using-ssh-key-based-authentication-for-creating-and-using-compute-targets)。 在此示例中，我们使用密码来访问 VM。  将下表与 DSVM 信息一起保存，以供在后续步骤中使用：

 字段名称| 值 |  
 |------------|------|
DSVM IP 地址 | xxx|
 用户名  | xxx|
 密码   | xxx|


 可以选择将任何 VM 与安装的 [Docker 引擎](https://docs.docker.com/engine/)结合使用。

* 具有 Hortonworks Data Platform 版本 3.6 和 Spark 版本 2.1.x 的 HDInsight Spark 群集，最好位于数据所在的美国东部地区。 请访问[在 Azure HDInsight 中创建 Apache Spark 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)，详细了解如何创建 HDInsight 群集。 建议使用一个三工作节点型群集，每个工作节点具有 16 个核心和 112 GB 的内存。 也可直接选择 VM 类型 `D12 V2` 作为头节点，`D14 V2` 作为工作节点。 群集的部署需要约 20 分钟。 尝试该示例需要群集名、SSH 用户名和密码。 将下表与 Azure HDInsight 群集信息一起保存，以供在后续步骤中使用：

 字段名称| 值 |  
 |------------|------|
 群集名称| xxx|
 用户名  | xxx（默认为 sshuser）|
 密码   | xxx|


* 一个 Azure 存储帐户。 可以按[这些说明](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)创建一个。 另外，请在该存储帐户中创建两个名为 `fullmodel` 和 `onemonthmodel` 的专用 Blob 容器。 存储帐户用于保存中间计算结果和机器学习模型。 需要使用存储帐户名和访问密钥来尝试此示例。 请将下表与 Azure 存储帐户信息一起保存，以供在后续步骤中使用：

 字段名称| 值 |  
 |------------|------|
 存储帐户名称| xxx|
 访问密钥  | xxx|


在必备组件列表中创建的 Ubuntu DSVM 和 Azure HDInsight 群集是计算目标。 计算目标是 Machine Learning Workbench 环境中的计算源，可能与运行 Workbench 的计算机不同。   

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：
1.  打开 Machine Learning Workbench。
2.  在“项目”页上选择 + 号，然后选择“新建项目”。
3.  在“新建项目”窗格中，填写新项目的信息。
4.  在“搜索项目模板”搜索框中，键入“TB 数据的工作负荷预测”，并选择模板。
5.  选择“创建”。

可以按照[此说明](./tutorial-classifying-iris-part-1.md)创建包含预先创建的 git 存储库的 Workbench 项目。  
运行 `git status`，检查用于版本跟踪的文件的状态。

## <a name="data-description"></a>数据说明

此示例中使用的数据是综合性的服务器工作负荷数据， 托管于可在美国东部地区公开访问的 Azure Blob 存储帐户中。 [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) 的 `dataFile` 字段中提供了具体的存储帐户信息，格式为“wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>”。 可以直接使用 Blob 存储中的数据。 如果许多用户同时使用存储，你可以使用 [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) 将数据下载到自己的存储中，以便获得更好的试验体验。 

总数据大小约为 1 TB。 每个文件约为 1-3 GB，且是不含标头的 CSV 文件格式。 每行数据代表某个特定服务器上的事务负荷。 数据架构的详细信息如下所示：

列号 | 字段名称| Type | 说明 |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Datetime |    会话开始时间
2  |`SessionEnd`    | Datetime | 会话结束时间
3 |`ConcurrentConnectionCounts` | Integer | 并发连接数
4 | `MbytesTransferred` | Double | 以兆字节为单位传输的规范化数据
5 | `ServiceGrade` | Integer |  会话服务等级
6 | `HTTP1` | Integer|  会话使用 HTTP1 或 HTTP2
7 |`ServerType` | Integer   |服务器类型
8 |`SubService_1_Load` | Double |   子服务 1 负荷
9 | `SubService_2_Load` | Double |  子服务 2 负荷
10 | `SubService_3_Load` | Double |     子服务 3 负荷
11 |`SubService_4_Load` | Double |  子服务 4 负荷
12 | `SubService_5_Load`| Double |      子服务 5 负荷
13 |`SecureBytes_Load`  | Double | 安全字节负荷
14 |`TotalLoad` | Double | 服务器总负荷
15 |`ClientIP` | 字符串|    客户端 IP 地址
16 |`ServerIP` | 字符串|    服务器 IP 地址



请注意，上表中列出了预期的数据类型。 由于缺失值和脏数据问题，不能保证实际的数据类型与预期一致。 数据处理应考虑这一点。 


## <a name="scenario-structure"></a>方案结构

本示例中的文件按如下方式组织。

| 文件名 | Type | 说明 |
|-----------|------|-------------|
| `Code` | 文件夹 | 包含该示例中所有代码的文件夹。 |
| `Config` | 文件夹 | 包含配置文件的文件夹。 |
| `Image` | 文件夹 | 用于保存自述文件的图像的文件夹。 |
| `Model` | 文件夹 | 用于保存从 Blob 存储下载的模型文件的文件夹。 |
| `Code/etl.py` | Python 文件 | 用于数据准备和特征工程的 Python 文件。 |
| `Code/train.py` | Python 文件 | 用于定型三类多分类模型的 Python 文件。  |
| `Code/webservice.py` | Python 文件 | 用于实施的 Python 文件。  |
| `Code/scoring_webservice.py` | Python 文件 |  用于数据转换和 Web 服务调用的 Python 文件。 |
| `Code/O16Npreprocessing.py` | Python 文件 | 用于对 scoring_webservice.py 的数据进行预处理的 Python 文件。  |
| `Code/util.py` | Python 文件 | 包含的代码用于读取和写入 Azure Blob 的 Python 文件。  
| `Config/storageconfig.json` | JSON 文件 | Azure Blob 容器的配置文件，该容器存储的中间结果和模型用于一个月数据的处理和基于该数据的定型。 |
| `Config/fulldata_storageconfig.json` | Json 文件 | Azure Blob 容器的配置文件，该容器存储的中间结果和模型用于完整数据集的处理和基于该数据集的定型。|
| `Config/webservice.json` | JSON 文件 | scoring_webservice.py 的配置文件。|
| `Config/conda_dependencies.yml` | YAML 文件 | Conda 依赖项文件。 |
| `Config/conda_dependencies_webservice.yml` | YAML 文件 | Web 服务的 Conda 依赖项文件。|
| `Config/dsvm_spark_dependencies.yml` | YAML 文件 | Ubuntu DSVM 的 Spark 依赖项文件。 |
| `Config/hdi_spark_dependencies.yml` | YAML 文件 | HDInsight Spark 群集的 Spark 依赖项文件。 |
| `README.md` | Markdown 文件 | 自述 Markdown 文件。 |
| `Code/download_model.py` | Python 文件 | 用于将模型文件从 Azure Blob 下载到本地磁盘的 Python 文件。 |
| `Docs/DownloadModelsFromBlob.md` | Markdown 文件 | 包含 `Code/download_model.py` 运行方式说明的 Markdown 文件。 |



### <a name="data-flow"></a>数据流

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 中的代码从可以公开访问的容器（[`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) 的 `dataFile` 字段）加载数据。 它包括数据准备和功能设计，并将中间计算结果和模型保存到自己的专用容器中。 [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) 中的代码从专用容器加载中间计算结果、定型多类分类模型，并将定型的机器学习模型写入专用容器中。 

应将一个容器用于一个月数据集的试验，将另一个容器用于完整数据集的试验。 因为数据和模型另存为 Parquet 文件，所以每个文件实际上是容器中的一个文件夹，包含多个 Blob。 生成的容器如下所示：

| Blob 前缀名称 | Type | 说明 |
|-----------|------|-------------|
| featureScaleModel | Parquet | 数字功能的标准扩展器模型。 |
| stringIndexModel | Parquet | 非数字功能的字符串索引器模型。|
| oneHotEncoderModel|Parquet | 分类功能的独热编码器模型。 |
| mlModel | Parquet | 定型的机器学习模型。 |
| info| Python pickle 文件 | 有关已转换数据的信息，其中包括定型启动、定型结束、持续时间、定型测试拆分的时间戳，以及用于索引编制和独热编码的列。

上表中的所有文件和 Blob 均用于实施。


### <a name="model-development"></a>模型开发

#### <a name="architecture-diagram"></a>体系结构关系图


下图演示使用 Machine Learning Workbench 来开发模型的端到端工作流：![体系结构](media/scenario-big-data/architecture.PNG)

以下各部分演示如何使用 Machine Learning Workbench 中的远程计算目标功能完成模型开发。 首先加载少量示例数据，并在 Ubuntu DSVM 上运行脚本 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 以实现快速迭代。 我们可以通过传递额外参数以实现更快速的迭代，从而进一步限制我们在 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 中执行的操作。 最后，使用 HDInsight 群集针对完整数据进行定型。     

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 文件加载和准备数据，并执行特征工程操作。 它接受两个参数：
* 一个用于 Blob 存储容器的配置文件，该容器用于存储中间计算结果和模型。
* 一个用于加快迭代速度的调试配置参数。

第一个参数 `configFilename` 是本地配置文件，可以在该文件中存储 Blob 存储信息并指定加载数据的位置。 默认情况下，它是 [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json)，并将在一个月数据运行中使用。 我们还加入了 [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json)，完整数据集运行中将需要使用。 配置中的内容如下所示： 

| 字段 | Type | 说明 |
|-----------|------|-------------|
| storageAccount | 字符串 | Azure 存储帐户名称 |
| storageContainer | 字符串 | Azure 存储帐户中用于存储中间结果的容器 |
| storageKey | 字符串 |Azure 存储帐户访问密钥 |
| dataFile|字符串 | 数据源文件  |
| duration| 字符串 | 数据源文件中的数据持续时间|

同时修改 `Config/storageconfig.json` 和 `Config/fulldata_storageconfig.json` 以配置存储帐户、存储密钥和 Blob 容器，以存储中间结果。 默认情况下，一个月数据运行的 Blob 容器是 `onemonthmodel`，完整数据集运行的 Blob 容器是 `fullmodel`。 请确保在存储帐户中创建这两个容器。 [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) 中的 `dataFile` 字段配置在 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 中加载的数据。 `duration` 字段配置数据所包括的范围。 如果持续时间设置为 ONE_MONTH，则所加载的数据应为 2016 年 6 月数据的七个文件中的一个 .csv 文件。 如果持续时间为 FULL，则加载完整数据集 (1 TB)。 无需在这两个配置文件中更改 `dataFile` 和 `duration`。

第二个参数是 DEBUG。 将其设置为 FILTER_IP 可实现更快速的迭代。 想要调试你的脚本时，使用该参数很有帮助。

> [!NOTE]
> 在下述所有命令中，将任何参数变量替换为实际值。
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>在 Ubuntu DSVM 的 Docker 上开发模型

#####  <a name="1-set-up-the-compute-target"></a>1.设置计算目标

从 Machine Learning Workbench 启动命令行，方法是选择“文件” > “打开命令提示符”。 然后运行： 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

下述两个文件在项目的 aml_config 文件夹中创建：

-  dockerdsvm.compute：此文件包含远程执行目标的连接和配置信息。
-  dockerdsvm.runconfig：此文件是一组在 Workbench 应用程序中使用的运行选项。

浏览到 dockerdsvm.runconfig 并更改以下字段的配置，如下所示：

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

通过运行以下命令准备项目环境：

```az ml experiment prepare -c dockerdsvm```


将 `PrepareEnvironment` 设置为 true 后，Machine Learning Workbench 会在你每次提交作业时创建运行时环境。 `Config/conda_dependencies.yml` 和 `Config/dsvm_spark_dependencies.yml` 包含运行时环境的自定义。 可以始终通过编辑这两个 YMAL 文件来修改 Conda 依赖项、Spark 配置和 Spark 依赖项。 在此示例中，我们已将 `azure-storage` 和 `azure-ml-api-sdk` 添加为 `Config/conda_dependencies.yml` 中的额外 Phthon 包。 我们还在 `Config/dsvm_spark_dependencies.yml` 中添加了 `spark.default.parallelism`、`spark.executor.instances`、`spark.executor.cores`。 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2.DSVM Docker 上的数据准备和功能设计

在 DSVM Docker 上运行脚本 `etl.py`。 使用调试参数，筛选具有特定服务器 IP 地址的已加载数据：

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

浏览到侧面板，选择“运行”以查看 `etl.py` 的运行历史记录。 请注意，需要约 2 分钟的运行时间。 如果计划通过更改代码来包括新功能，可提供 FILTER_IP 作为第二个参数，加快迭代速度。 在处理自己的机器学习问题时，可能需要多次运行该步骤，以了解数据集或创建新功能。 

通过对要加载的数据进行自定义限制并对要处理的数据进行进一步筛选，可以在模型开发中加快迭代过程。 在试验时，应定期将代码中所做的更改保存到 Git 存储库中。 请注意，我们使用了 `etl.py` 中的以下代码来实现对专用容器的访问：

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


接下来，在没有调试参数 FILTER_IP 的情况下在 DSVM Docker 上运行脚本 `etl.py`：

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

浏览到侧面板，选择“运行”以查看 `etl.py` 的运行历史记录。 请注意，需要约 4 分钟的运行时间。 该步骤的处理结果会保存到容器中，加载后即可在 train.py 中定型。 此外，字符串索引器、编码器管道和标准缩放器被保存到专用容器中， 并在实施中使用。 


##### <a name="3-model-training-on-dsvm-docker"></a>3.DSVM Docker 的模型定型

在 DSVM Docker 上运行脚本 `train.py`：

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

此步骤将加载 `etl.py` 运行的中间计算结果并定型机器学习模型。 此步骤需要约 2 分钟的时间。

成功完成小型数据的试验后，即可继续运行完整数据集的试验。 可以首先使用同一代码，然后试验参数和计算目标更改。  

####  <a name="model-development-on-the-hdinsight-cluster"></a>HDInsight 群集的模型开发

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1.在 Machine Learning Workbench 中为 HDInsight 群集创建计算目标

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

下述两个文件在 aml_config 文件夹中创建：
    
-  myhdi.compute：此文件包含远程执行目标的连接和配置信息。
-  myhdi.runconfig：此文件是一组在 Workbench 应用程序中使用的运行选项。


浏览到 myhdi.runconfig 并更改以下字段的配置，如下所示：

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

通过运行以下命令准备项目环境：

```az ml experiment prepare -c myhdi```

此步骤可能需要 7 分钟的时间。

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2.HDInsight 群集的数据准备和功能设计

在 HDInsight 群集上使用完整数据运行脚本 `etl.py`：

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

因为该作业持续相对较长的时间（约 2 小时），所以可以使用 `-a` 禁用输出流式处理。 作业完成后，可以在“运行历史记录”中查看驱动程序和控制器日志。 如果有较大的群集，可以始终重新配置 `Config/hdi_spark_dependencies.yml` 中的配置以使用更多的实例或核心。 例如，如果有一个四工作节点型群集，可以将 `spark.executor.instances` 的值从 5 增加到 7。 可以在存储帐户的 fullmodel 容器中看到该步骤的输出。 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3.HDInsight 群集的模型定型

在 HDInsight 群集上运行脚本 `train.py`：

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

因为该作业持续相对较长的时间（约 30 分钟），所以可以使用 `-a` 禁用输出流式处理。

#### <a name="run-history-exploration"></a>运行历史记录浏览

运行历史记录是一项 功能，允许在 Machine Learning Workbench 中进行试验跟踪。 默认情况下，它跟踪试验的持续时间。 在特定示例中，在该试验中转到 `Code/etl.py` 的完整数据集时，可以看到持续时间显著增加。 也可以记录用于跟踪的特定指标。 若要启用指标跟踪，请将以下代码行添加到 Phthon 文件的开头：
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
以下是跟踪特定指标的示例：

```python
run_logger.log("Test Accuracy", testAccuracy)
```

在 Workbench 的右侧边栏上浏览到“运行”，查看每个 Phthon 文件的运行历史记录。 也可转到 GitHub 存储库。 创建了名字以“AMLHistory”开头的新分支，以跟踪每次运行时对脚本所做的更改。 


### <a name="operationalize-the-model"></a>实施模型

在此部分，请实施在前述步骤中作为 Web 服务创建的模型。 另请了解如何使用 Web 服务来预测工作负荷。 使用计算机语言实施命令行接口 (CLI) 将代码和依赖项打包为 Docker 图像，并将模型作为容器化 Web 服务发布。

可以在 Machine Learning Workbench 中使用命令行提示符来运行 CLI。  也可以按照[安装指南](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md)在 Ubuntu Linux 上运行 CLI。 

> [!NOTE]
> 在下述所有命令中，将任何参数变量替换为实际值。 完成此部分需要约 40 分钟的时间。
> 

选择唯一字符串作为实施环境。 在这里，我们使用字符串“[unique]”来代表你选择的字符串。

1. 创建实施环境并创建资源组。

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   请注意，可以在 `az ml env setup` 命令中使用 `--cluster` 将容器服务用作环境。 可以在 [Azure 容器服务](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes)中实施机器学习模型。 它使用 [Kubernetes](https://kubernetes.io/) 对容器化应用程序进行自动部署、扩展和管理。 此命令需要约 20 分钟的运行时间。 请使用以下命令查看部署是否成功完成： 

        az ml env show -g [unique]rg -n [unique]

   通过运行以下命令将部署环境设置为刚创建的环境：

        az ml env set -g [unique]rg -n [unique]

2. 创建和使用模型管理帐户。 若要创建模型管理帐户，请运行以下命令：

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   通过运行以下命令将模型管理用于实施：

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   模型管理帐户用于管理模型和 Web 服务。 在 Azure 门户中，可以看到新的模型管理帐户已创建。 可以看到使用此模型管理帐户创建的模型、清单、Docker 映像和服务。

3. 下载和注册模型。

   将 fullmodel 容器中的模型下载到代码目录中的本地计算机。 请勿下载名为“vmlSource.parquet”的 parquet 数据文件。 它不是模型文件，而是中间计算结果。 也可以重复使用包括在 Git 存储库中的模型文件。 有关详细信息，请参阅 [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md)。 

   转到 CLI 中的 `Model` 文件夹并按以下要求注册模型：

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   每个命令的输出都会给出下一步中需要的模型 ID。 将它们保存为文本文件供以后使用。

4. 创建 Web 服务的清单。

   清单包括 Web 服务的代码、所有机器学习模型以及运行时环境依赖项。 转到 CLI 中的 `Code` 文件夹并运行以下命令：

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   输出将提供下一步所需的清单 ID。 

   停留在 `Code` 目录中，然后即可通过运行以下命令测试 webservice.py： 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. 创建 Docker 图像。 

        az ml image create -n [unique]image --manifest-id $manifestID

   输出会提供下一步需要的映像 ID。此 Docker 映像在容器服务中使用。 

6. 将 Web 服务部署到容器服务群集。

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   输出提供服务 ID。 需要使用该 ID 来获取授权密钥和服务 URL。

7. 在 Python 代码中调用 Web 服务，以微批的形式计分。

   使用以下命令获取授权密钥：

         az ml service keys realtime -i $ServiceID 

   使用以下命令获取服务计分 URL：

        az ml service usage realtime -i $ServiceID

   使用正确的服务计分 URL 和授权密钥修改 `./Config/webservice.json` 中的内容。 保留原始文件中的“Bearer”，替换“xxx”部分。 
   
   转到项目的根目录，并使用以下命令测试进行微批计分的 Web 服务：

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. 扩展 Web 服务。 

   有关详细信息，请参阅 [How to scale operationalization on your Azure Container Service cluster](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md)（如何在 Azure 容器服务群集上进行大规模实施）。
 

## <a name="next-steps"></a>后续步骤

本示例重点介绍如何使用 Machine Learning Workbench 定型大数据的机器学习模型并实施定型的模型。 具体说来，介绍了如何配置和使用不同的计算目标、如何运行跟踪指标的历史记录，以及如何使用不同的运行。

可以扩展代码以了解交叉验证和超参数优化。 若要详细了解交叉验证和超参数优化，请参阅[此 GitHub 资源](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)。  

若要详细了解时序预测，请参阅[此 GitHub 资源](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)。
