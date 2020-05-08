---
title: 保存和写入试验文件的位置
titleSuffix: Azure Machine Learning
description: 了解保存试验输入文件以及写入输出文件的位置，以防止出现存储空间上限错误和试验延迟。
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 0938888b7343b441725faace7a5f20d8f50674c8
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872060"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>保存和写入 Azure 机器学习试验文件的位置
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍在试验中保存输入文件以及写入输出文件的位置，以防止出现存储空间上限错误和试验延迟。

在[计算目标](how-to-set-up-training-targets.md)上启动训练运行时，它们会与外部环境隔离。 此设计的目的是确保试验的可再现性和可移植性。 如果在相同或不同的计算目标上运行同一脚本两次，可获得相同的结果。 在此设计中，可将计算目标视为无状态计算资源，其中每个资源与完成后运行的作业无关联。

## <a name="where-to-save-input-files"></a>保存输入文件的位置

在对计算目标或本地计算机启动试验之前，必须确保所需文件对该计算目标可用，例如需要运行的代码的依赖项文件和数据文件。

Azure 机器学习通过将整个脚本文件夹复制到目标计算上下文来运行训练脚本，然后拍摄快照。 试验快照的存储空间上限为 300 MB 和/或 2000 个文件。

因此，我们建议：

* 将文件存储在 Azure 机器学习[数据存储](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)中  。 这样能防止出现试验延迟问题，并具有从远程计算目标访问数据的优点，这意味着身份验证和装载工作都由 Azure 机器学习管理。 请参阅[访问数据存储中的数据](how-to-access-data.md)一文，详细了解如何将数据存储指定为源目录，以及如何将文件上传到数据存储。

* 如果只需要几个数据文件和依赖项脚本且无法使用数据存储，请将文件放在与训练脚本相同的文件夹目录中  。 在训练脚本或者调用训练脚本的代码中直接将此文件夹指定为 `source_directory`。

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>试验快照的存储空间上限

Azure 机器学习会根据配置运行时所选择的目录自动为试验制作代码的试验快照。 此试验快照的总空间上限为 300 MB 和/或 2000 个文件。 如果超出限制，将看到以下错误：

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

要解决此错误，请将试验文件存储在数据存储中。 如果无法使用数据存储，下表提供了一些可以考虑的替代解决方案。

试验说明|存储空间上限解决方案
---|---
上限低于 2000 个文件且无法使用数据存储| 使用该方法重写快照大小限制 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 这可能需要数分钟的时间，具体取决于文件的数量和大小。
必须使用特定的脚本目录| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
管道|在每个步骤中使用不同的子目录
Jupyter 笔记本| 创建 `.amlignore` 文件或将笔记本移动到新的空子目录，然后再次运行代码。

## <a name="where-to-write-files"></a>写入文件的位置

由于训练实验具有隔离性，所以在运行期间发生的文件更改并不一定在你的环境之外保留。 如果脚本在本地修改了文件以进行计算，那么，既不会为下一次试验运行持久保存更改，也不会自动将更改传播回客户端计算机。 因此在第一次试验运行期间所做的更改不应影响第二次试验运行中的更改。

写入更改时，建议将文件写入 Azure 机器学习数据存储。 请参阅[访问数据存储中的数据](how-to-access-data.md)。

如果不需要使用数据存储，请将文件写入 `./outputs` 和/或 `./logs` 文件夹。

>[!Important]
> “outputs”和“logs”两个文件夹接收 Azure 机器学习的特殊处理****。 在训练期间，如果将文件写入 `./outputs` 和 `./logs` 文件夹，则会将这些文件自动上传到运行历史记录，以便在完成运行后对其具有访问权限。

* 对于诸如状态消息或评分结果这样的输出，请将文件写入 `./outputs` 文件夹，以便将它们作为项目持久保存在运行历史记录中****。 请注意写入到此文件夹中的文件数量和文件大小，因为在将内容上传到运行历史记录时可能会出现延迟。 如果需要考虑延迟，则建议将文件写入数据存储。

* 若要将写入的文件以日志形式保存在运行历史记录中，请将文件写入 `./logs` 文件夹****。 日志是实时上传的，所以此方法适用于从远程运行流式传输实时更新。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[访问数据存储中的数据](how-to-access-data.md)。

* 详细了解[如何设置训练目标](how-to-set-up-training-targets.md)。
