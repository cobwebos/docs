---
title: 在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷
description: 了解如何在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917027"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>在 Azure HDInsight 中的 Apache Spark 上使用自动化机器学习 (AutoML) 运行 Azure 机器学习工作负荷

Azure 机器学习简化并加速构建、 培训和部署机器学习模型。 在自动化机器学习 (AutoML)，开始使用已定义的目标功能的训练数据，然后循环访问的算法和自动选择根据训练分数对数据的最佳模型的功能选择的组合。 HDInsight 允许客户使用数百个节点设置群集。 在 HDInsight 群集中的 Spark 上运行的 AutoML 允许用户在这些节点使用计算能力，以向外缩放的方式，运行训练作业，并以并行方式运行多个培训作业。 这允许用户同时共享计算其其他大数据工作负荷运行 AutoML 试验。
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>在 HDInsight 群集上安装 Azure 机器学习

常规自动化的机器学习教程，请参阅[教程：使用自动化机器学习生成回归模型](../../machine-learning/service/tutorial-auto-train-models.md)。
所有新的 HDInsight Spark 群集预装了 AzureML AutoML SDK。 你可以开始使用 HDInsight 上的 AutoML 与此[示例 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi)。 此 Jupyter Notebook 演示如何使用自动化机器学习分类器来解决一个简单的分类问题。

> [!Note]
> Azure 机器学习包将安装到 Python3 conda 环境中。 应使用 PySpark3 内核运行安装的 Jupyter Notebook。

或者，可以使用 Zeppelin 笔记本也使用 AutoML。

> [!Note]
> 具有 Zeppelin[已知问题](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html)PySpark3 不会在其中选择了正确版本的 Python。 请使用有案可稽解决。

## <a name="authentication-for-workspace"></a>工作区身份验证

创建工作区和提交试验需要使用身份验证令牌。 可以使用 [Azure AD 应用程序](../../active-directory/develop/app-objects-and-service-principals.md)生成此令牌。 如果未在帐户中启用多重身份验证，则还可以使用 [Azure AD 用户](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)来生成所需的身份验证令牌。  

以下代码片段使用 **Azure AD 应用程序**创建身份验证令牌。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
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

在中[自动化的机器学习配置](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig)，该属性`spark_context`应设置为要在分布式模式下运行的包。 属性 `concurrent_iterations` 表示并行执行的最大迭代数，应设置为小于 Spark 应用的执行器核心数。

## <a name="next-steps"></a>后续步骤

* 自动化的机器学习背后的动机的详细信息，请参阅[模型发布节奏使用 Microsoft 的自动完成机器学习 ！](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* 使用 Azure 机器学习自动化机器学习功能的更多详细信息，请参阅[新建自动在 Azure 机器学习服务中的机器学习功能](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Microsoft Research 提供的 AutoML 项目](https://www.microsoft.com/research/project/automl/)
