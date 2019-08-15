---
title: 如何将模型部署到笔记本 Vm
titleSuffix: Azure Machine Learning service
description: 了解如何使用笔记本 Vm 将 Azure 机器学习服务模型部署为 web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: d6b26dfe1eb8ea65dd7c751a148c599123b0f6db
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947755"
---
# <a name="deploy-a-model-to-notebook-vms"></a>将模型部署到笔记本 Vm

了解如何使用 Azure 机器学习服务将模型部署为笔记本 VM 上的 web 服务。 如果满足以下条件之一, 则使用笔记本 Vm:

- 你需要快速部署并验证你的模型。
- 正在测试一个开发中的模型。

> [!TIP]
> 将模型从笔记本 VM 上的 Jupyter Notebook 部署到同一 VM 上的 web 服务是_本地部署_。 在这种情况下, "本地" 计算机是笔记本 VM。 有关部署的详细信息, 请参阅[部署具有 Azure 机器学习服务的模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 机器学习服务工作区, 其中包含运行的笔记本 VM。 有关详细信息, 请参阅[设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)。

## <a name="deploy-to-the-notebook-vms"></a>部署到笔记本 Vm

笔记本 VM 上包含了演示本地部署的示例笔记本。 使用以下步骤加载笔记本, 并将模型部署为 VM 上的 web 服务:

1. 在[Azure 门户](https://portal.azure.com)中, 选择 Azure 机器学习笔记本 vm。

1. 打开子目录, 然后打开`how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`。 `samples-*` 打开后, 运行笔记本。

    ![笔记本上运行的本地服务的屏幕截图](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. 笔记本将显示服务在其上运行的 URL 和端口。 例如， `https://localhost:6789` 。 您还可以运行包含`print('Local service port: {}'.format(local_service.port))`的单元来显示端口。

    ![正在运行的本地服务端口的屏幕截图](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. 若要从笔记本 VM 测试服务, 请使用`https://localhost:<local_service.port>` URL。 若要从远程客户端测试, 请获取在笔记本 VM 上运行的服务的公共 URL。可以使用以下公式来确定公共 URL;`https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. 例如， `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score` 。

## <a name="test-the-service"></a>测试服务

若要将示例数据提交到正在运行的服务, 请使用以下代码。 将的值`service_url`替换为上一步中的 URL:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score"
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>后续步骤

* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [部署故障排除](how-to-troubleshoot-deployment.md)
* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)