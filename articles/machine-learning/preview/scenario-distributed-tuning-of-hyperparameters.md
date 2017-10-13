---
title: "使用 Azure Machine Learning Workbench 执行超参数的分布式优化 | Microsoft Docs"
description: "此方案展示了如何使用 Azure Machine Learning Workbench 执行超参数的分布式优化"
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.topic: article
ms.author: dmpechyo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 643cea5cc134a2eb25a0dec4abefd9edca726332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>使用 Azure Machine Learning Workbench 执行超参数的分布式优化

此方案展示了如何使用 Azure Machine Learning Workbench 来横向扩展实现了 scikit-learn API 的机器学习算法的超参数的优化。 我们展示了如何配置和使用远程 Docker 容器和 Spark 群集作为执行后端来优化超参数。

## <a name="link-of-the-gallery-github-repository"></a>库 GitHub 存储库的链接
下面是公共 GitHub 存储库的链接： 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>用例概述

许多机器学习算法具有一个或多个旋钮，称为超参数。 这些旋钮可用来优化算法以针对根据用户指定的指标（例如，准确度、AUC、RMSE）度量的将来数据优化其性能。 基于训练数据构建模型时以及查看将来的测试数据之前，数据科学家需要提供超参数的值。 可以如何基于已知的训练数据来设置超参数的值，以便使模型对于未知的测试数据具有良好的性能？ 

用于优化超参数的一种常用方法是“网格搜索”加“交叉验证”。 交叉验证是一种评估方法，用于评估根据训练集训练的模型的好坏程度，针对测试集进行预测。 使用此方法时，我们首先将数据集分为 K 份，然后以循环方式针对除一份（称为保留份）之外的所有份将算法训练 K 次。 我们将针对 K 个保留份计算 K 个模型的指标的平均值。 此平均值称为“交叉验证的性能估计值”，取决于在创建 K 个模型时使用的超参数的值。 当优化超参数时，我们在候选超参数值的空间中进行搜索以查找可以优化交叉验证性能估计值的值。 网格搜索是一项常用的搜索技术，其中，多个超参数的候选值的空间是各个超参数的候选值集的叉积。 

使用交叉验证的网格搜索可能非常耗时。 如果某个算法有 5 个超参数，每个超参数有 5 个候选值，并且我们使用 K=5 份，那么，若要完成网格搜索，需要训练 5<sup>6</sup>=15625 个模型。 幸运的是，使用交叉验证的网格搜索是一个复杂的并行过程，所有这些模型都可以并行训练。

## <a name="prerequisites"></a>先决条件

* [Azure 帐户](https://azure.microsoft.com/free/)（提供免费试用版）。
* 按照用于安装 Workbench 并创建帐户的[安装和创建快速入门](./quickstart-installation.md)安装的 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 副本。
* 此方案假设在本地装有 Docker 引擎的 Windows 10 或 MacOS 上运行 Azure ML Workbench。 
* 若要使用远程 Docker 容器运行此方案，请按照[说明](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-provision-vm)预配 Ubuntu 数据科学虚拟机 (DSVM)。 建议使用至少具有 8 个核心和 28 GB 内存的虚拟机。 虚拟机的 D4 实例具有这样的容量。 
* 若要使用 Spark 群集运行此方案，请按照[说明](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)预配 HDInsight 群集。 建议使用至少具有 6 个工作节点的群集，并且建议在头节点和工作节点中都至少有 8 个核心和 28 GB 内存。 虚拟机的 D4 实例具有这样的容量。 若要最大程度地提高群集的性能，建议按照[说明](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-resource-manager)进行操作并编辑“custom spark defaults”部分中的定义来更改参数 spark.executor.instances、spark.executor.cores 和 spark.executor.memory。

     **故障排除**：你的 Azure 订阅对于可以使用的核心数可能具有配额。 Azure 门户不允许使用超出配额的总核心数创建群集。 若要查明配额，请在 Azure 门户中转到“订阅”部分，单击用来部署群集的订阅，然后单击“使用情况 + 配额”。 通常，配额是按 Azure 区域定义的，并且你可以选择在有足够空闲核心的区域中部署 Spark 群集。 

* 创建一个用于存储数据集的 Azure 存储帐户。 请按照[说明](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account)创建一个存储帐户。

## <a name="data-description"></a>数据说明

我们使用 [TalkingData 数据集](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data)。 此数据集包含来自手机中的应用的事件。 目标是根据给定的手机类型和用户最近生成的事件预测手机用户的性别和年龄类别。  

## <a name="scenario-structure"></a>方案结构
此方案在 GitHub 存储库中有多个文件夹。 代码和配置文件位于 **Code** 文件夹中，所有文档位于 **Docs** 文件夹中，所有图像位于 **Images** 文件夹中。 根文件夹中有一个 README 文件，其中包含此方案的摘要。

### <a name="getting-started"></a>入门
单击 Azure Machine Learning Workbench 图标以运行 Azure Machine Learning Workbench 并基于“超参数的分布式优化”模板创建一个项目。 可以在[安装和创建快速入门](quickstart-installation.md)中找到有关如何创建新项目的详细说明。   

### <a name="configuration-of-execution-environments"></a>执行环境的配置
我们将展示如何在远程 Docker 容器和 Spark 群集中运行我们的代码。 首先我们将介绍两种环境共有的设置。 

我们将使用 Azure Machine Learning Workbench 的默认 Docker 容器中没有提供的 [scikit-learn](https://anaconda.org/conda-forge/scikit-learn)、[xgboost](https://anaconda.org/conda-forge/xgboost) 和 [azure-storage](https://pypi.python.org/pypi/azure-storage) 包。 azure-storage 包要求安装 [cryptography](https://pypi.python.org/pypi/cryptography) 和 [azure](https://pypi.python.org/pypi/azure) 包。 为了在 Docker 容器和 Spark 群集的节点中安装这些包，我们将修改 conda_dependencies.yml 文件：

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

修改后的 conda\_dependencies.yml 文件存储在教程的 aml_config 目录中。 

在下面的步骤中，我们将执行环境连接到 Azure 帐户。 通过单击 AML Workbench 的左上角中的“文件”菜单并选择“打开命令提示符”打开命令行窗口 (CLI)。 然后，在 CLI 中运行以下命令：

    az login

将会收到一条消息

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

转到此网页，输入代码并登录到你的 Azure 帐户。 在此步骤后，在 CLI 中运行以下命令：

    az account list -o table

查找包含你的 AML Workbench Workspace 帐户的 Azure 订阅的订阅 ID。 最后，在 CLI 中运行以下命令：

    az account set -s <subscription ID>

以完成与你的 Azure 订阅的连接。

在接下来的两部分中，我们将展示如何完成远程 Docker 和 Spark 群集的配置。

#### <a name="configuration-of-remote-docker-container"></a>远程 Docker 容器的配置

 若要设置远程 Docker 容器，请在 CLI 中使用 DSVM 中的 IP 地址、用户名和密码运行以下命令：

    az ml computetarget attach --name dsvm --address <IP address> --username <username> --password <password> --type remotedocker

。 可以在 Azure 门户中在你的 DSVM 页面的“概述”部分中找到 DSVM 的 IP 地址：

![VM IP](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Spark 群集的配置

若要设置 Spark 环境，请在 CLI 中使用群集的名称、群集的 SSH 用户名和密码运行以下命令：

    az ml computetarget attach --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> --type cluster

。 SSH 用户名的默认值是 `sshuser`，除非在预配群集的过程中更改了该值。 可以在 Azure 门户中在你的群集页面的“属性”部分中找到群集的名称：

![群集名称](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

我们使用 spark-sklearn 包将 Spark 作为执行环境，用于进行超参数的分布式优化。 使用 Spark 执行环境时，我们修改了 spark_dependencies.yml 文件来安装此包：

    configuration: {}
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

修改后的 spark\_dependencies.yml 文件存储在教程的 aml_config 目录中。 

### <a name="data-ingestion"></a>数据引入
此方案中的代码假设数据存储在 Azure blob 存储中。 首先，我们将展示如何从 Kaggle 站点将数据下载到计算机并将其上传到 blob 存储。 然后，我们将展示如何从 blob 存储中读取数据。 

若要从 Kaggle 下载数据，请转到[数据集页面](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data)并单击“下载”按钮。 将会要求你登录到 Kaggle。 在登录后，会重定向回数据集页面。 然后，通过选择左栏中的前七个文件并单击“下载”按钮来下载它们。 下载的文件的总大小为 289 MB。 若要将这些文件上传到 blob 存储，请在你的存储帐户中创建 blob 存储容器“dataset”。 可以通过转到你的存储帐户的 Azure 页面并依次单击“Blob”和“+容器”来执行该操作。 输入“dataset”作为名称并单击“确定”。 下面的各个屏幕截图展示了这些步骤：

![打开 blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![打开容器](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

从列表中选择数据集容器后，单击“上传”按钮。 Azure 门户允许同时上传多个文件。 在“上传 blob”部分中，单击文件夹按钮，从数据集中选择所有文件，单击“打开”，然后单击“上传”。 下面的屏幕截图展示了以下步骤：

![上传 blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

文件上传需要花费数分钟时间，具体取决于 Internet 连接。 

在代码中，我们使用 [Azure 存储 SDK](https://azure-storage.readthedocs.io/en/latest/) 将数据集从 blob 存储下载到当前的执行环境。 下载是通过 load_data.py 文件中的 load\_data() 函数执行的。 若要使用此代码，需要将 <ACCOUNT_NAME> 和 <ACCOUNT_KEY> 替换为承载着该数据集的存储帐户的名称和主密钥。 帐户名称显示在存储帐户的 Azure 页面的左上角中。 若要获取帐户密钥，请在存储帐户的 Azure 页面中选择“访问密钥”（请参阅“数据引入”部分中的第一个屏幕截图），然后复制密钥列的第一行中的长字符串：
 
![访问密钥](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

load_data() 函数中的以下代码下载单个文件：

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

注意，不需要手动运行 load_data.py 文件。 以后将从其他文件调用该文件。

### <a name="feature-engineering"></a>特性工程
用于计算所有特征的代码位于 feature\_engineering.py 文件中。 不需要手动运行 feature_engineering.py 文件。 以后将从其他文件调用该文件。

我们将创建多个特征集：
* 手机品牌和型号的独热编码（one\_hot\_brand_model 函数）
* 用户每个工作日生成的事件的一部分（weekday\_hour_features 函数）
* 用户每小时生成的事件的一部分（weekday\_hour_features 函数）
* 用户在每个工作日和小时组合中生成的事件的一部分（weekday\_hour_features 函数）
* 用户在每个应用中生成的事件的一部分（one\_hot\_app_labels 函数）
* 用户在每个应用标签中生成的事件的一部分（one\_hot\_app_labels 函数）
* 用户在每个应用类别中生成的事件的一部分（text\_category_features 函数）
* 用来生成事件的应用的类别指标特征（one\_hot_category 函数）

这些特征是由 Kaggle 内核（ [针对应用和标签的一个线性模型](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels)）产生的。

计算这些特征需要大量内存。 最初，我们在具有 16 GB RAM 的本地环境中计算机特征。 我们能够计算前四个特征集，但是在计算第五个特征集时，出现了“内存不足”错误。 前四个特征集的计算在 singleVMsmall.py 文件中，可以通过在 CLI 窗口中运行以下命令在本地环境中执行它： 

     az ml experiment submit -c local .\singleVMsmall.py   

。

因为本地环境太小，无法计算所有功能集，因此，我们切换到具有更大内存的远程 DSVM。 在 DSVM 内的执行是在由 AML Workbench 管理的 Docker 容器内完成的。 使用此 DSVM，我们能够计算所有特征、对模型进行训练以及对超参数进行优化（请参阅下一部分）。 singleVM.py 文件包含完整的特征计算和建模代码。 在下一部分中，我们将展示如何在远程 DSVM 中运行 singleVM.py。 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>使用远程 DSVM 优化超参数
我们将使用梯度树提升的 [xgboost](https://anaconda.org/conda-forge/xgboost) 实现 [1]。 我们将使用 [scikit-learn](http://scikit-learn.org/) 包来优化 xgboost 的超参数。 虽然 xgboost 不是 scikit-learn 包的一部分，但是它实现了 scikit-learn API，因此可以与 scikit-learn 的超参数优化函数一起使用。 

Xgboost 有八个超参数：
* n_esitmators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* subsample
* objective 可以在[此处](http://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.sklearn)和[此处](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md)找到这些超参数的说明。 最初，我们使用远程 DSVM 并使用一个小网格范围内的候选值对超参数进行优化：

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

此网格具有超参数值的四个组合。 我们将使用 5-份交叉验证，导致 xgboost 运行 4x5=20 次。 为了度量模型的性能，我们使用负对数损失指标。 下面的代码从网格中查找使交叉验证的负对数损失最大的超参数值。 此代码还使用这些值基于整个训练集对最终模型进行训练：

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

在创建模型后，我们保存超参数优化的结果。 我们使用 AML Workbench 的日志记录 API 来保存超参数的最佳值和对应的负对数损失的交叉验证估计值：

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

我们还将创建 sweeping_results.txt 文件，使其中包含网格中超参数值的所有组合的交叉验证负对数损失：

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

此文件存储在一个特殊的 ./outputs 目录中。 稍后我们将展示如何下载它。  

 首次在远程 DSVM 中运行 singleVM.py 之前，我们将通过在 CLI 窗口中运行以下命令来创建一个 Docker 容器： 

    az ml experiment prepare -c dsvm

。 创建 Docker 容器需要花费数分钟时间。 在 DSVM 中运行 singleVM.py 之后：

    az ml experiment submit -c dsvm .\singleVM.py

当 DSVM 有 8 个核心和 28 GB 内存时，此命令在 1 小时 38 分钟内完成。 可以在 AML Workbench 的“运行历史记录”窗口中查看所记录的值：

![运行历史记录](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

默认情况下，“运行历史记录”窗口会显示前 1-2 个所记录值的值和图形。 若要查看超参数的所选值的完整列表，请单击上一屏幕截图中标有红圈的设置图标，并选择要在表中显示的超参数。 另外，若要选择在“运行历史记录”窗口的上半部分中显示的图形，请单击标有蓝圈的设置图标，并从列表中选择图形。 

还可以在“运行属性”窗口中查看超参数的所选值： 

![运行属性](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

“运行属性”窗口的右上角有一个“输出文件”部分，其中包含在执行环境的“.\output”文件夹中创建的所有文件的列表。 可以通过从那里选择 sweeping\_results.txt 并单击“下载”按钮来下载该文件。 sweeping_results.txt 应当具有以下输出：

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>使用 Spark 群集优化超参数
我们将使用 Spark 群集来横向扩展优化超参数并使用更大的网格。 我们的新网格为

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

此网格具有超参数值的 16 个组合。 因为我们使用 5-份交叉验证，所以会将 xgboost 运行 16x5=80 次。

使用 Spark 群集时，scikit-learn 包未提供对优化超参数的本机支持。 幸运的是，Databricks 中的 [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) 包填补了此空白。 此包提供了 GridSearchCV 函数，该函数具有与 scikit-learn 中的 GridSearchCV 函数几乎相同的 API。 若要使用 spark-sklearn 并使用 Spark 优化超参数，需要进行连接以创建 Spark 上下文

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

然后我们将 

    from sklearn.model_selection import GridSearchCV

替换为 

    from spark_sklearn import GridSearchCV

另外，我们还将对 scikit-learn 中的 GridSearchCV 的调用替换为对 spark-sklearn 中的相应函数的调用：

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

使用 Spark 对超参数进行优化的最终代码位于 distributed\_sweep.py 文件中。 singleVM.py 与 distributed_sweep.py 之间的差异在于网格的定义和另加的四行代码。 另请注意，由于 AML Workbench 服务的原因，将执行环境从远程 DSVM 更改到 Spark 群集时，日志记录代码不会更改。

首次在 Spark 群集中运行 distributed_sweep.py 时，需要在该群集中安装 Python 包。 可以通过在 CLI 窗口中运行以下命令来实现此目的： 

    az ml experiment prepare -c spark

。 此安装需要花费数分钟时间。 在 Spark 群集中运行 distributed_sweep.py：

    az ml experiment submit -c spark .\distributed_sweep.py

当 Spark 群集有 6 个工作节点和 28 GB 内存时，此命令可在 1 小时 6 分钟内完成。 可以采用与在远程 DSVM 执行中相同的方式在 Azure Machine Learning Workbench 中访问在 Spark 群集中优化超参数的结果（也就是日志）、超参数的最佳值和 sweeping_results.txt 文件。 

### <a name="architecture-diagram"></a>体系结构关系图

下面的关系图显示了端到端工作流： ![体系结构](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>结束语 

在此方案中，我们已展示了如何使用 Azure Machine Learning Workbench 在远程虚拟机和远程 Spark 群集中执行超参数的优化。 我们看到，Azure Machine Learning Workbench 提供了用于轻松配置执行环境以及在它们之间进行切换的工具。 

## <a name="references"></a>参考

[1] T. Chen 和 C. Guestrin 合著的 [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/abs/1603.02754)。 KDD 2016 年出版。




