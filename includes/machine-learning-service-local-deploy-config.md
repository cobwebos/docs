---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: e8c0b35f2ac6d3468a5e38a90cc7f4de09d3682b
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348548"
---
`deploymentconfig.json`文档中的项将映射到 LocalWebservice 的参数[。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py) 下表描述了 JSON 文档中的实体与方法的参数之间的映射:

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `computeType` | 不可用 | 计算目标。 对于 local, 该值必须为`local`。 |
| `port` | `port` | 要在其上公开服务的 HTTP 终结点的本地端口。 |

以下 JSON 是用于 CLI 的示例部署配置:

```json
{
    "computeType": "local",
    "port": 32267
}
```
