---
title: 教程：使用自己的数据
titleSuffix: Azure Machine Learning
description: Azure ML 入门系列第 4 部分介绍了如何在远程训练运行中使用自己的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: 876ba76655572979a1d831a1ca07e5f3871a3283
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929252"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>教程：使用自己的数据（第 4 部分，共 4 部分）

本教程介绍了如何在 Azure 机器学习中上传并使用你自己的数据来训练机器学习模型。

本教程是**由四个部分组成的教程系列的第四部分**。你可以在其中了解 Azure 机器学习基础知识，并在 Azure 中完成基于作业的机器学习任务。 本教程以你在[第 1 部分：设置](tutorial-1st-experiment-sdk-setup-local.md)、[第 2 部分：运行“Hello World”](tutorial-1st-experiment-hello-world.md)和[第 3 部分：训练模型](tutorial-1st-experiment-sdk-train.md)中完成的工作为基础编写。

在[第 3 部分：训练模型](tutorial-1st-experiment-sdk-train.md)中，已使用 PyTorch API 中的内置 `torchvision.datasets.CIFAR10` 方法下载了数据。 但在许多情况下，你需要在远程训练运行中使用自己的数据。 本文介绍了可用于在 Azure 机器学习中处理你自己的数据的工作流。

本教程介绍以下操作：

> [!div class="checklist"]
> * 将训练脚本配置为使用本地目录中的数据
> * 在本地测试训练脚本
> * 将数据上传到 Azure
> * 创建控制脚本
> * 了解新的 Azure 机器学习概念（传递参数、数据集、数据存储）
> * 提交并运行训练脚本
> * 在云中查看代码输出

## <a name="prerequisites"></a>先决条件

* 完成本系列的[第 3 部分](tutorial-1st-experiment-sdk-train.md)。
* Python 语言和机器学习工作流的入门知识。
* 本地开发环境。 这包括但不限于 Visual Studio Code、Jupyter 或 PyCharm。
* Python（版本 3.5-3.7）。

## <a name="adjust-the-training-script"></a>调整训练脚本
现在，你已在 Azure 机器学习中运行训练脚本 (tutorial/src/train.py)，并且可以监视模型性能。 让我们通过引入参数来参数化训练脚本。 使用参数可轻松比较不同的超参数。

目前，我们的训练脚本设置为在每次运行时下载 CIFAR10 数据集。 以下 Python 代码已调整为读取某个目录中的数据。

>[!NOTE] 
> 使用 `argparse` 来参数化脚本。

```python
# tutorial/src/train.py
import os
import argparse
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run

run = Run.get_context()

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument('--data_path', type=str, help='Path to the training data')
    parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
    parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
    args = parser.parse_args()
    
    print("===== DATA =====")
    print("DATA PATH: " + args.data_path)
    print("LIST FILES IN DATA PATH...")
    print(os.listdir(args.data_path))
    print("================")
    
    # prepare DataLoader for CIFAR10 data
    transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    trainset = torchvision.datasets.CIFAR10(
        root=args.data_path,
        train=True,
        download=False,
        transform=transform,
    )
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(
        net.parameters(),
        lr=args.learning_rate,
        momentum=args.momentum,
    )

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # log loss metric to AML
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

### <a name="understanding-the-code-changes"></a>了解代码更改

`train.py` 中使用的代码利用了 `argparse` 库来设置 `data_path`、`learning_rate` 和 `momentum`。

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

此外，`train.py` 脚本已改编，更新了优化器以使用用户定义的参数：

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

## <a name="test-the-script-locally"></a>在本地测试脚本

你的脚本现在接受数据路径作为参数。 若要开始，请在本地对其进行测试。 向教程目录结构添加一个名为 `data` 的文件夹。 目录结构应类似于：

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

如果在上一教程中未在本地运行 `train.py`，则不会有 `data/` 目录。 在此示例中，请在 `train.py` 脚本中在 `download=True` 的情况下在本地运行 `torchvision.datasets.CIFAR10` 方法。

若要在本地运行修改后的训练脚本，请调用：

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

可以通过传入数据的本地路径来避免下载 CIFAR10 数据集。 此外，还可以为“学习速率”和“动量”超参数使用不同的值进行试验，无需在训练脚本中对其进行硬编码。

## <a name="upload-the-data-to-azure"></a>将数据上传到 Azure

若要在 Azure 机器学习中运行此脚本，需要使训练数据在 Azure 中可用。 Azure 机器学习工作区附带了一个默认的数据存储（一个 Azure Blob 存储帐户），它可以用来存储训练数据。

>[!NOTE] 
> Azure 机器学习允许连接其他基于云的数据存储，用来存储你的数据。 如需更多详细信息，请参阅[数据存储文档](./concept-data.md)。  

在 `tutorial` 目录中，创建一个名为 `05-upload-data.py` 的新 Python 控制脚本：

```python
# tutorial/05-upload-data.py
from azureml.core import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_datastore()
datastore.upload(src_dir='./data', target_path='datasets/cifar10', overwrite=True)
```

`target_path` 指定将在其中上传 CIFAR10 数据的数据存储的路径。

>[!TIP] 
> 使用 Azure 机器学习上传数据时，可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)来上传临时文件。 如果需要 ETL 工具，可使用 [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)将数据引入 Azure。

运行 Python 文件以上传数据（注意：上传速度应快，时间应少于 60 秒。）

```bash
python 05-upload-data.py
```
应该会看到以下标准输出：
```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```


## <a name="create-a-control-script"></a>创建控制脚本

如之前所做的那样，创建一个名为 `06-run-pytorch-data.py` 的新 Python 控制脚本：

```python
# tutorial/06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
        )
    
    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env',file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to an Azure Machine Learning compute cluster. Click on the link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>了解代码更改

此控制脚本类似于[此系列第 3 部分](tutorial-1st-experiment-sdk-train.md)的控制脚本，其中包含以下新行：

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true)用于引用上传到 Azure Blob 存储的数据。 数据集是基于数据的抽象层，旨在提高可靠性和可信任性。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) 已修改，包含将传递到 `train.py` 中的参数列表。 `dataset.as_named_input('input').as_mount()` 参数表示指定的目录将装载到计算目标。
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>将运行提交到 Azure 机器学习

现在，请重新提交该运行以使用新配置：

```bash
python 06-run-pytorch-data.py
```

这将在 Azure 机器学习工作室中输出试验的 URL。 导航到该链接就可以看到代码在运行。

### <a name="inspect-the-70_driver_log-log-file"></a>检查 70_driver_log 日志文件

在 Azure 机器学习工作室中导航到试验运行（单击上面单元格中的 URL 输出），然后单击“输出 + 日志”。 单击 70_driver_log.txt 文件，应该会看到以下输出：

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

注意：

1. Azure 机器学习已自动为你将 blob 存储装载到计算群集。
2. 控制脚本中使用的 ``dataset.as_named_input('input').as_mount()`` 将解析为装入点

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 `Datastore` 将数据上传到 Azure。 数据存储充当工作区的云存储，为你提供了一个持久且灵活的数据保存位置。

你已了解如何修改训练脚本，以通过命令行接受数据路径。 可以通过使用 `Dataset` 将目录安装到远程运行。 

有了模型后，接下来请学习：

* 如何[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)
