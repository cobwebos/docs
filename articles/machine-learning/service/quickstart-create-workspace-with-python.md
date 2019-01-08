---
title: 快速入门：Python 入门
titleSuffix: Azure Machine Learning service
description: 在 Python 中开始使用 Azure 机器学习服务。 使用 Python SDK 创建工作区，该工作区是基础的云端块，用于试验、训练和部署机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: da84d6361d80db8aea797827ed3d7bc612e2eda3
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999044"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>快速入门：通过 Python SDK 开始使用 Azure 机器学习

本文介绍通过用于 Python 的 Azure 机器学习 SDK 来创建并使用 Azure 机器学习服务[工作区](concept-azure-machine-learning-architecture.md)。 该工作区是基础的云端块，用于通过机器学习进行机器学习模型的试验、训练和部署。 

请从配置自己的 Python 环境和 Jupyter Notebook 服务器着手。 若要在未安装的情况下运行，请参阅[快速入门：通过 Azure 门户开始使用 Azure 机器学习](quickstart-get-started.md)。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

本文内容：
* 安装 Python SDK。
* 在 Azure 订阅中创建工作区。
* 创建该工作区的配置文件，以便稍后在其他 Notebook 和脚本中使用。
* 编写代码，用于记录工作区中的值。
* 在工作区中查看所记录的值。

创建工作区和配置文件，将其用作其他机器学习教程和操作方法文章的先决条件。 与其他 Azure 服务一样，机器学习也有相关的限制和配额。 [了解配额以及如何请求提高配额。](how-to-manage-quotas.md)

如果区域支持，下述 Azure 资源会自动添加到工作区：
 
- [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)
- [Azure 存储](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](http://aka.ms/AMLFree)。

## <a name="install-the-sdk"></a>安装 SDK

> [!IMPORTANT]
> 如果使用的是 2018 年 9 月 27 日之后创建的数据科学虚拟机，请跳过本部分。
> 在此日期之后创建的数据科学虚拟机附带预先安装的 Python SDK。

本文中的代码需要 Azure 机器学习 SDK 1.0.2 版或更高版本。

在安装该 SDK 之前，建议创建一个隔离的 Python 环境。 尽管本文使用 [Miniconda](https://conda.io/docs/user-guide/install/index.html)，但也可以使用完整安装的 [Anaconda](https://www.anaconda.com/)，或使用 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)。

### <a name="install-miniconda"></a>安装 Miniconda

[下载并安装 Miniconda](https://conda.io/miniconda.html)。 选择 Python 3.7 或更高版本。 请勿选择 Python 2.x。

### <a name="create-an-isolated-python-environment"></a>创建隔离的 Python 环境 

1. 打开命令行窗口，然后使用 Python 3.6 创建名为 myenv 的新 conda 环境。

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. 激活该环境。

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>安装 SDK

在激活的 conda 环境中安装 SDK。 此代码安装机器学习 SDK 的核心组件。 它还在 conda 环境中安装 Jupyter Notebook 服务器。 安装需要数分钟才能完成，取决于计算机的配置。

```sh
# Install Jupyter
conda install nb_conda

# Install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```

还可以使用其他关键字来安装 SDK 的其他组件：

```sh
# Install the base SDK and auto ml components
pip install azureml-sdk[automl]

# Install the base SDK and the model explainability component
pip install azureml-sdk[explain]

# Install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

在 Azure Databricks 环境中，改为使用以下安装命令：

```
# Install the base SDK and automl components in the Azure Databricks environment.
# For more information, see https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks.
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>创建工作区

1. 若要启动 Jupyter Notebook，请输入以下命令：

    ```shell
    jupyter notebook
    ```

1. 在浏览器窗口中，使用默认 `Python 3` 内核创建新的 Notebook。 

1. 若要显示 SDK 版本，请在 Notebook 单元中输入并执行以下 Python 代码：

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. 在 [Azure 门户中的订阅列表](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)内找到 `<azure-subscription-id>` 参数的值。 使用你在其中充当所有者或参与者角色的任何订阅。

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' # Or other supported Azure region   
                        )
   ```

   执行代码时，你可能会收到登录到 Azure 帐户的提示。 登录后，身份验证令牌在本地缓存。

1. 若要查看工作区详细信息（例如关联的存储、容器注册表和密钥保管库），请输入以下代码：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>编写配置文件

将配置文件中工作区的详细信息保存到当前目录中。 此文件名为“aml_config\config.json”。  

稍后可以通过此工作区配置文件轻松加载相同的工作区。 可以使用同一目录或子目录中的其他 Notebook 和脚本来加载它。 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

`write_config()` API 调用在当前目录中创建配置文件。 config.json 文件包含以下脚本：

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>使用工作区

编写一些使用 SDK 基本 API 来跟踪试验运行的代码。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>查看记录的结果
运行完成后，可在 Azure 门户中查看试验运行。 使用以下代码打印导航至最后一次运行的结果的 URL：

```python
print(run.get_portal_url())
```

在浏览器中使用相应的链接查看 Azure 门户中记录的值。

![Azure 门户中记录的值](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>清理资源 
>[!IMPORTANT]
>可以使用此处已创建的资源作为其他机器学习教程和操作方法文章的先决条件。

如果不打算使用本文中创建的资源，请删除它们，以免产生任何费用。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>后续步骤

在本文中，你创建了进行试验和部署模型所需的资源。 在 Notebook 中运行了代码，并在云端工作区中浏览了该代码的运行历史记录。

要将该代码用于机器学习教程，需要在环境中额外安装几个包。

1. 在浏览器中关闭 Notebook。
1. 在命令行窗口中，选择 Ctrl+C 停止 Jupyter Notebook 服务器。
1. 安装额外的包。

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

安装这些包后，继续学习训练和部署模型的教程。 

> [!div class="nextstepaction"]
> [教程：训练图像分类模型](tutorial-train-models-with-aml.md)

还可以学习 [GitHub 中的更高级示例](https://aka.ms/aml-notebooks)。
