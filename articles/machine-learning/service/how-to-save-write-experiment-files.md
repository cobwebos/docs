---
title: 防止存储限制和试验延迟，输入和输出目录
description: 在本文中，你了解你实验的输入的文件的保存位置以及在何处写入输出文件，若要防止存储限制错误并尝试延迟。
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 1f9199b5bae0c82cd46750d8ef5522a0d3579671
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595285"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>若要保存和写入文件的位置的 Azure 机器学习试验

在本文中，了解输入的文件的保存位置以及在何处写入输出文件将试验，以防止存储限制错误并尝试延迟。

启动培训的运行时[计算目标](how-to-set-up-training-targets.md)，它们是隔离的环境外。 此设计的目的是确保可再现性和可移植性的试验。 如果两次运行同一个脚本，在相同或另一个计算目标，你收到相同的结果。 与此设计中，您可以计算目标视为无状态计算资源，每个都有完成后运行的作业无关联。

## <a name="where-to-save-input-files"></a>输入的文件的保存位置

你可以启动试验的计算目标或本地计算机上之前，必须确保所需的文件可供该计算目标，如代码需要运行的依赖项文件和数据文件。

Azure 机器学习通过将整个脚本文件夹复制到目标计算上下文中运行训练脚本，然后拍摄快照。 实验快照存储限制为 300 MB 和/或 2000年文件。

出于此原因，我们建议：

* **将文件存储在 Azure 机器学习[数据存储](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)。** 这可以防止实验延迟问题，并具有以下优点： 从远程计算目标，这意味着由 Azure 机器学习服务管理身份验证和装载访问的数据。 了解有关指定一种数据存储为源目录，并将文件上载到您数据中存储的详细信息[访问你的数据存储中的数据](how-to-access-data.md)一文。

* **如果您只需要几个数据文件和依赖项编写的脚本，并且不能使用一种数据存储，** 将文件放在训练脚本所在的文件夹目录中。 指定作为此文件夹在`source_directory`直接在训练脚本，或调用训练脚本的代码中。

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>存储限制的实验快照

进行试验，Azure 机器学习自动使你的代码基于建议配置运行时的目录的试验快照。 这有 300 MB 和/或 2000年文件的总限制。 如果超过此限制，会看到以下错误：

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

若要解决此错误，存储上一种数据存储的试验文件。 如果您不能使用一种数据存储，下表提供了可能的备选解决方案。

实验&nbsp;说明|存储限制解决方案
---|---
小于 2000年文件和不能使用一种数据存储| 重写与快照大小限制 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 这可能需要几分钟时间，具体取决于的数量和大小的文件。
必须使用特定的脚本目录| 使`.amlignore`文件，以从实验快照不属于的源代码文件。 添加到文件名`.amlignore`文件，并将其放在训练脚本所在的同一目录中。 `.amlignore`文件使用相同[语法和模式](https://git-scm.com/docs/gitignore)作为`.gitignore`文件。
管道|为每个步骤中使用的不同子目录
Jupyter 笔记本| 创建`.amlignore`文件或将你的 notebook 移到新的、 为空，子目录，然后再次运行你的代码。

## <a name="where-to-write-files"></a>写入文件的位置

训练实验的隔离，因为对文件运行过程中发生的更改之外不会一定保留您的环境。 如果脚本中修改本地计算的文件，所做的更改不会保留的下一步实验运行，并且它们不会传播回客户端计算机自动。 因此，所做的更改在第一个试验过程运行不，不应影响那些在第二个。

在编写时更改，我们建议将文件写入到 Azure 机器学习数据存储。 请参阅[访问你的数据存储中的数据](how-to-access-data.md)。

如果不需要一种数据存储，将文件写入`./outputs`和/或`./logs`文件夹。

>[!Important]
> 两个文件夹*输出*并*日志*，接收由 Azure 机器学习的特殊处理。 在训练时将文件写入期间`./outputs`和`./logs`文件夹，文件将自动上传到运行历史记录，以便完成运行后，您可以访问它们。

* **对于输出，例如状态消息或评分结果**向其中写入文件`./outputs`文件夹，因此它们将保留为运行历史记录中的项目。 留意的数量和大小的文件写入到此文件夹中上, 传内容运行历史记录时可能发生延迟。 如果延迟是一个问题，建议将文件写入到数据存储。

* **将写入的文件另存为运行历史记录中的日志**向其中写入文件`./logs`文件夹。 日志上载实时，因此此方法适用于流式处理从远程运行的实时更新。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[访问你的数据存储中的数据](how-to-access-data.md)。

* 详细了解如何[如何设置培训目标](how-to-set-up-training-targets.md)。
