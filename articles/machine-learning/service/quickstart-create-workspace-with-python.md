---
title: 快速入门：Python 入门
titleSuffix: Azure Machine Learning service
description: 在 Python 中开始使用 Azure 机器学习服务。 使用 Python SDK 创建工作区，该工作区是基础的云端块，用于试验、训练和部署机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/22/2019
ms.custom: seodec18
ms.openlocfilehash: e0c235a9fd3898fa4525651d514c77432627603c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238952"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>快速入门：通过 Python SDK 开始使用 Azure 机器学习

本文介绍如何通过适用于 Python 的 Azure 机器学习 SDK 3 来创建并使用 Azure 机器学习服务[工作区](concept-azure-machine-learning-architecture.md)。 该工作区是基础的云端块，用于通过机器学习进行机器学习模型的试验、训练和部署。

请从配置自己的 Python 环境和 Jupyter Notebook 服务器着手。 若要在未安装的情况下运行，请参阅[快速入门：通过 Azure 门户开始使用 Azure 机器学习](quickstart-get-started.md)。 

观看本快速入门的视频版本：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

在本快速入门中，请执行以下操作：

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

>[!NOTE]
> 本文中的代码需要 Azure 机器学习 SDK 版本 1.0.2 或更高版本，并且已使用版本 1.0.8 进行测试。


如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](http://aka.ms/AMLFree)。

## <a name="install-the-sdk"></a>安装 SDK

> [!IMPORTANT]
> 如果使用的是 Azure Data Science Virtual Machine 或 Azure Databricks，请跳过本部分。
> * 在 2018 年 9 月 27 日之后创建的 Azure Data Science Virtual Machine 已预装 Python SDK。
> * 在 Azure Databricks 环境中，请改用 [Databricks 安装步骤](how-to-configure-environment.md#azure-databricks)。

在安装该 SDK 之前，建议创建一个隔离的 Python 环境。 尽管本文使用 [Miniconda](https://docs.conda.io/en/latest/miniconda.html)，但也可以使用完整安装的 [Anaconda](https://www.anaconda.com/)，或使用 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)。

### <a name="install-miniconda"></a>安装 Miniconda

[下载并安装 Miniconda](https://docs.conda.io/en/latest/miniconda.html)。 选择要安装的 Python 3.7 或更高版本。 请勿选择 Python 2.x 版本。  

### <a name="create-an-isolated-python-environment"></a>创建隔离的 Python 环境

1. 打开命令行窗口，然后创建名为 *myenv* 的新 conda 环境，并安装 Python 3.6。 Azure 机器学习 SDK 可与 Python 3.5.2 或更高版本配合工作，但自动化机器学习组件不能在 Python 3.7 上完全正常运行。

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. 激活该环境。

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>安装 SDK

1. 在已激活的 conda 环境中，安装机器学习 SDK 的核心组件以及 Jupyter Notebook 功能。  安装需要数分钟才能完成，取决于计算机的配置。

  ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. 在 conda 环境中安装 Jupyter Notebook 服务器。

  ```shell
    conda install nb_conda
    ```

1. 若要在 Azure 机器学习教程中使用此环境，请安装这些包。

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. 若要在 Azure 机器学习教程中使用此环境，请安装自动化机器学习组件。

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

## <a name="create-a-workspace"></a>创建工作区

使用 Python SDK 在 Jupyter Notebook 中创建工作区。

1. 创建要用于快速入门和教程的目录，并/或通过 cd 切换到该目录。

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
                         location='eastus2' 
                        )
   ```

   执行代码时，你可能会收到登录到 Azure 帐户的提示。 登录后，身份验证令牌在本地缓存。

1. 若要查看工作区详细信息（例如关联的存储、容器注册表和密钥保管库），请输入以下代码：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>编写配置文件

将配置文件中工作区的详细信息保存到当前目录中。 此文件名为“aml_config\config.json”。  

稍后可以通过此工作区配置文件轻松加载相同的工作区。 可以使用同一目录或子目录中的其他 Notebook 和脚本来加载它。  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

此 `write_config()` API 调用在当前目录中创建配置文件。 config.json 文件包含以下内容：

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>使用工作区

运行一些使用 SDK 基本 API 来跟踪试验运行的代码。

1. 在工作区中创建一个试验。
1. 将单个值记录到试验中。
1. 将值列表记录到试验中。

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

> [!div class="nextstepaction"]
> [教程：训练图像分类模型](tutorial-train-models-with-aml.md)

还可以学习 [GitHub 中的更高级示例](https://aka.ms/aml-notebooks)。
