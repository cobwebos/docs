---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: f35245aea0d7ba20561d1504b111e747ccbe5e0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799652"
---
`deploymentconfig.json` 文档中的条目映射到[deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)的参数。 下表描述了 JSON 文档中的实体与方法的参数之间的映射：

| JSON 实体 | 方法参数 | 说明 |
| ----- | ----- | ----- |
| `computeType` | 不可用 | 计算目标。 对于本地目标，该值必须是 `local`。 |
| `port` | `port` | 要在其上公开服务的 HTTP 终结点的本地端口。 |

此 JSON 是用于 CLI 的示例部署配置：

```json
{
    "computeType": "local",
    "port": 32267
}
```
