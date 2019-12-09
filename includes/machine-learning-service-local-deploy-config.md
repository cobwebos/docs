---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: c288ea4a2c62999f27b3f07c39df40dae7ab1426
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926927"
---
`deploymentconfig.json` 文档中的条目映射到 LocalWebservice 的参数。 [deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)。 下表描述了 JSON 文档中的实体与方法的参数之间的映射：

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 计算目标。 对于本地目标，该值必须是 `local`。 |
| `port` | `port` | 要在其上公开服务的 HTTP 终结点的本地端口。 |

此 JSON 是用于 CLI 的示例部署配置：

```json
{
    "computeType": "local",
    "port": 32267
}
```
