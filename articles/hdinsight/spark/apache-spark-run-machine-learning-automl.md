---
title: 在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷
description: 了解如何在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: a731d9e0c97cb8431b1294d961ad46ff324f32be
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141185"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷

Azure 机器学习简化并加速了机器学习模型的构建、培训和部署。 在自动机器学习 (AutoML) 中, 您从具有定义的目标功能的训练数据开始, 然后循环访问算法和特征选择的组合, 根据定型评分自动为您的数据选择最佳模型。 HDInsight 允许客户设置具有数百个节点的群集。 在 HDInsight 群集中的 Spark 上运行的 AutoML 允许用户在这些节点上使用计算容量, 以向外扩展的方式运行定型作业, 以及并行运行多个定型作业。 这样, 用户便可以运行 AutoML 试验, 同时将计算与其他大数据工作负荷共享。
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>在 HDInsight 群集上安装 Azure 机器学习

有关自动机器学习的一般教程, 请[参阅教程:使用自动化机器学习生成回归模型](../../machine-learning/service/tutorial-auto-train-models.md)。
所有新的 HDInsight-Spark 群集都预装了 AutoML SDK。 可以通过此[示例 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi)开始 AutoML on HDInsight。 此 Jupyter Notebook 演示如何使用自动化机器学习分类器来解决一个简单的分类问题。

> [!Note]
> Azure 机器学习包将安装到 Python3 conda 环境中。 应使用 PySpark3 内核运行安装的 Jupyter Notebook。

还可以使用 Zeppelin 笔记本来使用 AutoML。

> [!Note]
> Zeppelin 有一个[已知问题](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html), PySpark3 不会选取适当版本的 Python。 请使用记录的解决方法。

## <a name="authentication-for-workspace"></a>工作区身份验证

创建工作区和提交试验需要使用身份验证令牌。 可以使用 [Azure AD 应用程序](../../active-directory/develop/app-objects-and-service-principals.md)生成此令牌。 如果未在帐户中启用多重身份验证，则还可以使用 [Azure AD 用户](/azure/python/python-sdk-azure-authenticate)来生成所需的身份验证令牌。  

以下代码片段使用 **Azure AD 应用程序**创建身份验证令牌。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```
以下代码片段使用 **Azure AD 用户**创建身份验证令牌。

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>加载数据集

Spark 上的自动化机器学习使用**数据流** - 针对数据执行的松散评估且不可变的操作。  数据流使用公共读取访问权限从 Blob 加载数据集，或使用 SAS 令牌从 Blob URL 加载数据集。

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

还可以使用一次性的注册将数据存储注册到到工作区。

## <a name="experiment-submission"></a>试验提交

在[自动机器学习配置](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig)中, 应将`spark_context`属性设置为在分布式模式下运行包。 属性 `concurrent_iterations` 表示并行执行的最大迭代数，应设置为小于 Spark 应用的执行器核心数。

## <a name="next-steps"></a>后续步骤

* 有关自动机器学习背后的动机的详细信息, 请参阅[使用 Microsoft 的自动机器学习在步调上发布模型!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* 有关使用 Azure ML 自动 ML ML 功能的更多详细信息, 请参阅[Azure 机器学习服务中的新自动化机器学习功能](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Microsoft Research 提供的 AutoML 项目](https://www.microsoft.com/research/project/automl/)
