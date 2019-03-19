---
title: 在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷
description: 了解如何在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 896cae9b7fc43765e340ba3b92351e04b5512efd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762545"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷

Azure 机器学习是一个协作型拖放式工具，可用于基于数据生成、测试和部署预测分析解决方案。 Azure 机器学习将模型发布为可供自定义应用或 BI 工具（例如 Excel）方便使用的 Web 服务。 自动化机器学习 (AutoML) 可帮助用户使用智能自动化和优化功能创建优质的机器学习模型。 AutoML 可以确定适合用于特定问题类型的算法和超参数。

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>在 HDInsight 群集上安装 Azure 机器学习

> [!Note]
> Azure 机器学习工作区目前已在以下区域推出：eastus、eastus2 和 westcentralus。 HDInsight 群集也应在上述区域之一中创建。

有关 Azure 机器学习和自动化机器学习的一般教程，请参阅[教程：在 Azure 机器学习工作室中创建第一个数据科学试验](../../machine-learning/studio/create-experiment.md)和[教程：使用自动化机器学习生成回归模型](../../machine-learning/service/tutorial-auto-train-models.md)。
若要在 Azure HDInsight 群集上安装 AzureML，请在 HDInsight 3.6 Spark 2.3.0 群集（建议版本）的头节点和工作节点上运行脚本操作 [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh)。 可以在群集创建过程中运行此脚本操作，也可以通过 Azure 门户在现有群集上运行此脚本操作。

有关脚本操作的详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](../hdinsight-hadoop-customize-cluster-linux.md)。 在安装 Azure 机器学习包和依赖项的同时，该脚本还会下载示例 Jupyter Notebook（下载到默认存储的 `HdiNotebooks/PySpark` 路径中）。 此 Jupyter Notebook 演示如何使用自动化机器学习分类器来解决一个简单的分类问题。

> [!Note]
> Azure 机器学习包将安装到 Python3 conda 环境中。 应使用 PySpark3 内核运行安装的 Jupyter Notebook。

## <a name="authentication-for-workspace"></a>工作区身份验证

创建工作区和提交试验需要使用身份验证令牌。 可以使用 [Azure AD 应用程序](../../active-directory/develop/app-objects-and-service-principals.md)生成此令牌。 如果未在帐户中启用多重身份验证，则还可以使用 [Azure AD 用户](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)来生成所需的身份验证令牌。  

以下代码片段使用 **Azure AD 应用程序**创建身份验证令牌。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
                )
```
以下代码片段使用 **Azure AD 用户**创建身份验证令牌。

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>加载数据集

Spark 上的自动化机器学习使用**数据流** - 针对数据执行的松散评估且不可变的操作。  数据流使用公共读取访问权限从 Blob 加载数据集，或使用 SAS 令牌从 Blob URL 加载数据集。

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

还可以使用一次性的注册将数据存储注册到到工作区。

## <a name="experiment-submission"></a>试验提交

在自动化机器学习配置中，应设置属性 `spark_context`，使包在分布式模式下运行。 属性 `concurrent_iterations` 表示并行执行的最大迭代数，应设置为小于 Spark 应用的执行器核心数。

## <a name="next-steps"></a>后续步骤

* 有关自动化机器学习的动机详细信息，请参阅[使用 Microsoft 自动化机器学习逐步发布模型！](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)。
* [Microsoft Research 提供的 AutoML 项目](https://www.microsoft.com/research/project/automl/)