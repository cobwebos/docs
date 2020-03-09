---
title: 通过数据工厂运行 Python 脚本 - Azure Batch Python
description: 教程 - 了解如何使用 Azure Batch 通过 Azure 数据工厂将 Python 脚本作为管道的一部分运行。
services: batch
author: mammask
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 2995c5da4491f14471d9ed03022a144a02beab5a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201634"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>教程：使用 Azure Batch 通过 Azure 数据工厂运行 Python 脚本

本教程介绍以下操作：

> [!div class="checklist"]
> * 通过 Batch 和存储帐户进行身份验证
> * 在 Python 中开发和运行脚本
> * 创建运行应用程序所需的计算节点池
> * 计划 Python 工作负荷
> * 监视分析管道
> * 访问日志文件

以下示例运行一个 Python 脚本，该脚本从 Blob 存储容器接收 CSV 输入，执行数据处理过程，并将输出写入到单独的 Blob 存储容器。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

* 已安装一个 [Python](https://www.python.org/downloads/) 分发版用于本地测试。
* [Azure](https://pypi.org/project/azure/) `pip` 包。
* Azure Batch 帐户和关联的 Azure 存储帐户。 有关如何创建 Batch 帐户并将其链接到存储帐户的详细信息，请参阅[创建 Batch 帐户](quick-create-portal.md#create-a-batch-account)。
* 一个 Azure 数据工厂帐户。 有关如何通过 Azure 门户创建数据工厂的详细信息，请参阅[创建数据工厂](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)。
* [Batch Explorer](https://azure.github.io/BatchExplorer/)。
* [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>使用 Batch Explorer 创建 Batch 池

在本部分，你将通过 Batch Explorer 创建 Azure 数据工厂管道要使用的 Batch 池。 

1. 使用 Azure 凭据登录到 Batch Explorer。
1. 选择你的 Batch 帐户
1. 选择左侧栏上的“池”来创建池，然后选择搜索窗体上方的“添加”按钮   。 
    1. 选择 ID 和显示名称。 本示例使用 `custom-activity-pool`。
    1. 将规模类型为“固定大小”，将专用节点计数设置为 2。 
    1. 在“数据科学”下，选择“Dsvm Windows”作为操作系统。  
    1. 选择 `Standard_f2s_v2` 作为虚拟机大小。
    1. 启用启动任务，并添加命令 `cmd /c "pip install pandas"`。 用户标识可以保留为默认的“池用户”。 
    1. 选择“确定”  。

## <a name="create-blob-containers"></a>创建 Blob 容器

此处将创建 Blob 容器，用于存储 OCR 批处理作业的输入和输出文件。

1. 使用 Azure 凭据登录到存储资源管理器。
1. 使用链接到批处理帐户的存储帐户，按照[创建 Blob 容器](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)的步骤创建两个 Blob 容器（一个用于输入文件，一个用于输出文件）。
    * 在本例中，我们将调用输入容器 `input` 和输出容器 `output`。
1. 遵循[管理 Blob 容器中的 Blob](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) 中的步骤，使用存储资源管理器将 `main.py` 和 `iris.csv` 上传到输入容器 `input`


## <a name="develop-a-script-in-python"></a>在 Python 中开发脚本

以下 Python 脚本从 `input` 容器加载 `iris.csv` 数据集，执行数据处理过程，并将结果保存回到 `output` 容器。

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_text(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

将脚本另存为 `main.py`，然后将其上传到“Azure 存储”容器。  在将其上传到 Blob 容器之前，请务必在本地测试并验证其功能：

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>设置 Azure 数据工厂管道

在本部分，你将使用 Python 脚本创建并验证一个管道。

1. 遵循[此文](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)的“创建数据工厂”部分中的步骤创建一个数据工厂。
1. 在“工厂资源”框中选择“+”（加号）按钮，然后选择“管道”  
1. 在“常规”选项卡中，将管道名称设置为“运行 Python” 

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. 在“活动”框中展开“Batch 服务”。   将“活动”工具箱中的自定义活动拖到管道设计器图面。 
1. 在“常规”选项卡中，指定 **testPipeline** 作为名称。 

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. 在“Azure Batch”选项卡中，添加在前面步骤中创建的 **Batch 帐户**，然后选择“测试连接”以确保连接成功  

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. 在“设置”选项卡中，输入命令 `python main.py`。 
1. 对于“资源链接服务”，请添加在前面步骤中创建的存储帐户。  测试连接以确保连接成功。
1. 在“文件夹路径”中，选择包含 Python 脚本和关联输入的“Azure Blob 存储”容器的名称。   这会在执行 Python 脚本之前，将所选文件从该容器下载到池节点实例。

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. 在画布上面的管道工具栏中单击“验证”，以便验证管道设置。  确认已成功验证管道。 若要关闭验证输出，请选择 &gt;&gt;（右箭头）按钮。
1. 单击“调试”以测试管道，确保管道正常运行。 
1. 单击“发布”以发布管道。 
1. 单击“触发”，以便在批处理过程中运行 Python 脚本。 

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>监视日志文件

如果执行脚本时生成了警告或错误，可以查看 `stdout.txt` 或 `stderr.txt` 获取有关记录的输出的详细信息。

1. 在 Batch Explorer 的左侧选择“作业”。 
1. 选择数据工厂创建的作业。 假设池命名为 `custom-activity-pool`，请选择 `adfv2-custom-activity-pool`。
1. 单击具有失败退出代码的任务。
1. 查看 `stdout.txt` 和 `stderr.txt` 以调查和诊断问题。

## <a name="next-steps"></a>后续步骤

本教程探讨的示例演示了如何使用 Azure Batch 通过 Azure 数据工厂将 Python 脚本作为管道的一部分运行。

若要详细了解 Azure 数据工厂，请参阅：

> [!div class="nextstepaction"]
> [Azure 数据工厂](../data-factory/introduction.md)
> [管道和活动](../data-factory/concepts-pipelines-activities.md)
> [自定义活动](../data-factory/transform-data-using-dotnet-custom-activity.md)
