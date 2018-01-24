---
title: "航拍图像分类 | Microsoft Docs"
description: "提供有关航拍图像分类的实际方案的说明"
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: 76c706496b3bcdbc1604661be85dc31000873ad3
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="aerial-image-classification"></a>航拍图像分类

本示例演示了如何使用 Azure Machine Learning Workbench 来协调图像分类模型的分布式定型和实施。 训练方式有两种：(i) 通过 [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) GPU 群集细化深层神经网络；(ii) 通过 [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) 软件包，使用预先训练的 CNTK 模型对图像进行特征化，并使用派生的功能训练分类器。 再使用 [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) 群集，以并行方式将已训练的模型应用于云中较大的图像集，如此，便可通过添加或删除辅助角色节点来调整训练和实施的速度。

本示例重点介绍传输学习的两种方式，利用预先训练的模型避免从头开始训练深层神经网络的成本。 重新训练深层神经网络通常需要 GPU 计算，但如果训练集足够大，则可大幅提高准确度。 在已特征化的图像上训练简单分类器无需 GPU 计算，它本质上既高效快速，又可任意缩放，但适用的参数较少。 因此，提供少量的培训示例时，这种方法是一个很好的选择，对自定义用例来说很常见。 

本示例中使用的 Spark 群集有 40 个辅助角色节点，操作成本为约 40 美元/小时；Batch AI 群集资源包括 8 GPU，操作成本为约 10 美元/小时。 完成此演练大约需要三个小时。 完成后，请按照清理说明删除你创建的资源，并不再产生费用。

## <a name="link-to-the-gallery-github-repository"></a>库 GitHub 存储库的链接

此实际方案的公共 GitHub 存储库包含此示例需要的所有材料，其中包括代码示例：

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>用例说明

在此方案中，我们定型机器学习模型来对显示在 224 米 × 224 米航空图中的土地类型进行分类。 使用定期收集的航空图像，可以使用土地利用分类模型来跟踪城市化、森林砍伐、湿地流失以及其他主要环境趋势。 我们已根据美国国家农业图像计划中的图像和美国国土覆盖数据库发布的土地利用标签准备了培训和验证图像集。 各个用地分类的示例图像如下所示：

![每个土地利用标签的示例区域](media/scenario-aerial-image-classification/example-labels.PNG)

定型和验证分类器模型后，我们将其应用于跨越马萨诸塞州米德尔塞克斯县（是微软新英格兰研究与开发 (NERD) 中心的本部）的航空图像，以展示这些模型如何用于研究城市发展趋势。

为了生成使用传输学习的图像分类器，数据科学家通常使用一系列方法构建多个模型，然后选择性能最佳的模型。 Azure Machine Learning Workbench 可以帮助数据科学家在不同的计算环境中协调定型，跟踪和比较多个模型的性能，并将选定的模型应用于云上的大型数据集。

## <a name="scenario-structure"></a>方案结构

在此示例中，图像数据和预培训的模型存储在 Azure 存储帐户中。 Azure HDInsight Spark 群集读取这些文件，并使用 MMLSpark 构建图像分类模型。 然后将定型的模型及其预测写入存储帐户，在那里可以通过本地运行的 Jupyter 笔记本进行分析和可视化。 Azure Machine Learning Workbench 在 Spark 群集上协调脚本的远程执行。 它还跟踪使用不同方法定型的多个模型的准确性指标，允许用户选择性能最佳的模型。

![航空图像分类实际方案的示意图](media/scenario-aerial-image-classification/scenario-schematic.PNG)

这些分步说明首先指导你完成 Azure 存储帐户和 Spark 群集的创建和准备工作，这包括数据传输和依赖项安装。 然后，它们介绍如何启动定型作业并比较生成模型的性能。 最后，它们说明了如何将所选模型应用于 Spark 群集上的大型图像集，并在本地分析预测结果。


## <a name="set-up-the-execution-environment"></a>设置执行环境

以下说明将指导你完成本示例的设置执行环境的过程。

### <a name="prerequisites"></a>先决条件
- [Azure 帐户](https://azure.microsoft.com/free/)（提供免费试用版）
    - 你将创建一个具有 40 个辅助角色节点（共 168 个内核）的 HDInsight Spark 群集。 通过查看 Azure 门户中订阅的“使用情况 + 配额”选项卡，确保帐户拥有足够的可用内核。
       - 如果可用的内核较少，你可以修改 HDInsight 群集模板以减少所配备的工作节点数量。 这个说明显示在“创建 HDInsight Spark 群集”部分下。
    - 此示例使用两个 NC6（1 GPU 和 6 vCPU）VM 创建一个 Batch AI 训练群集。 通过查看 Azure 门户中订阅的“使用情况 + 配额”选项卡，确保帐户在美国东部拥有足够的可用内核。
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - 请按照[安装和创建快速入门](quickstart-installation.md)中的说明安装 Azure Machine Learning Workbench 并创建试验和模型管理帐户。
- [Batch AI](https://github.com/Azure/BatchAI) Python SDK 和 Azure CLI 2.0
    - 完成 [Batch AI 工作程序自述文件](https://github.com/Azure/BatchAI/tree/master/recipes)中的以下部分：
        - “先决条件”
        - “创建并获取 Azure Active Directory (AAD) 应用程序”
        - “注册 BatchAI 资源提供程序”（在“使用 Azure CLI 2.0 运行工作程序”下）
        - “安装 Azure Batch AI 管理客户端”
        - “安装 Azure Python SDK”
    - 记录客户端 ID、机密以及按引导创建的 Azure Active Directory 应用程序的租户 ID。 本教程的后面部分会用到这些凭据。
    - 在本文中，Azure Machine Learning Workbench 和 Azure Batch AI 使用 Azure CLI 2.0 的单独分支。 为清楚起见，我们将该 Workbench 的 CLI 版本称为“从 Azure Machine Learning Workbench 启动的 CLI”，将常规发布版本（包括 Batch AI）称为“Azure CLI 2.0”。
- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)，一种用于协调 Azure 存储帐户之间的文件传输的免费实用工具
    - 确保包含 AzCopy 可执行文件的文件夹位于系统的 PATH 环境变量中。 （有关修改环境变量的说明，可在[此处](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp)获取。）
- 一个 SSH 客户端，建议使用 [PuTTY](http://www.putty.org/)。

在 Windows 10 电脑上测试了此示例；你应该可以从任何 Windows 计算机（包括 Azure 数据科学虚拟机）运行它。 Azure CLI 2.0 是按[以下说明](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials)从 MSI 安装的。 在 macOS 上运行此示例时可能需要进行细微的修改（例如更改文件路径）。

### <a name="set-up-azure-resources"></a>设置 Azure 资源

此示例需要 HDInsight Spark 群集和 Azure 存储帐户来托管相关文件。 按照这些说明在新的 Azure 资源组中创建这些资源：

#### <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“航拍图像分类”并选择模板
5.  单击“创建” 
 
#### <a name="create-the-resource-group"></a>创建资源组

1. 在 Azure Machine Learning Workbench 中加载项目后，单击“文件”->“打开命令提示符”打开命令行接口 (CLI)。
    本教程主要使用此版 CLI。 （需要时，系统将提示打开另一版本的 CLI，用于准备 Batch AI 资源。）

1. 通过运行以下命令从命令行接口登录到 Azure 帐户：

    ```
    az login
    ```

    需要访问某个 URL 并键入所提供的临时代码；网站会请求你的 Azure 帐户凭据。
    
1. 完成登录后，返回 CLI 并运行以下命令，确定可供 Azure 帐户使用的 Azure 订阅：

    ```
    az account list
    ```

    此命令列出与 Azure 帐户关联的所有订阅。 找到要使用的订阅的 ID。 在以下命令中的指示位置写入订阅 ID，然后执行该命令设置活动订阅：

    ```
    az account set --subscription [subscription ID]
    ```

1. 在此示例中创建的 Azure 资源统一存储在 Azure 资源组中。 选择唯一的资源组名称并在指示的位置写入该名称，然后执行这两条命令来创建 Azure 资源组：

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>创建存储帐户

现在，创建存储帐户，用于托管 HDInsight Spark 必须访问的项目文件。

1. 选择唯一的存储帐户名称，并在以下 `set` 命令中的指示位置写入该名称，然后执行这两条命令创建 Azure 存储帐户：

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. 通过运行以下命令列出存储帐户密钥：

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    记录以下命令中的 `key1` 值（存储密钥），然后运行该命令以存储该值。
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. 使用以下命令在存储帐户中创建名为 `baitshare` 的文件共享：

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. 在偏好的文本编辑器中，从 Azure Machine Learning Workbench 项目的“Code”子目录加载 `settings.cfg` 文件，并根据指示插入存储帐户名称和密钥。 保存并关闭 `settings.cfg` 文件。
1. 下载并安装 [AzCopy](http://aka.ms/downloadazcopy) 实用工具（如果尚未这样做）。 键入“AzCopy”并按 Enter 显示其文档，确保 AzCopy 可执行文件位于系统路径中。
1. 发出以下命令，将所有样本数据、预先训练的模型和模型训练脚本复制到存储帐户中的相应位置：

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    预计文件传输大概需要一个小时。 在等待时，可以继续执行下面的部分：可能需要通过 Workbench 打开另一个命令行接口，并在其中重新定义临时变量。

#### <a name="create-the-hdinsight-spark-cluster"></a>创建 HDInsight Spark 群集

创建 HDInsight 群集的建议方法是使用此项目的“Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning”子文件夹中包含的 HDInsight Spark 群集资源管理器模板。

1. HDInsight Spark 群集模板是此项目的“Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning”子文件夹下的“template.json”文件。 默认情况下，该模板会创建包含 40 个工作节点的 Spark 群集。 如果必须调整该数字，请在偏好的文本编辑器中打开该模板，并将出现的所有“40”替换为所选的工作节点数。
    - 如果所选的工作节点数较小，稍后可能会遇到内存不足错误。 若要避免内存错误，可根据本文档稍后所述，针对可用数据的子集运行训练和操作化脚本。
2. 为 HDInsight 群集选择唯一的名称和密码，并将其写入以下命令指示的位置。然后通过发出命令创建群集：

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

群集部署（包括预配和执行脚本操作）最长可能需要 30 分钟。

### <a name="set-up-batch-ai-resources"></a>设置 Batch AI 资源

在等待存储帐户文件传输和 Spark 群集部署完成时，可以准备 Batch AI 网络文件服务器 (NFS) 和 GPU 群集。 打开 Azure CLI 2.0 命令提示符并运行以下命令：

```
az --version 
```

确认 `batchai` 列在已安装的模块中。 如果没有，则表明你使用的可能是不同的命令行接口（例如通过 Workbench 打开的命令行接口）。

接着，检查提供程序注册是否已成功完成。 （提供程序注册最长耗时十五分钟，如果你最近完成了 [Batch AI 设置说明](https://github.com/Azure/BatchAI/tree/master/recipes)，则它可能仍在进行。）确认“Microsoft.Batch”和“Microsoft.BatchAI”在以下命令的输出中都显示状态“已注册”：

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

如果没有，请运行以下提供程序注册命令，并等待 15 分钟左右完成注册。
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

修改以下命令，使用之前在资源组和存储帐户创建过程中使用的值来替换括号内的表达式。 然后，通过执行以下命令将值存储为变量：
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

识别包含 Azure 机器学习项目的文件夹（例如 `C:\Users\<your username>\AzureML\aerialimageclassification`）。 用文件夹的文件路径替换括号内的值（不得出现尾随反斜杠）并执行命令：
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
现在就可以创建本教程所需的 Batch AI 资源了。

#### <a name="prepare-the-batch-ai-network-file-server"></a>准备 Batch AI 网络文件服务器

Batch AI 群集访问网络文件服务器上的训练数据。 从 NFS 与 Azure文件共享或 Azure Blob 存储访问文件时，数据访问速度可能会快几倍。

1. 发布以下命令创建网络文件服务器：

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. 使用以下命令检查网络文件服务器的预配状态：

    ```
    az batchai file-server list
    ```

    当名为“landuseclassifier”的网络文件服务器的“provisioningState”为“已成功”时，即可使用。 预配预计耗时五分钟左右。
1. 在前一个命令的输出中查找 NFS 的 IP 地址（“mountSettings”下的“fileServerPublicIp”属性）。 在下方命令所指示的位置写入 IP 地址，然后执行下列命令存储该值：

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. 使用喜爱的 SSH 工具（下面的示例命令使用 [PuTTY](http://www.putty.org/)）在 NFS 上执行此项目的 `prep_nfs.sh` 脚本，以便传输该处的训练图像集和验证图像集。

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    如果 shell 窗口中的数据下载和提取进程更新滚动太过迅速而无法看清，请不用担心。

如有必要，可以使用你所喜爱的 SSH 工具登录到文件服务器并检查 `/mnt/data` 目录内容，确认数据传输是否按计划进行。 应查找 training_images 和 validation_images 这两个文件夹，其中每个都包含按用地类别命名的子文件夹。  训练集和验证集应分别包含约 44,000 和 11,000 张图像。

#### <a name="create-a-batch-ai-cluster"></a>创建 Batch AI 群集

1. 发出以下命令来创建群集：

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p Dem0Pa$$w0rd --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. 使用以下命令检查群集的预配状态：

    ```
    az batchai cluster list
    ```

    当名为“landuseclassifier”的群集的分配状态由“重设大小”变为“就绪”时，则可以提交作业。 但是，直到群集中的所有 VM 不再是“准备”状态时，作业才开始运行。 如果群集的“错误”属性不为 NULL，则表示该群集在创建过程中出错，不应使用。

#### <a name="record-batch-ai-training-credentials"></a>记录 Batch AI 训练凭据

在等待群集分配完成的过程中，根据喜好使用任意一个文本编辑器，从此项目的“代码”子文件夹打开 `settings.cfg` 文件。 使用自己的凭据更新下列变量：
- `bait_subscription_id`（你的 36 字符 Azure 订阅 ID）
- `bait_aad_client_id`（“先决条件”部分中提到的 Azure Active Directory 应用程序/客户端 ID）
- `bait_aad_secret`（“先决条件”部分中提到的 Azure Active Directory 应用程序机密）
- `bait_aad_tenant`（“先决条件”部分中提到的 Azure Active Directory 租户 ID）
- `bait_region`（在本文中，唯一的选项是：eastus）
- `bait_resource_group_name`（先前所选择的资源组）

这些值一旦分配完毕，你的 settings.cfg 文件中已修改的行应类似于下方所示文本：

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

保存并关闭 `settings.cfg`。

现在，可以关闭用来执行 Batch AI 资源创建命令的 CLI 窗口了。 本教程中的所有后续步骤都使用从 Azure Machine Learning Workbench 启动的 CLI。

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>准备 Azure Machine Learning Workbench 执行环境

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>将 HDInsight 群集注册为 Azure Machine Learning Workbench 计算目标

完成 HDInsight 群集的创建后，请按如下所示，将该群集注册为项目的计算目标：

1.  从 Azure 机器学习命令行接口发出以下命令：

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    此命令会将两个文件（`myhdi.runconfig` 和 `myhdi.compute`）添加到项目的 `aml_config` 文件夹。

1. 在偏好的文本编辑器中打开 `myhdi.compute` 文件。 将 `yarnDeployMode: cluster` 行修改为 `yarnDeployMode: client`，然后保存并关闭该文件。
1. 运行以下命令来准备要使用的 HDInsight 环境：
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>安装本地依赖项

从 Azure Machine Learning Workbench 打开 CLI，并发出以下命令来安装本地执行所需的依赖项：

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>数据获取和理解

本方案使用[全国农业图像计划](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/)中分辨率为 1 米的公用航拍图像数据。 我们已基于原始的 NAIP 数据生成了 224 x 224 像素的裁剪 PNG 文件集，并根据[全国土地覆盖数据库](https://www.mrlc.gov/nlcd2011.php)中的土地用途标签将这些文件排序。 带有“已开发”标签的示例图像以完整大小显示：

![已开发土地的示例平铺图](media/scenario-aerial-image-classification/sample-tile-developed.png)

~44K 和 11K 图像的类均衡集分别用于模型训练和验证。 我们将会根据覆盖马萨诸塞州米德塞斯郡（Microsoft 的新英格兰研发中心 (NERD) 基地）的 ~67K 图像集演示模型部署。 有关这些图像集的构造方式的详细信息，请参阅[易并行图像分类 git 存储库](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)。

![马萨诸塞州米德塞斯郡的位置](media/scenario-aerial-image-classification/middlesex-ma.png)

在设置期间，本示例中使用的航拍图像集已传输到所创建的存储帐户。 训练、验证和操作化图像都是 224 x 224 像素的 PNG 文件，分辨率为每平方米 1 个像素。 训练和验证图像已根据其土地用途标签组织到子文件夹中。 （操作化图像的土地用途标签是未知的，在许多情况下不明确；其中一些图像包含多种土地类型。）有关这些图像集的构造方式的详细信息，请参阅[易并行图像分类 git 存储库](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)。

在 Azure 存储帐户中查看示例映像（可选）：
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在屏幕顶部的搜索栏中搜索自己的存储帐户的名称。 在搜索结果中单击自己的存储帐户。
2. 在存储帐户的主窗格中单击“Blob”链接。
3. 单击名为“train”的容器。 应会看到根据土地用途命名的目录的列表。
4. 单击其中的任一目录可加载其中包含的图像列表。
5. 单击任一图像可将其下载，以查看该图像。
6. 如果需要，还可以单击名为“test”和“middlesexma2016”的容器来查看其内容。

## <a name="modeling"></a>建模

### <a name="training-models-with-azure-batch-ai"></a>使用 Azure Batch AI 使模型训练

使用 Workbench 项目“Code\02_Modeling”子文件夹中的 `run_batch_ai.py` 脚本来发布 Batch AI 训练作业。 此作业重训练用户选择的图像分类器 DNN（在 ImageNet 上预训练的 AlexNet 或 ResNet 18）。 还可以指定重训练的深度：当可用的训练示例不多时，仅对网络的最终层进行重训练可以减少过度拟合，而当训练集足够大时，微调整个网络（对于 AlexNet 而言，为完全连接的层）可大幅提升模型性能。

完成训练作业后，此脚本将模型（以及描述模型的整数输出和字符串标签之间映射的文件）和预测保存到 blob 存储。 对 BAIT 作业的日志文件进行解析，以提取训练时期错误率改善的时间进程。 AML Workbench 的运行历史记录功能记录了错误率改善的时间进程，供以后查看。

选择已训练模型的名称、预训练模型类型和重训练深度。 将所选内容写入到以下命令中的指示位置，从 Azure 机器学习命令行接口执行该命令，开始重训练：

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

预计需耗时半小时才能完成 Azure 机器学习的运行。 我们建议，运行几个类似的命令（因输出模型名称、预训练模型类型和预训练深度而异），以便比较通过不同方法训练的模型。

### <a name="training-models-with-mmlspark"></a>使用 MMLSpark 训练模型

使用 Workbench 项目的“Code\02_Modeling”子文件夹中的 `run_mmlspark.py` 脚本可以训练用于图像分类的 [MMLSpark](https://github.com/Azure/mmlspark) 模型。 该脚本首先使用基于 ImageNet 数据集（AlexNet 或 18 层 ResNet）预先训练的图像分类器 DNN 将训练集图像特征化。 然后，该脚本使用特征化的图像来训练 MMLSpark 模型（随机林或逻辑回归模型），以将图像分类。 接着，使用已训练的模型将测试图像集特征化并评分。 将会计算该模型对测试集的预测准确性，并将准确性值记录到 Azure Machine Learning Workbench 的运行历史记录功能中。 最后，将已训练的 MMLSpark 模型及其对测试集的预测结果保存到 Blob 存储中。

选择已训练模型的唯一输出模型名称、预先训练的模型类型以及 MMLSpark 模型类型。 将所选内容写入到以下命令模板中的指示位置，然后从 Azure 机器学习命令行接口执行该命令，以开始重新训练：

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

可以使用一个附加参数 `--sample_frac`，通过可用数据的子集来训练和测试模型。 使用较小的样本片断可以降低运行时和内存方面的要求，代价是训练模型的准确性也会降低。 （例如，使用 `--sample_frac 0.1` 运行预计耗时约二十分钟。）有关此参数和其他参数的详细信息，请运行 `python Code\02_Modeling\run_mmlspark.py -h`。

建议用户结合不同的输入参数多次运行此脚本。 然后，可在 Azure Machine Learning Workbench 的运行历史记录功能中比较生成的模型的性能。

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>使用 Workbench 运行历史记录功能比较模型性能

执行每种类型的两个或更多个训练运行后，请单击左侧菜单栏中的时钟图标，导航到 Workbench 中的“运行历史记录”功能。 从左侧的脚本列表中选择 `run_mmlspark.py`。 随后会加载一个窗格，其中对所有运行的测试集准确性做了比较。 若要查看更多详细信息，请向下滚动，并单击单个运行的名称。

## <a name="deployment"></a>部署

若要使用 HDInsight 上的远程执行功能将某个已训练的模型应用到覆盖马萨诸塞州米德塞斯郡的航拍图像，请在以下命令中插入所需模型的名称，并执行该命令：

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

可以使用一个附加参数 `--sample_frac`，通过可用数据的子集来操作化模型。 使用较小的样本片断可以降低运行时和内存方面的要求，代价是预测完整性也会降低。 有关此参数和其他参数的详细信息，请运行 `python Code\03_Deployment\batch_score_spark -h`。

此脚本将模型的预测结果写入存储帐户。 可根据下一部分所述检查预测结果。

## <a name="visualization"></a>可视化

Workbench 项目的“Code\04_Result_Analysis”子文件夹中的“模型预测分析”Jupyter Notebook 可将模型预测结果可视化。 按如下所述加载并运行该 Notebook：
1. 在 Workbench 中打开项目，单击左侧菜单中的文件夹（“文件”）图标加载目录列表。
2. 导航到“Code\04_Result_Analysis”子文件夹，单击名为“模型预测分析”的 Notebook。 此时应会显示该 Notebook 的预览。
3. 单击“启动 Notebook 服务器”加载该 Notebook。
4. 在第一个单元中，在指示的位置输入想要分析其结果的模型的名称。
5. 单击“单元”->“全部运行”，执行该 Notebook 中的所有单元。
6. 阅读该 Notebook 的整个执行结果，详细了解它所呈现的分析和视觉对象。

## <a name="cleanup"></a>清理
完成示例后，我们建议在 Azure 命令行接口中执行以下命令来删除创建的所有资源：

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>参考

- [易并行图像分类存储库](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - 介绍如何通过免费的图像和标签构造数据集
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub 存储库
   - 包含有关如何使用 MMLSpark 训练和评估模型的其他示例

## <a name="conclusions"></a>结论

Azure Machine Learning Workbench 可帮助数据科学家在远程计算目标上轻松部署代码。 此示例中，在 HDInsight 群集上为远程执行部署了本地 MMLSpark 训练代码，且一个本地脚本在 Azure Batch AI GPU 群集上启动了训练作业。 Azure Machine Learning Workbench 的运行历史记录功能跟踪了多个模型的性能，并帮助我们识别最准确的模型。 Workbench 的 Jupyter Notebook 功能已帮助我们在交互式图形环境中将模型的预测结果可视化。

## <a name="next-steps"></a>后续步骤
更深入地学习本示例：
- 在 Azure Machine Learning Workbench 的“运行历史记录”功能中，单击齿轮符号选择要显示的图形和指标。
- 在示例脚本中查看调用 `run_logger` 的语句。 检查是否了解每个指标的记录方式。
- 在示例脚本中查看调用 `blob_service` 的语句。 检查是否了解已训练模型和预测结果的存储方式以及从云中检索这些信息的方式。
- 浏览在 Blob 存储帐户中创建的容器的内容。 确保了解哪个脚本或命令负责创建每个文件组。
- 修改训练脚本，以训练不同的 MMLSpark 模型类型或更改模型超参数。 使用运行历史记录功能确定所做的更改是提高还是降低了模型的准确性。
