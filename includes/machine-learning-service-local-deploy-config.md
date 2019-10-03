---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: ec0f85ff568bcd89d74ccd727d1c5ecfd8aab398
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390651"
---
`deploymentconfig.json`文档中的项将映射到 LocalWebservice 的参数[。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py) 下表描述了 JSON 文档中的实体与方法的参数之间的映射：

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `computeType` | 不可用 | 计算目标。 对于本地目标，该值必须为`local`。 |
| `port` | `port` | 要在其上公开服务的 HTTP 终结点的本地端口。 |

此 JSON 是用于 CLI 的示例部署配置：

```json
{
    "computeType": "local",
    "port": 32267
}
```
