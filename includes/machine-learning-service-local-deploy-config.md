---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: a87773c8f6ddda11b62064758b46d666740a03c2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556956"
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
