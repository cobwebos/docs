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
ms.openlocfilehash: a80559761c8a3eba6045db5cd99a7719dd041fa8
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704389"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>从 Azure 开放数据集创建 Azure 机器学习数据集

本文介绍了如何将特选扩充数据引入 [Azure 机器学习](../machine-learning/overview-what-is-azure-ml.md) 数据集和 [Azure 开放式数据集](https://docs.microsoft.com/azure/open-datasets/)的本地或远程机器学习试验。 

通过创建 [Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)，你可以创建对数据源位置的引用以及其元数据的副本。 由于数据集是延迟计算的，并且数据仍保留在其现有位置，因此
* 不会产生额外的存储成本。
* 不会无意中更改原始数据源。 
* 会提高 ML 工作流性能速度。

若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据集，请参阅[安全地访问数据](../machine-learning/concept-data.md#data-workflow)一文。

Azure 开放式数据集是特选的公共数据集，可用于添加特定于方案的功能，以丰富预测解决方案并提高其准确性。 请参阅可帮助你训练机器学习模型的公共域数据的 [开放数据集目录](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) ，例如：

* [等](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [普查](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [节日](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [公共安全](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

打开的数据集位于 Microsoft Azure 的云中，同时包含在 [Azure 机器学习 PYTHON SDK](#create-datasets-with-the-sdk) 和 [Azure 机器学习 studio](#create-datasets-with-the-studio)中。


## <a name="prerequisites"></a>先决条件

对于本文，你需要：

* Azure 订阅。 如果没有订阅，请在开始之前创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 一个 [Azure 机器学习工作区](../machine-learning/how-to-manage-workspace.md)。

* [安装的适用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true )，其中包括 `azureml-datasets` 包。

    * 创建一个 [Azure 机器学习计算实例](../machine-learning/how-to-create-manage-compute-instance.md)，它是一个完全配置且托管的开发环境，其中包括集成的笔记本和已安装的 SDK。

    **或者**

    * 使用你自己的 Python 环境，并使用 [这些说明](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true )自行安装 SDK。

> [!NOTE]
> 某些数据集类依赖于 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 包，此包仅兼容64位 Python。 对于 Linux 用户，只有以下分发版支持这些类：Red Hat Enterprise Linux（7、8）、Ubuntu（14.04、16.04、18.04）、Fedora（27、28）、Debian（8、9）和 CentOS (7)。

## <a name="create-datasets-with-the-sdk"></a>用 SDK 创建数据集

若要通过 Python SDK 中的 Azure 开放式数据集类创建 Azure 机器学习数据集，请确保已通过安装了包 `pip install azureml-opendatasets` 。 每个离散数据集均由其在 SDK 中的类来表示，而某些类可作为 Azure 机器学习[ `TabularDataset` 、 `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)或两者使用。 有关类的完整列表，请参阅 [参考文档](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py&preserve-view=true ) `opendatasets` 。

您可以检索某些 `opendatasets` 类作为 `TabularDataset` 或 `FileDataset` ，这允许您直接操作和/或下载文件。 其他 **类只能在** `get_tabular_dataset()` `get_file_dataset()` `Dataset` Python SDK 中使用类中的或函数来获取数据集。

下面的代码演示 MNIST `opendatasets` 类可以返回 `TabularDataset` 或 `FileDataset` 。 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

在此示例中，糖尿病 `opendatasets` 类只可用作 `TabularDataset` ，因此使用 `get_tabular_dataset()` 。

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>注册数据集

向工作区注册 Azure 机器学习数据集，以便与其他人共享这些数据集，并在工作区中的试验间重复使用这些数据集。 在注册从打开的数据集创建的 Azure 机器学习数据集时，不会立即下载数据，但会在定型期间请求 (时访问数据，例如从中心存储位置) 。

若要将数据集注册到工作区，请使用 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-&preserve-view=true ) 方法。 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>用工作室创建数据集

你还可以使用 [Azure 机器学习 studio](https://ml.azure.com)创建 Azure 开放数据集中的 Azure 机器学习数据集，这是一个合并的 web 界面，其中包含用于针对所有技能级别的数据科学专业人员执行数据科学方案的机器学习工具。

> [!Note]
> 通过 Azure 机器学习工作室创建的数据集会自动注册到工作区。

1. 在工作区中，选择“资产”下的“数据集”选项卡。  在“创建数据集”下拉菜单中，选择“从开放数据集”。 

    ![UI 中的开放数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. 选择数据集对应的磁贴将其选中。 （可以选择使用搜索栏进行筛选。）选择“**下一步**”。

    ![选择数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. 选择数据集的注册名称，并（可选）使用可用的筛选器筛选数据。 在这种情况下，对于 **公共假日** 数据集，请将时间段筛选为一年，将国家/地区代码筛选为仅限美国。 有关数据的详细信息，请参阅 [Azure 开放数据集目录](https://azure.microsoft.com/services/open-datasets/catalog) ，例如字段说明和日期范围。 选择“创建” 。

    ![设置数据集参数并创建数据集](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    现在，该数据集已显示在工作区中的“数据集”下。 可以像使用创建的其他数据集一样来使用它。


## <a name="access-datasets-for-your-experiments"></a>为试验访问数据集

在机器学习试验中使用数据集来训练 ML 模型。 [详细了解如何使用数据集进行训练](../machine-learning/how-to-train-with-datasets.md)。

## <a name="example-notebooks"></a>示例笔记本

有关开放式数据集功能的示例和演示，请参阅这些 [示例笔记本](samples.md)。

## <a name="next-steps"></a>后续步骤

* [训练首个 ML 模型](../machine-learning/tutorial-1st-experiment-sdk-train.md)。

* [训练数据集](../machine-learning/how-to-train-with-datasets.md)。

* [创建 Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)。



