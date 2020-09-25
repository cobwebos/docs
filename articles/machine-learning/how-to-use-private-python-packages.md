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
ms.openlocfilehash: 1afa9173c2ca3704bf4408c271e3cf950ef79077
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302210"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>将专用 Python 包与 Azure 机器学习一起使用


本文介绍如何在 Azure 机器学习内安全地使用专用 Python 包。 专用 Python 包的用例包括：

 * 已开发不想公开分享的专用包。
 * 希望使用企业防火墙中存储的包的特选存储库。

建议的方法取决于单个 Azure 机器学习工作区有少量包，还是组织中所有工作区有整个包存储库。

通过 [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) 类使用专用包。 在环境内可声明要使用的 Python 包，包括专用包。 若要大体了解 Azure 机器学习中的环境，请参阅[如何使用环境](how-to-use-environments.md)。 

## <a name="prerequisites"></a>先决条件

 * [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
 * 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>使用少量包进行开发和测试

对于单个工作区的少量专用包，请使用静态 [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=trueadd-private-pip-wheel-workspace--file-path--exist-ok-false-) 方法。 此方法可让你快速地将专用包添加到工作区，并且非常适用于开发和测试目的。

将文件路径参数指向本地 wheel 文件，然后运行 ```add_private_pip_wheel``` 命令。 该命令返回用于跟踪工作区中包位置的 URL。 捕获存储 URL，并将其传递给 `add_pip_package()` 方法。

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Azure 机器学习服务在内部将 URL 替换为安全的 SAS URL，使 wheel 文件保持专用和安全。

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>从 Azure DevOps 源使用包的存储库

如果正积极开发机器学习应用程序的 Python 包，可以将它们作为项目托管在 Azure DevOps 存储库，并将其作为源发布。 此方法可让你集成 DevOps 工作流，以便通过 Azure 机器学习工作区生成包。 若要了解如何使用 Azure DevOps 设置 Python 源，请阅读 [Azure Artifacts 中的 Python 包入门](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops&preserve-view=true)

此方法使用个人访问令牌对存储库进行身份验证。 同样的方法适用于采用基于令牌的身份验证的其他存储库，如专用 GitHub 存储库。 

 1. 为 Azure DevOps 实例[创建个人访问令牌 (PAT)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&preserve-view=true&tabs=preview-page#create-a-pat)。 将令牌的范围设为 Packaging > Read。 

 2. 使用 [Workspace.set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-connection-name--category--target--authtype--value-) 方法添加 Azure DevOps URL 和 PAT 作为工作区属性。

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

环境现在已准备就绪，可用于训练运行或 Web 服务终结点部署。 构建环境时，Azure 机器学习服务使用 PAT 通过匹配的基 URL 对源进行身份验证。

## <a name="use-a-repository-of-packages-from-private-storage"></a>从专用存储使用包的存储库

可以在组织的防火墙内使用 Azure 存储帐户的包。 该存储帐户可以包含一组特选包，或者可公开使用的包的内部镜像。

若要设置此类专用存储，请参阅 [保护 Azure 机器学习工作区和关联的资源](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)。 还必须在 [VNet 后面放置 Azure 容器注册表 (ACR) ](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)。

> [!IMPORTANT]
> 必须完成此步骤才能使用专用包存储库训练或部署模型。

完成这些部署后，便可以通过 Azure Blob 存储中的完整 URL 引用 Azure 机器学习环境定义中的包。

## <a name="next-steps"></a>后续步骤

 * 详细了解 [Azure 机器学习的企业安全性](concept-enterprise-security.md)
