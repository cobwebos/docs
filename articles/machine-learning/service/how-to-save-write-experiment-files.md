---
title: 在何处保存 & 写入试验文件
titleSuffix: Azure Machine Learning
description: 了解保存实验输入文件的位置，以及写入输出文件的位置，以防止出现存储限制错误和试验延迟。
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 77f816400709262fab8cb9bd351bdcde73377e09
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034294"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>在何处为 Azure 机器学习试验保存和写入文件

本文介绍保存输入文件的位置，以及从试验写入输出文件的位置，以防止出现存储限制错误和试验延迟。

在[计算目标](how-to-set-up-training-targets.md)上启动训练时，它们会与外部环境隔离。 此设计的目的是确保实验的可再现性和可移植性。 如果在相同或其他计算目标上运行相同的脚本两次，则会收到相同的结果。 利用此设计，你可以将计算目标视为无状态计算资源，每个资源都与在完成后运行的作业没有关联。

## <a name="where-to-save-input-files"></a>在何处保存输入文件

在对计算目标或本地计算机启动试验之前，必须确保所需的文件对该计算目标可用，如需要运行的代码的依赖项文件和数据文件。

Azure 机器学习通过将整个脚本文件夹复制到目标计算上下文来运行训练脚本，然后拍摄快照。 试验快照的存储限制为 300 MB 和/或2000个文件。

出于此原因，我们建议：

* **将文件存储在 Azure 机器学习[数据](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)存储中。** 这会阻止试验延迟问题，并具有从远程计算目标访问数据的优点，这意味着身份验证和装载由 Azure 机器学习管理。 详细了解如何将数据存储指定为源目录，并将文件上传到数据存储中的数据存储文章中的[访问数据](how-to-access-data.md)。

* **如果只需要几个数据文件和依赖项脚本并且不能使用数据存储，请**将这些文件放在与训练脚本相同的文件夹目录中。 将此文件夹指定为`source_directory`您直接在训练脚本中或在调用训练脚本的代码中。

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>试验快照的存储限制

对于试验，Azure 机器学习会根据配置运行时所建议的目录自动进行代码的实验。 此限制的总大小限制为 300 MB 和/或2000个文件。 如果超出此限制，你将看到以下错误：

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

若要解决此错误，请将实验文件存储在数据存储上。 如果无法使用数据存储，下表提供了可能的替代解决方案。

试验&nbsp;说明|存储限制解决方案
---|---
小于2000个文件 & 无法使用数据存储| 替代快照大小限制 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 这可能需要几分钟时间，具体取决于文件的数量和大小。
必须使用特定的脚本目录| 创建一个`.amlignore`文件以从实验快照中排除不属于源代码的文件。 将文件名添加到`.amlignore`该文件中，并将其放置在训练脚本所在的同一目录中。 文件使用与`.gitignore`文件相同的[语法和模式。](https://git-scm.com/docs/gitignore) `.amlignore`
管道|为每个步骤使用不同的子目录
Jupyter 笔记本| 创建一个`.amlignore`文件或将笔记本移动到一个新的空子目录，然后再次运行代码。

## <a name="where-to-write-files"></a>写入文件的位置

由于定型试验的隔离，在运行期间发生的文件更改不一定在您的环境中保留。 如果你的脚本修改本地的文件以进行计算，则不会为下一次试验运行保留更改，并且不会自动将其传播回客户端计算机。 因此，在第一次试验期间所做的更改不会在第二次运行时产生影响。

写入更改时，建议将文件写入 Azure 机器学习数据存储。 请参阅[访问数据存储中的数据](how-to-access-data.md)。

如果不需要数据存储，请将文件`./outputs`写入和/或`./logs`文件夹。

>[!Important]
> 两个文件夹（*输出*和*日志*）接收 Azure 机器学习的特殊处理。 在训练过程中，当您将`./outputs`文件`./logs`写入和文件夹时，这些文件将自动上载到您的运行历史记录，以便您在运行完成后可以访问这些文件。

* **对于诸如状态消息或评分结果这样的输出，请**将文件`./outputs`写入文件夹，因此它们在运行历史记录中作为项目保存。 请注意写入到此文件夹中的文件的数量和大小，因为在将内容上载到运行历史记录时可能会发生延迟。 如果需要考虑延迟，则建议将文件写入数据存储。

* **若要将写入的文件以日志形式保存在运行历史记录中，请**将文件`./logs`写入文件夹。 日志是实时上载的，因此，此方法适用于从远程运行流式传输实时更新。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[访问数据存储中的数据](how-to-access-data.md)。

* 了解有关[如何设置定型目标](how-to-set-up-training-targets.md)的详细信息。
