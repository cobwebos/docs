---
title: 将 ml 模型部署到 Azure App Service (预览版)
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务将模型部署到 Azure App Service 中的 Web 应用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: 84de9d53b19f5aa9b73570aa0d115d204e8b6596
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848220"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>将机器学习模型部署到 Azure App Service (预览版)

了解如何在 Azure App Service 中以 web 应用的形式在 Azure 机器学习服务中部署模型。

> [!IMPORTANT]
> 尽管 Azure 机器学习服务和 Azure App Service 都已正式发布, 但将模型从机器学习服务部署到应用服务的功能处于预览阶段。

利用 Azure 机器学习 service, 你可以从经过训练的机器学习模型中创建 Docker 映像。 此映像包含一个 web 服务, 该服务接收数据、将数据提交到模型, 然后返回响应。 Azure App Service 可用于部署映像, 并提供以下功能:

* [SSL](/azure/app-service/app-service-web-ssl-cert-load)对客户端和服务之间的安全通信的支持。
* 向[外扩展](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)到多个实例, 而无需重新部署。
* 增强安全性的[高级身份验证](/azure/app-service/configure-authentication-provider-aad)。

有关 Azure App Service 提供的功能的详细信息, 请参阅[应用服务概述](/azure/app-service/overview)。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习服务工作区。 有关详细信息, 请参阅[创建工作区一](how-to-manage-workspace.md)文。
* 已在工作区中注册的经过训练的机器学习模型。 如果没有模型, 请使用[图像分类教程: 训练模型](tutorial-train-models-with-aml.md)训练和注册。
* 从模型创建的 Docker 映像。 如果没有图像, 请使用[映像分类: 部署模型](tutorial-deploy-models-with-aml.md)创建一个。

## <a name="deploy-image-as-a-web-app"></a>将映像部署为 Web 应用

1. 在[Azure 门户](https://portal.azure.com)中, 选择 Azure 机器学习工作区。 从 "__概述__" 部分中, 使用__注册表__链接访问工作区的 Azure 容器注册表。

    ![工作区概述的屏幕截图](media/how-to-deploy-app-service/workspace-overview.png)

2. 从 Azure 容器注册表中, 选择 "__存储库__", 然后选择要部署的__映像名称__。 对于要部署的版本, 请选择 " __...__ " 条目, 然后将__其部署到 web 应用__。

    ![从 ACR 部署到 web 应用的屏幕截图](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. 若要创建 Web 应用, 请提供站点名称、订阅、资源组, 并选择 "应用服务计划/位置"。 最后，选择“创建”。

    !["新建 web 应用" 对话框的屏幕截图](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>使用 Web 应用

在[Azure 门户](https://portal.azure.com)中, 选择上一步中创建的 Web 应用。 从 "__概述__" 部分中, 复制__URL__。 此值为服务的__基 URL__ 。

![Web 应用概述的屏幕截图](media/how-to-deploy-app-service/web-app-overview.png)

向模型传递请求的 web 服务位于`{baseurl}/score`。 例如， `https://mywebapp.azurewebsites.net/score` 。 以下 Python 代码演示了如何将数据提交到 URL 并显示响应:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>后续步骤

* 有关配置 Web 应用的详细信息, 请参阅[Linux 上的应用服务](/azure/app-service/containers/)文档。
* 有关缩放的详细信息, 请参阅[Azure 中的自动缩放入门](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)。
* 有关 SSL 支持的详细信息, 请参阅[在 Azure App Service 中使用 SSL 证书](/azure/app-service/app-service-web-ssl-cert-load)。
* 有关身份验证的详细信息, 请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](/azure/app-service/configure-authentication-provider-aad)。