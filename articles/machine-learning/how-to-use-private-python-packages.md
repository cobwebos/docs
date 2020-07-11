---
title: 使用专用 Python 包
titleSuffix: Azure Machine Learning
description: 从 Azure 机器学习环境安全地访问专用 Python 包。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 580525b2e8e408949ce1d8f2d1b8241c431fc755
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209272"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>将专用 Python 包与 Azure 机器学习配合使用
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 Azure 机器学习内安全地使用专用 Python 包。 专用 Python 包的用例包括：

 * 你已开发不想公开共享的专用包。
 * 希望使用企业防火墙中存储的包的特选存储库。

建议的方法取决于单个 Azure 机器学习工作区是否有少量包，或者组织中所有工作区的整个包存储库。

专用包通过[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment)类使用。 在环境中，可声明要使用的 Python 包，包括专用包。 若要深入了解 Azure 机器学习中的环境，请参阅[如何使用环境](how-to-use-environments.md)。 

## <a name="prerequisites"></a>必备知识

 * [用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * [Azure 机器学习工作区](how-to-manage-workspace.md)

### <a name="use-small-number-of-packages-for-development-and-testing"></a>使用少量包进行开发和测试

对于单个工作区的少量专用包，请使用静态 [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) 方法。 此方法可让你快速地将专用包添加到工作区，并且非常适合于开发和测试目的。

将文件路径参数指向本地滑轮文件，然后运行 ```add_private_pip_wheel``` 命令。 命令返回一个用于跟踪包在工作区中的位置的 URL。 捕获存储 URL 并向其传递 `add_pip_package()` 方法。

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Azure 机器学习服务在内部将 URL 替换为安全 SAS URL，使滚轮文件保持私密和安全。

### <a name="consume-a-repository-of-packages-from-azure-devops-feed"></a>从 Azure DevOps 源使用包的存储库

如果你正在积极地开发机器学习应用程序的 Python 包，你可以将它们作为项目托管在 Azure DevOps 存储库中，并将其发布为源。 此方法可让你集成 DevOps 工作流，以便通过 Azure 机器学习工作区生成包。 若要了解如何使用 Azure DevOps 设置 Python 源，请阅读[Azure Artifacts 中的 Python 包入门](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops)

此方法使用个人访问令牌对存储库进行身份验证。 同样的方法适用于具有基于令牌的身份验证的其他存储库，如专用 GitHub 存储库。 

 1. 为 Azure DevOps 实例[创建 (PAT) 的个人访问令牌](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat)。 将令牌的范围设置为__打包 > 读取__。 

 2. 使用[工作区 set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-)方法添加 AZURE DevOps URL 和 PAT 作为工作区属性。

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. 创建 Azure 机器学习环境，并从源添加 Python 包。
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

环境现在已准备就绪，可在定型运行或 web 服务终结点部署中使用。 构建环境时，Azure 机器学习服务使用 PAT 通过匹配的基 URL 对源进行身份验证。

### <a name="consume-a-repository-of-packages-from-private-storage"></a>使用专用存储中的包存储库

你可以在组织的防火墙内使用 Azure 存储帐户中的包。 此类存储帐户可以包含一组用于企业使用的特选包，也可以包含一个可公开使用的包的内部镜像。

若要设置此类专用存储：

 1. [将工作区放在虚拟网络)  (VNET 中](how-to-enable-virtual-network.md)。
 2. 创建存储帐户并[禁用公共访问权限](https://docs.microsoft.com/azure/storage/common/storage-network-security)。
 2. 将想要使用的 Python 包置于存储帐户中的容器内 
 3. [允许存储帐户从工作区 VNET 访问](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) 

然后，可以通过 Azure blob 存储中的完整 URL 引用 Azure 机器学习环境定义中的包。

## <a name="next-steps"></a>后续步骤

 * 详细了解[Azure 机器学习中的企业安全性](concept-enterprise-security.md)
