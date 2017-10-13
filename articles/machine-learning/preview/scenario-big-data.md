---
title: "基于 TB 数据的服务器工作负荷预测 - Azure | Microsoft Docs"
description: "如何使用 Azure ML Workbench 定型大数据的机器学习模型。"
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: b76253fad43be231591023c4d4466bf6e3f329a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="server-workload-forecasting-on-terabytes-data"></a>基于 TB 数据的服务器工作负荷预测

此示例说明数据科学家如何使用 Azure ML Workbench 开发需要使用大数据的解决方案。 我们介绍用户如何通过使用 Azure ML Workbench 顺利地完成从单个大型数据集示例开始遍历数据准备、功能设计和机器学习，然后最终将此过程延伸至整个大型数据集。 

与此同时，我们还将演示 Azure ML Workbench 的以下关键功能：
* 在计算目标之间轻松切换：我们介绍用户如何设置不同的计算目标并在试验中使用这些目标。 在该示例中，我们将 Ubuntu DSVM 和 HDInsight 群集用作计算目标。 此外，我们还向用户演示如何根据资源的可用性配置计算目标。 特别是在向外扩展 Spark 群集后（即，在 Spark 群集中包括更多工作节点），用户如何在整个 Azure ML Workbench 中使用资源以加速试验运行。
* 运行历史记录跟踪：我们向用户演示如何使用 Azure ML Workbench 来跟踪 ML 模型的性能和感兴趣的其他指标。
* 机器学习模型的实施：我们将演示如何使用 Azure ML Workbench 中的内置工具将定型的 ML 模型部署为 Azure 容器服务 (ACS) 上的 Web 服务。 此外，我们还将演示如何使用 Web 服务通过 REST API 调用获取微批处理预测。 
* 支持 TB 数据。



## <a name="link-to-the-gallery-github-repository"></a>到库 GitHub 存储库的链接

该示例的公共 GitHub 存储库包含所有材料，其中包括代码示例： 
 
[https://github.com/Azure/MachineLearningSamples-BigData](https://github.com/Azure/MachineLearningSamples-BigData)



## <a name="use-case-overview"></a>用例概述


对于管理自己的基础结构的技术公司而言，预测服务器上的工作负荷是常见的业务需求。 若要降低基础结构成本，应将在利用率不足的服务器上运行的服务分组在一起，在较少数量的计算机上运行，而对于在工作负荷繁重的服务器上运行的服务，应为它们分配更多可供运行的计算机。 在此场景中，我们重点介绍针对每台计算机（或服务器）的工作负荷预测。 特别是我们使用每台服务器上的会话数据来预测未来服务器的工作负荷类别。 我们使用 [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html) 中的随机林分类器将每台服务器的负荷分为低、中和高三个类别。 该示例中的机器学习技术和工作流可以被轻松地延伸至其他类似问题。 


## <a name="prerequisites"></a>先决条件

运行此示例的先决条件如下所示：

* [Azure 帐户](https://azure.microsoft.com/free/)（提供免费试用版）。
* 按照[快速入门安装指南](./quickstart-installation.md)安装 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的副本，以安装程序并创建工作区。
* 该场景假定你在 Windows 10 上运行 Azure Machine Learning (ML) Workbench。 如果使用 macOS，则操作说明大体上是相同的。
* 适用于 Linux (Ubuntu) 的数据科学虚拟机 (DSVM)。 可以按照[说明](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm)设置 Ubuntu DSVM。 单击[此处](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu)快速启动。 我们建议使用至少具有 8 个内核和 32 GB 内存的虚拟机。  需要 DSVM IP 地址、用户名和密码以尝试该示例。 将下表与 DSVM 信息一起保存，以供在后续步骤中使用：

 字段名称| 值 |  
 |------------|------|
DSVM IP 地址 | xxx|
 用户名  | xxx|
 密码   | xxx|

 可以将任何虚拟机 (VM) 与安装的 [Docker 引擎](https://docs.docker.com/engine/)结合使用。

* 具有 HDP 版本 3.6 和 Spark 版本 2.1.x 的 HDInsight Spark 群集。 访问 [在 Azure HDInsight 中创建 Apache Spark 群集] (https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)，了解如何创建 HDInsight 群集的详细信息。 我们建议使用三个工作群集，其中每个工作群集具有 16 个内核和 112 GB 的内存。 或者，只需为头节点选择虚拟机类型“`D12 V2`”，为工作节点选择“`D14 V2`”。 群集的部署需要约 20 分钟。 尝试该示例需要群集名、SSH 用户名和密码。 将下表与 Azure HDInsight 群集信息一起保存，以供在后续步骤中使用：

 字段名称| 值 |  
 |------------|------|
 群集名称| xxx|
 用户名  | xxx（默认情况下是 sshuser）|
 密码   | xxx|


* 一个 Azure 存储帐户。 可以按照[说明](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)创建 Azure 存储帐户。 此外，在该存储帐户中创建分别名为“`fullmodel`”和“`onemonthmodel`”的两个专用 Blob 容器。 存储帐户用于保存中间计算结果和机器学习模型。 需要使用存储帐户名和访问密钥来尝试此示例。 将下表与 Azure 存储帐户信息一起保存，以供在后续步骤中使用：

 字段名称| 值 |  
 |------------|------|
 存储帐户名称| xxx|
 访问密钥  | xxx|


必备组件列表中创建的 Ubuntu DSVM 和 Azure HDInsight 群集是计算目标。 计算目标是 Azure ML Workbench 环境中的计算源，它可能与运行 Azure ML Workbench 的计算机不同。   

## <a name="create-a-new-workbench-project"></a>新建 Workbench 项目

使用本示例作为模板，创建一个新项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“TB 数据的工作负荷预测”，并选择模板
5.  单击“创建” 

可以按照该[说明](./tutorial-classifying-iris-part-1.md)创建包含预先创建的 git 存储库的 Azure ML Workbench 项目。  
运行 git 状态以检查用于版本跟踪的文件状态。

## <a name="data-description"></a>数据说明

场景中使用的数据是合成服务器工作负荷数据，托管在可以公开访问的 Azure Blob 存储帐户中。 可以在 [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) 的 `dataFile` 字段中找到特定存储帐户信息。 可以直接使用 Azure Blob 存储中的数据。 如果许多用户同时使用存储，可以选择使用 [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) 将数据下载到自己的存储中。 

总数据大小约为 1 TB。 每个文件约为 1-3 GB，且是不含标头的 CSV 文件格式。 每行数据代表某个特定服务器上的事务负荷。  数据架构的详细信息如下所示：

列号 | 字段名称| 类型 | 说明 |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Datetime |    会话开始时间
#N/A  |`SessionEnd`    | Datetime | 会话结束时间
3 |`ConcurrentConnectionCounts` | Integer | 并发连接数
4 | `MbytesTransferred` | Double | 以兆字节为单位传输的规范化数据
5 | `ServiceGrade` | Integer |  会话服务等级
6 | `HTTP1` | Integer|  会话是使用 HTTP1 还是 HTTP2
7 |`ServerType` | Integer   |服务器类型
8 |`SubService_1_Load` | Double |   子服务 1 负荷
9 | `SubService_1_Load` | Double |  子服务 2 负荷
10 | `SubService_1_Load` | Double |     子服务 3 负荷
11 |`SubService_1_Load` | Double |  子服务 4 负荷
12 | `SubService_1_Load`| Double |      子服务 5 负荷
13 |`SecureBytes_Load`  | Double | 安全字节负荷
14 |`TotalLoad` | Double | 服务器总负荷
15 |`ClientIP` | String|    客户端 IP 地址
16 |`ServerIP` | String|    服务器 IP 地址



请注意，尽管预期的数据类型在上表中已经列出，但是由于缺失值和脏数据的问题，无法保证数据为预期的类型，所以在处理数据时应考虑这一点。 


## <a name="scenario-structure"></a>场景结构

本示例中的文件按如下方式组织。

| 文件名 | 类型 | 说明 |
|-----------|------|-------------|
| `Code` | 文件夹 | 包含该示例中所有代码的文件夹 |
| `Config` | 文件夹 | 包含配置文件的文件夹 |
| `Image` | 文件夹 | 用于保存自述文件的图像的文件夹 |
| `Model` | 文件夹 | 用于保存从 Azure Blob 存储下载的模型文件的文件夹 |
| `Code/etl.py` | Python 文件 | 用于数据准备和功能设计的 Python 文件 |
| `Code/train.py` | Python 文件 | 用于定型三类多分类模型的 Python 文件  |
| `Code/webservice.py` | Python 文件 | 用于实施的 Python 文件  |
| `Code/scoring_webservice.py` | Python 文件 |  用于数据转换和调用 Web 服务的 Python 文件 |
| `Code/O16Npreprocessing.py` | Python 文件 | 用于对 scoring_webservice.py 的数据进行预处理的 Python 文件。  |
| `Code/util.py` | Python 文件 | 包含用于读取和写入 Azure Blob 的代码的 Python 文件。  
| `Config/storageconfig.json` | JSON 文件 | 存储中间结果和模型以用于处理和定型一个月数据的 Azure Blob 容器的配置文件 |
| `Config/fulldata_storageconfig.json` | Json 文件 |  存储中间结果和模型以用于处理和定型完整数据集的 Azure Blob 容器的配置文件|
| `Config/webservice.json` | JSON 文件 | scoring_webservice.py 的配置文件|
| `Config/conda_dependencies.yml` | YAML 文件 | Conda 依赖项文件 |
| `Config/conda_dependencies_webservice.yml` | YAML 文件 | Web 服务的 Conda 依赖项文件|
| `Config/dsvm_spark_dependencies.yml` | YAML 文件 | Ubuntu DSVM 的 Spark 依赖项文件 |
| `Config/hdi_spark_dependencies.yml` | YAML 文件 | HDInsight Spark 群集的 Spark 依赖项文件 |
| `README.md` | Markdown 文件 | 自述 Markdown 文件 |
| `Code/download_model.py` | Python 文件 | 用于将模型文件从 Azure Blob 下载到本地磁盘的 Python 文件 |
| `Docs/DownloadModelsFromBlob.md` | Markdown 文件 | 包含如何运行 `Code/download_model.py` 的说明的 Markdown 文件 |



### <a name="data-flow"></a>数据流

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 中的代码从可以公开访问的容器（[`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) 的 `dataFile` 字段）加载数据。 它包括数据准备和功能设计，并将中间计算结果和模型保存到自己的专用容器中。 [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) 中的代码从专用容器加载中间计算结果、定型多类分类模型，并最终将定型的机器学习模型写入专用容器中。 建议用户将一个容器用于一个月数据集的试验，然后将另一个容器用于完整数据集的试验。 因为数据和模型保存为 Parquet 文件，所以每个文件实际上是容器中的一个文件夹，包含多个 Blob。 生成的容器如下所示：

| Blob 前缀名称 | 类型 | 说明 |
|-----------|------|-------------|
| featureScaleModel | Parquet | 数字功能的标准扩展器模型 |
| stringIndexModel | Parquet | 非数字功能的字符串索引器模型|
| oneHotEncoderModel|Parquet | 分类功能的独热编码器模型 |
| mlModel | Parquet | 定型的机器学习模型 |
| info| Python pickle 文件 | 有关转换数据的信息，其中包括定型启动、定型结束、持续时间、定型测试拆分的时间戳和用于编制索引以及进行独热编码的列。

上表中的所有文件/blob 均用于实施。


### <a name="model-development"></a>模型开发

#### <a name="architecture-diagram"></a>体系结构关系图


下图演示使用 Azure ML Workbench 开发模型的端到端工作流：![体系结构](media/scenario-big-data/architecture.PNG)



接下来，我们演示如何使用 Azure ML orkbench 中的远程计算目标功能完成模型开发。 我们首先加载一个小型示例数据，并在 Ubuntu DSVM 上运行脚本 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 以实现快速迭代。 我们可以通过传递额外参数以实现更快速的迭代，从而进一步限制我们在 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 中执行的操作。 最后，我们使用 HDInsight 群集定型完整数据。     

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 文件执行加载数据、数据准备和功能设计操作。 它接受两个参数：(1) 用于存储中间计算结果和模型的 Azure Blob 存储容器的配置文件，(2) 用于实现更快速迭代的调试配置文件。

第一个参数 `configFilename` 是本地配置文件，你在该文件中存储 Azure Blob 存储信息并指定加载数据的位置。 默认情况下，它是 [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json)，并将在一个月数据运行中使用。 我们还加入了 [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json)，完整数据集运行中将需要使用。 配置中的内容如下所示： 

| 字段 | 类型 | 说明 |
|-----------|------|-------------|
| storageAccount | String | Azure 存储帐户名称 |
| storageContainer | String | Azure 存储帐户中用于存储中间结果的容器 |
| storageKey | String |Azure 存储帐户访问密钥 |
| dataFile|String | 数据源文件  |
| duration| String | 数据源文件中的数据持续时间|

同时修改 `Config/storageconfig.json` 和 `Config/fulldata_storageconfig.json` 以配置存储帐户、存储密钥和 Blob 容器，以存储中间结果。 默认情况下，一个月数据运行的 Blob 容器是“`onemonthmodel`”，完整数据集运行的 Blob 容器是“`fullmodel`”。请确保在存储帐户中创建这两个容器。 [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) 中的 `"dataFile"` 字段配置 [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) 中加载的数据，而 `"duration"` 配置数据包括的范围。 如果持续时间设置为“ONE_MONTH”，则所加载的数据应为 2016 年 6 月数据的七个文件中的一个 CSV 文件。 如果持续时间为“FULL”，则加载完整数据集，为 1 TB。 无需在这两个配置文件中更改 `"dataFile"` 和 `"duration"`。

第二个参数是 DEBUG。 将其设置为“FILTER_IP”可实现更快速的迭代。 想要调试你的脚本时，使用该参数很有帮助。

> [!NOTE]
> 将所有以下命令中的任何参数变量替换为实际值。
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>在 Ubuntu DSVM 的 Docker 上开发模型

#####  <a name="1-setting-up-the-compute-target-for-docker-on-ubuntu-dsvm"></a>1.为 Ubuntu DSVM 上的 Docker 设置计算目标

通过单击 Azure ML Workbench 左上角的“文件”菜单启动 Azure ML Workbench 的命令行，选择“打开命令提示符”，然后运行 

```az ml computetarget attach --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password --type remotedocker```

命令行成功完成执行后，将看到在你的项目的 aml_config 文件夹中创建了以下两个文件：

    dockerdsvm.compute: contains the connection and configuration information for a remote execution target
    dockerdsvm.runconfig: set of run options used when executing within the Azure ML Workbench application

导航到 dockerdsvm.runconfig 并将以下字段的配置更改为如下所示：

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

通过运行以下命令准备项目环境：

```az ml experiment prepare -c dockerdsvm```


将“PrepareEnvironment”设置为 true 表示允许 Azure ML Workbench 在你提交作业时创建运行时环境。 `Config/conda_dependencies.yml` 和 `Config/dsvm_spark_dependencies.yml` 包含运行时环境的自定义。 可以始终通过编辑这两个 YMAL 文件来修改 Conda 依赖项、Spark 配置和 Spark 依赖项。 在该示例中，我们将 `azure-storage` 和 `azure-ml-api-sdk` 添加为 `Config/conda_dependencies.yml` 中的额外 Phthon 程序包，并在 `Config/dsvm_spark_dependencies.yml` 中添加了“`spark.default.parallelism`”、“`spark.executor.instances`”和“`spark.executor.cores`”等。 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2.DSVM Docker 上的数据准备和功能设计

使用调试参数（该参数筛选具有特定服务器 IP 地址的已加载数据）在 DSVM Docker 上运行脚本 `etl.py`：

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

导航到侧面板，单击“运行”以查看 `etl.py` 的运行历史记录。 请注意，需要约 2 分钟的运行时间。 如果计划更改代码以包括新功能，提供 FILTER_IP 作为第二个参数可实现更快速的迭代。 在处理自己的机器学习问题时，你可能需要多次运行该步骤，以了解数据集或创建新功能。 通过对要加载的数据进行自定义限制并对要处理的数据进行进一步筛选，可以在你的模型开发中加快迭代过程。 在试用时，应定期将代码中所做的更改保存到 git 存储库中。  请注意，我们使用了 `etl.py` 中的以下代码来实现对专用容器的访问：

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


接下来，在没有调试参数 FILTER_IP 的情况下在 DSVM Docker 上运行脚本 `etl.py`

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

导航到侧面板，单击“运行”以查看 `etl.py` 的运行历史记录。 请注意，需要约 4 分钟的运行时间。 该步骤的处理结果会保存到容器中并进行加载，以供在 train.py 中定型。 此外，字符串索引器、编码器管道和标准缩放器也被保存到专用容器中并在实施中使用 (O16N)。 


##### <a name="3-model-training-on-dsvm-docker"></a>3.DSVM Docker 的模型定型

在 DSVM Docker 上运行脚本 `train.py`：

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

此步骤将加载 `etl.py` 运行的中间计算结果并定型机器学习模型。 此步骤需要约 2 分钟的时间。

成功完成小型数据的试验后，就可以继续运行完整数据集的试验。 可以首先使用同一代码，然后试验参数和计算目标更改。  

####  <a name="model-development-on-the-hdinsight-cluster"></a>HDInsight 群集的模型开发

##### <a name="1-create-compute-target-in-azure-ml-workbench-for-the-hdinsight-cluster"></a>1.在 Azure ML workbench 中为 HDInsight 群集创建计算目标

```az ml computetarget attach --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password --type cluster```

命令行成功完成后，就能看到在 aml_config文件夹中创建了以下两个文件：
    
    myhdo.compute: contains connection and configuration information for a remote execution target
    myhdi.runconfig: set of run options used when executing within the Azure ML Workbench application


导航到 myhdi.runconfig 并将以下字段的配置更改为如下所示：

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

通过运行以下命令准备项目环境：

```az ml experiment prepare -c myhdi```

此步骤可能需要 7 分钟的时间。

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2.HDInsight 群集的数据准备和功能设计

在 HDInsight 群集上运行脚本 `etl.py` 与 fulldata

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

因为该作业持续相对较长的时间（约 2 小时），所以我们可以使用“-a”禁用输出流。 作业完成后，在“运行历史记录”中，可以进一步查看驱动程序日志和控制器日志。 如果有较大的群集，可以始终重新配置 `Config/hdi_spark_dependencies.yml` 中的配置以使用更多的实例或内核。 例如，如果有四个工作节点群集，可以将“`spark.executor.instances`”值从 5 增加到 7。 可以在存储帐户中的“fullmodel”容器中看到该步骤的输出。 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3.HDInsight 群集的模型定型

在 HDInsight 群集上运行脚本 `train.py`：

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

因为该作业持续相对较长的时间（约半小时），所以我们使用“-a”禁用输出流。

#### <a name="run-history-exploration"></a>运行历史记录浏览

运行历史记录是在 Azure ML Workbench 中进行试验跟踪的一项功能。 默认情况下，它跟踪试验的持续时间。 在我们的特定示例中，当我们在该试验中转到“`Code/etl.py`”的完整数据集时，我们注意到持续时间显著增加。 也可以记录用于跟踪的特定指标。 若要启用指标跟踪，将以下代码行添加到 Phthon 文件的开头：
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

导航到 Azure ML Workbench 的右侧边栏上的“运行”，以查看每个 Phthon 文件的运行历史记录。 此外，转到你的 github 存储库，创建了名字以“AMLHistory”开头的新分支，以跟踪每次运行时对你的脚本所做的更改。 


### <a name="operationalization"></a>实施

在本部分中，我们将前面步骤中创建的模型作为 Web 服务实施并演示如何使用 Web 服务来预测工作负荷。 我们使用 Azure ML 实施命令行接口 (CLI) 将代码和依赖项打包为 Docker 图像，并将模型作为容器化 Web 服务发布。 有关详细信息，请参阅[实施概述](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md)。 可以使用 Azure ML Workbench 中的命令行提示符运行 Azure ML 实施 CLI。  也可以按照[安装指南](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md)在 Ubuntu Linux 上运行 Azure ML 实施 CLI。 

> [!NOTE]
> 将所有以下命令中的任何参数变量替换为实际值。 完成该部分需要约 40 分钟的时间。
> 


选择一个唯一字符串作为实施环境，我们使用字符串“[unique]”代表所选的字符串。

1. 创建实施环境并创建资源组。

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   请注意，我们通过使用 `az ml env setup` 命令中的 `--cluster` 将 Azure 容器服务用作环境。 我们选择在 [Azue 容器服务](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes)上实施机器学习模型，因为它使用 [Kubernetes](https://kubernetes.io/) 实现容器化应用程序的自动化部署、扩展和管理。该命令需要约 20 分钟的运行时间。 用途 

        az ml env show -g [unique]rg -n [unique]

   查看部署是否成功完成。

   通过运行以下命令将部署环境设置为刚创建的环境

        az ml env set -g [unique]rg -n [unique]

2. 创建模型管理帐户和使用模型管理帐户。

   通过运行以下命令创建模型管理帐户

    az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   通过运行以下命令将模型管理用于实施

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   模型管理帐户用于管理模型和 Web 服务。 从 Azure 门户中，可以看到创建了新的模型管理帐户，可以使用该帐户查看模型、清单、Docker 图像和使用该模型管理帐户创建的服务。

3. 下载和注册模型。

   将“fullmodel”容器中的模型下载到代码目录中的本地计算机。 不要下载名为“vmlSource.parquet”的 parquet 数据文件，因为它不是模型文件，而是中间计算结果。 也可以重复使用我们在 git 存储库中加入的模型文件。 访问 [DownloadModelsFromBlob.md](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md) 了解下载 parquet 文件的详细信息。 

   转到 CLI 中的 `Model` 文件夹并按以下要求注册模型：

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   每个命令输出都会给出下一步中需要的模型 ID。 将它们保存为文本文件供以后使用。

4. 创建 Web 服务的清单。

   清单是用于创建 Web 服务容器的 Docker 图像的方法。 它包括 Web 服务的代码、所有机器学习模型以及运行时环境依赖项。  转到 CLI 中的 `Code` 文件夹并运行以下命令行：

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   输出将提供下一步所需的清单 ID。 

   停留在 `Code` 目录中，可以通过运行以下命令测试 webservice.py 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. 创建 Docker 图像。 

        az ml image create -n [unique]image --manifest-id $manifestID

   输出将提供下一步需要的图像 ID，因为该 Docker 图像在 ACS 中使用。 

6. 将 Web 服务部署到 ACS 群集

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   输出将提供服务 ID，你需要使用该 ID 来获取授权密钥和服务 URL。

7. 调用 Python 代码中的 Web 服务以在微批处理中评分。

   使用以下命令获取授权密钥

         az ml service keys realtime -i $ServiceID 

   并使用以下命令获取服务计分 URL

        az ml service usage realtime -i $ServiceID

   使用正确的服务计分 URL 和授权密钥修改 `./Config/webservice.json` 中的内容（保留原始文件中的“Bearer”并替换“xxx”部分）。 
   
   转到项目的根目录，并使用以下命令测试微批处理的 Web 服务

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. 扩展 Web 服务。 

   请参阅[如何在 ACS 群集上扩展实施](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md)以扩展 Web 服务。
 

## <a name="conclusion"></a>结束语

该示例重点介绍如何使用 Azure ML Workbench 定型大数据的机器学习模型并实施定型的模型。 特别是介绍了如何：

* 配置和使用不同的计算目标。

* 跟踪指标和不同运行的运行历史记录。

* 实施。

用户可以扩展代码以了解交叉验证和超参数优化。 若要了解有关交叉验证和超参数优化的详细信息，请参阅 https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning。  
若要了解有关时序预测的详细信息，请参阅 https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting。
