---
title: 将模型部署到计算实例
titleSuffix: Azure Machine Learning
description: 了解如何使用计算实例将 Azure 机器学习模型部署为 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 7992283c1652199d665aad07c027b88e9489ddf0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889825"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>将模型部署到 Azure 机器学习计算实例



了解如何使用 Azure 机器学习将模型部署为 Azure 机器学习计算实例上的 Web 服务。 如果满足下列任一条件，请使用计算实例：

- 你需要快速部署并验证你的模型。
- 正在测试一个开发中的模型。

> [!TIP]
> 将模型从计算实例上的 Jupyter Notebook 部署到同一 VM 上的 Web 服务是本地部署。 在这种情况下，“本地”计算机是计算实例。 有关部署的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>先决条件

- 一个有计算实例运行的 Azure 机器学习工作区。 有关详细信息，请参阅[设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)。

## <a name="deploy-to-the-compute-instances"></a>部署到计算实例

用于演示本地部署的示例笔记本包含在计算实例中。 使用以下步骤加载笔记本，并将模型部署为 VM 上的 Web 服务：

1. 从 [Azure 机器学习工作室](https://ml.azure.com)选择 Azure 机器学习计算实例。

1. 打开 `samples-*` 子目录，然后打开 `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`。 打开后，运行笔记本。

    ![笔记本上运行的本地服务的屏幕截图](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. 笔记本会显示其上有服务运行的 URL 和端口。 例如，`https://localhost:6789`。 还可以运行包含 `print('Local service port: {}'.format(local_service.port))` 的单元格以显示端口。

    ![正在运行的本地服务端口的屏幕截图](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. 若要从计算实例测试服务，请使用 `https://localhost:<local_service.port>` URL。 若要从远程客户端进行测试，请获取在计算实例上运行的服务的公共 URL。 可以使用以下公式来确定公共 URL； 
    * 笔记本 VM：`https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`。 
    * 计算实例：`https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`。 

    例如， 
    * 笔记本 VM：`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * 计算实例：`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>测试服务

若要将示例数据提交到正在运行的服务，请使用以下代码。 将 `service_url` 值替换为在上一步获取的 URL：

> [!NOTE]
> 对计算实例上的部署进行身份验证时，将使用 Azure Active Directory 进行身份验证。 对示例代码中 `interactive_auth.get_authentication_header()` 的调用将使用 AAD 对你进行身份验证，并返回一个标头，然后可使用该标头向计算实例上的服务进行身份验证。 有关详细信息，请参阅[为 Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md#interactive-authentication)。
>
> 对 Azure Kubernetes 服务或 Azure 容器实例上的部署进行身份验证时，将使用不同的身份验证方法。 有关详细信息，请参阅[为 Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md#web-service-authentication)。

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>后续步骤

* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑难解答](how-to-troubleshoot-deployment.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
