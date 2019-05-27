---
title: 创建工作区
titleSuffix: Azure Machine Learning service
description: 使用 Azure 门户、 SDK、 模板或 CLI 创建 Azure 机器学习服务工作区。 此工作区提供了集中的位置来处理在使用 Azure 机器学习服务时创建的所有项目。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: c6c32265e6fc2fc2bb0d6b00d89862e200731a2a
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "66016446"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>创建 Azure 机器学习服务工作区

若要使用 Azure 机器学习服务，需要[ **Azure 机器学习服务工作区**](concept-workspace.md)。  此工作区是服务的顶级资源，并提供集中的位置来使用您创建的所有内容。 

在本文中，您将学习如何创建工作区中使用任何一种方法： 
* [Azure 门户](#portal)接口
* [Azure 机器学习的 Python SDK](#sdk)
* Azure 资源管理器模板
* [Azure 机器学习 CLI](#cli)

使用此处中的步骤创建的工作区可以用作其他教程和操作指南文章的先决条件。

如果你想要使用脚本来设置本地 Python 环境中的自动化的机器学习请参阅[Azure/MachineLearningNotebooks GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)有关的说明。  

创建一个工作区时自动 （如果它们可用区域范围） 添加以下 Azure 资源：
 
- [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)：若要最小化成本，ACR 是**延迟加载**创建部署映像之前。
- [Azure 存储](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>与其他 Azure 服务一样，机器学习也有相关的限制和配额。 [了解配额以及如何请求提高配额。](how-to-manage-quotas.md)


## <a name="prerequisites"></a>必备组件
必须有 Azure 订阅，才能创建工作区。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

## <a name="portal"></a> Azure 门户

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

无论它的创建方式，您可以查看中的工作区[Azure 门户](https://portal.azure.com/)。  请参阅[查看工作区](how-to-manage-workspace.md#view)有关详细信息。

## <a name="sdk"></a> Python SDK

创建使用 Python SDK 在工作区。 首先，您需要安装 SDK。

> [!IMPORTANT]
> 如果你使用 Azure 数据科学虚拟机或 Azure Databricks，跳过安装的 SDK。
> * 在 2018 年 9 月 27 日之后创建的 Azure Data Science Virtual Machine 已预装 Python SDK。 跳过安装，并且开头[使用 SDK 创建一个工作区](#sdk-create)。
> * 在 Azure Databricks 环境中，请改用 [Databricks 安装步骤](how-to-configure-environment.md#azure-databricks)。

>[!NOTE]
> 使用这些说明来安装和使用从本地计算机的 SDK。 若要在远程虚拟机上使用 Jupyter，设置远程桌面或终端会话 X。

在安装该 SDK 之前，建议创建一个隔离的 Python 环境。 尽管本文使用 [Miniconda](https://docs.conda.io/en/latest/miniconda.html)，但也可以使用完整安装的 [Anaconda](https://www.anaconda.com/)，或使用 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)。

这篇文章中的说明将安装所有所需运行快速入门和教程笔记本的包。  其他示例笔记本可能需要安装附加组件。  有关这些组件的详细信息，请参阅[安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install)。

### <a name="install-miniconda"></a>安装 Miniconda

[下载并安装 Miniconda](https://docs.conda.io/en/latest/miniconda.html)。 选择 Python 3.7 版本进行安装。 请勿选择 Python 2.x 版本。  

### <a name="create-an-isolated-python-environment"></a>创建隔离的 Python 环境

1. 打开 Anaconda 提示符，然后创建名为的新 conda 环境*myenv*并安装 Python 3.6.5。 Azure 机器学习 SDK 可与 Python 3.5.2 或更高版本配合工作，但自动化机器学习组件不能在 Python 3.7 上完全正常运行。  下载组件和包时，创建环境需要花费几分钟。 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. 激活该环境。

    ```shell
    conda activate myenv
    ```

1. 启用特定于环境的 ipython 内核：

    ```shell
    conda install notebook ipykernel
    ```

    创建内核：

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>安装 SDK

1. 在已激活的 conda 环境中，安装机器学习 SDK 的核心组件以及 Jupyter Notebook 功能。 安装需要数分钟才能完成，取决于计算机的配置。

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. 若要在 Azure 机器学习教程中使用此环境，请安装这些包。

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. 若要在 Azure 机器学习教程中使用此环境，请安装自动化机器学习组件。

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> 在某些命令行工具中，可能需要添加引号，如下所示：
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> 使用 SDK 创建一个工作区

使用 Python SDK 在 Jupyter Notebook 中创建工作区。

1. 创建要用于快速入门和教程的目录，并/或通过 cd 切换到该目录。

1. 若要启动 Jupyter Notebook，请输入以下命令：

    ```shell
    jupyter notebook
    ```

1. 在浏览器窗口中，使用默认 `Python 3` 内核创建新的 Notebook。 

1. 若要显示 SDK 版本，请在 Notebook 单元中输入并执行以下 Python 代码：

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. 在 [Azure 门户中的订阅列表](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)内找到 `<azure-subscription-id>` 参数的值。 使用你在其中充当所有者或参与者角色的任何订阅。 有关角色的详细信息，请参阅[访问管理对 Azure 机器学习工作区](how-to-assign-roles.md)一文。

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


### <a name="write-a-configuration-file"></a>编写配置文件

将配置文件中工作区的详细信息保存到当前目录中。 此文件称为 *.azureml/config.json*。  

稍后可以通过此工作区配置文件轻松加载相同的工作区。 您可以与其他笔记本和相同的目录或使用代码的子目录中脚本加载它`ws=Workspace.from_config()`。 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

此 `write_config()` API 调用在当前目录中创建配置文件。 config.json 文件包含以下内容：

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> 若要使用 Python 脚本或其他目录中的 Jupyter 笔记本中的工作区，请将此文件复制到该目录。 该文件可以为同一个目录，一个名为子目录 *.azureml*，或父目录中。

## <a name="resource-manager-template"></a>资源管理器模板

若要使用模板创建一个工作区，请参阅[使用模板创建 Azure 机器学习服务工作区](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>命令行接口

若要使用 CLI 创建一个工作区，请参阅[CLI 扩展用于 Azure 机器学习服务](reference-azure-machine-learning-cli.md)。

## <a name="clean-up-resources"></a>清理资源 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

* 无论它的创建方式，您可以查看中的工作区[Azure 门户](https://portal.azure.com/)。  请参阅[查看工作区](how-to-manage-workspace.md#view)有关详细信息。

* 尝试使用这些快速入门和教程工作区。

    * 快速入门：[在云中运行 Jupyter notebook](quickstart-run-cloud-notebook.md)。
    * 快速入门：[在你自己的服务器上运行 Jupyter notebook](quickstart-run-local-notebook.md)。
    * 本教程分为两部分：[训练](tutorial-train-models-with-aml.md)并[部署](tutorial-deploy-models-with-aml.md)图像分类模式。
    * 本教程分为两部分：[准备数据](tutorial-data-prep.md)并[使用自动化的机器学习](tutorial-auto-train-models.md)生成回归模型。

* 了解如何[配置开发环境](how-to-configure-environment.md)。

* 详细了解如何[适用于 Python 的 Azure 机器学习 SDK](https://aka.ms/aml-sdk)。
