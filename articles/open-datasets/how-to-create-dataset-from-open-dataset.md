---
title: 使用 Azure 开放数据集创建数据集
titleSuffix: Azure Open Datasets
description: 了解如何从 Azure 开放数据集创建 Azure 机器学习数据集。
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: e6f3a541f1e9dbca2c9949fb0c5cde28cd43e8e5
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183000"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>从 Azure 开放数据集创建 Azure 机器学习数据集
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何从[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)创建 Azure 机器学习数据集，以便为本地或远程试验访问数据。 

通过创建[Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)，你可以创建对数据源位置的引用以及其元数据的副本。 由于数据仍保留在其现有位置，因此不会产生额外的存储成本，也不会无意中更改原始数据源。 此外，还会对数据集进行延迟计算，从而有助于工作流性能速度。
 
若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据集，请参阅[安全地访问数据](../machine-learning/concept-data.md#data-workflow)一文。


打开的数据集位于 Microsoft Azure 的云中，同时包含在[Azure 机器学习 PYTHON SDK](#create-datasets-with-the-sdk)和[Azure 机器学习 studio](#create-datasets-with-the-studio)中。

## <a name="why-use-azure-open-datasets"></a>为什么使用 Azure 开放式数据集？ 

Azure 开放数据集是精选公共数据集，可用于将方案专属特征添加到机器学习解决方案，以提高模型的准确度。 数据集包括不受任何限制的天气、人口普查、节假日、公共安全和位置数据，有助于定型机器学习模型和扩充预测解决方案。 

有关详细信息，请参阅[什么是打开的数据集？](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集，需要做好以下准备：

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 一个 [Azure 机器学习工作区](../machine-learning/how-to-manage-workspace.md)。

* [已安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml-datasets 包。

    * 创建一个[Azure 机器学习的计算实例](../machine-learning/concept-compute-instance.md#managing-a-compute-instance)，该实例是一个完全配置且托管的开发环境，其中包括集成的笔记本和已安装的 SDK。

    **OR**

    * 使用你自己的 Jupyter 笔记本，并使用[这些说明](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)自行安装 SDK。

> [!NOTE]
> 某些数据集类依赖于[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包，此包仅兼容64位 Python。 对于 Linux 用户，仅在以下分发版上支持这些类： Red Hat Enterprise Linux (7、8) 、Ubuntu (14.04、16.04、18.04) 、Fedora (27、28) 、Debian (8、9) 和 CentOS (7) 。

## <a name="create-datasets-with-the-sdk"></a>用 SDK 创建数据集

若要通过 Azure 机器学习 Python SDK 中的开放式数据集类创建数据集，请确保已通过安装包 `pip install azureml-opendatasets` 。 每个离散数据集在 SDK 中由其自身的类表示，某些类作为 `TabularDataset` 和/或 `FileDataset` 提供。 有关完整的类列表，请参阅[参考文档](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)。

可以检索 `TabularDataset` 或 `FileDataset` 形式的某些类，这样就可以直接操控和/或下载文件。 其他类可以使用 `get_tabular_dataset()` 或 `get_file_dataset()` 函数之一，**只**获取数据集。 以下代码示例演示了上述类类型的几个例子。

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

注册从开放数据集创建的数据集时，不会立即下载数据，但以后可按请求（例如，在训练期间）从某个中心存储位置访问数据。

## <a name="create-datasets-with-the-studio"></a>用工作室创建数据集

还可以通过[Azure 机器学习 studio](https://ml.azure.com)从打开的数据集创建数据集。

1. 在工作区中，选择“资产”下的“数据集”选项卡。  在“创建数据集”下拉菜单中，选择“从开放数据集”。 

    ![UI 中的开放数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. 选择数据集对应的磁贴将其选中。 （可以选择使用搜索栏进行筛选。）选择“**下一步**”。

    ![选择数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. 选择数据集的注册名称，并（可选）使用可用的筛选器筛选数据。 在这种情况下，对于**公共假日**数据集，请将时间段筛选为一年，将国家/地区代码筛选为仅限美国。 选择“创建” 。

    ![设置数据集参数并创建数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    现在，该数据集已显示在工作区中的“数据集”下。 可以像使用创建的其他数据集一样来使用它。


## <a name="access-datasets-for-your-experiments"></a>为试验访问数据集

在机器学习试验中使用您的数据集来训练 ML 模型。 [详细了解如何使用数据集进行训练](../machine-learning/how-to-train-with-datasets.md)。

## <a name="example-notebooks"></a>示例笔记本

有关开放式数据集功能的示例和演示，请参阅这些[笔记本](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials)。

## <a name="next-steps"></a>后续步骤

* [使用数据集为模型定型](../machine-learning/how-to-train-with-datasets.md)。

* [创建 Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)。



