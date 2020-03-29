---
title: 使用标签创建和浏览数据集
titleSuffix: Azure Machine Learning
description: 了解如何从 Azure 机器学习标记项目导出数据标签，并将其用于机器学习任务。
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549484"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>使用标签创建和浏览 Azure 机器学习数据集

在本文中，您将学习如何从 Azure 机器学习数据标记项目导出数据标签并将其加载到流行的格式，例如用于数据探索的熊猫数据框或用于图像转换的 Torchvision 数据集。 

## <a name="what-are-datasets-with-labels"></a>什么是带标签的数据集 

具有标签的 Azure 机器学习数据集是具有标签属性[的表格数据集](how-to-create-register-datasets.md#dataset-types)，我们将它们称为标记数据集。 这些特定类型的表格数据集仅作为 Azure 机器学习数据标记项目的输出创建。 使用[这些步骤](how-to-create-labeling-projects.md)创建数据标记项目。 机器学习支持图像分类（多标签或多类）的数据标记项目，以及对象标识以及边界框。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请先创建[一个免费帐户](https://aka.ms/AMLFree)。"
* [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。
    * 安装[azure-contrib 数据集](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)包
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
* 访问 Azure 机器学习数据标记项目。 如果没有标记项目，请使用[这些步骤](how-to-create-labeling-projects.md)创建一个 。

## <a name="export-data-labels"></a>导出数据标签 

完成数据标记项目时，可以从标签项目导出标签数据。 这样，可以捕获对数据的引用及其标签，然后以[COCO 格式](http://cocodataset.org/#format-data)或 Azure 机器学习数据集导出它们。 使用标记项目的“项目详细信息”页上的“导出”按钮。********

### <a name="coco"></a>可可 

 COCO 文件是在 Azure 机器学习工作区的默认 Blob 存储中创建的，该存储位于 *export/coco* 内的某个文件夹中。 

### <a name="azure-machine-learning-dataset"></a>Azure 机器学习数据集

您可以在 Azure 机器学习工作室的 **"数据集"** 部分访问导出的 Azure 机器学习数据集。 数据集**详细信息**页还提供示例代码，以便从 Python 访问标签。

![导出的数据集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>浏览标记的数据集

将标记的数据集加载到熊猫数据框或 Torchvision 数据集中，以利用流行的开源库进行数据探索，PyTorch 提供用于图像转换和培训的库。

### <a name="pandas-dataframe"></a>Pandas 数据帧

您可以使用类[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--)中的方法将标记的数据集加载到熊猫数据框中`azureml-contrib-dataset`。 使用以下 shell 命令安装类： 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>azureml.contrib 命名空间会频繁更改，因为我们正在改进服务。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。

在转换为熊猫数据框时，我们为文件流提供了以下文件处理选项。
* 下载：将数据文件下载到本地路径。
* 装载：将数据文件装载到装载点。 装载仅适用于基于 Linux 的计算，包括 Azure 机器学习笔记本 VM 和 Azure 机器学习计算。

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>火炬视觉数据集

您可以使用[to_torchvision（）](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--)方法从`azureml-contrib-dataset`类将标记的数据集加载到 Torchvision 数据集中。 要使用此方法，您需要安装[PyTorch。](https://pytorch.org/) 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>后续步骤

* 有关完整的培训示例，请参阅[带有标签笔记本的数据集](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)。
