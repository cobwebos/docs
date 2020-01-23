---
title: 创建和浏览带标签的数据集
titleSuffix: Azure Machine Learning
description: 了解如何从 Azure 机器学习标签项目中导出数据标签，并将其用于机器学习任务。
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549484"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>创建和浏览带标签的 Azure 机器学习数据集

在本文中，你将学习如何从 Azure 机器学习数据标记项目中导出数据标签，并将其加载到常用格式（例如，pandas 数据帧 for data 勘探）或 Torchvision dataset 以进行图像转换。 

## <a name="what-are-datasets-with-labels"></a>什么是带标签的数据集 

带有标签的 Azure 机器学习数据集[TabularDatasets](how-to-create-register-datasets.md#dataset-types)具有标签属性，我们将它们称为标记的数据集。 这些特定类型的 TabularDatasets 仅创建为 Azure 机器学习数据标记项目的输出。 使用[以下步骤](how-to-create-labeling-projects.md)创建数据标记项目。 机器学习支持图像分类的数据标记项目（多标签或多类）和对象标识以及带有界限的框。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://aka.ms/AMLFree)。
* [用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或访问[Azure 机器学习 studio](https://ml.azure.com/)。
    * 安装[contrib](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)包
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
* 访问 Azure 机器学习数据标记项目。 如果没有标签项目，请使用[以下步骤](how-to-create-labeling-projects.md)创建一个。

## <a name="export-data-labels"></a>导出数据标签 

完成数据标记项目后，可以从标签项目导出标签数据。 这样，便可以捕获对数据及其标签的引用，还可以将其导出为[COCO 格式](http://cocodataset.org/#format-data)或 Azure 机器学习数据集。 使用标记项目的“项目详细信息”页上的“导出”按钮。

### <a name="coco"></a>COCO 

 COCO 文件是在 Azure 机器学习工作区的默认 Blob 存储中创建的，该存储位于 *export/coco* 内的某个文件夹中。 

### <a name="azure-machine-learning-dataset"></a>Azure 机器学习数据集

可以在 Azure 机器学习 studio 的 "**数据集**" 部分中访问导出的 Azure 机器学习数据集。 数据集**详细信息**页还提供了从 Python 访问标签的示例代码。

![导出的数据集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>浏览标记的数据集

将标记的数据集加载到 pandas 数据帧或 Torchvision 数据集中，以利用流行的开源库进行数据浏览，并为图像转换和定型提供 PyTorch 的库。

### <a name="pandas-dataframe"></a>Pandas 数据帧

您可以使用 `azureml-contrib-dataset` 类中的[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--)方法将标记的数据集加载到 pandas 数据帧中。 用以下 shell 命令安装类： 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Contrib 命名空间经常更改，因为我们将努力改进服务。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。

转换为 pandas 数据帧时，我们为文件流提供以下文件处理选项。
* 下载：将数据文件下载到本地路径。
* 装载：将数据文件装载到装入点。 装载仅适用于基于 Linux 的计算，包括 Azure 机器学习笔记本 VM 和 Azure 机器学习计算。

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

### <a name="torchvision-datasets"></a>Torchvision 数据集

您可以通过[to_torchvision （）](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--)方法将带标签的数据集加载到 Torchvision 数据集中，`azureml-contrib-dataset` 类中。 若要使用此方法，需安装[PyTorch](https://pytorch.org/) 。 

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

* 有关完整定型示例，请参阅[带标签的数据集笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)。
