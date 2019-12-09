---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: 4f861d5e7832512e2c2732f1ce5dbf6ac76b91b1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935899"
---
`deploymentconfig.json` 文档中的条目映射到 AciWebservice 的参数。 [deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)。 下表描述了 JSON 文档中的实体与方法的参数之间的映射：

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 计算目标。 对于 ACI，此值必须为 `ACI`。 |
| `containerResourceRequirements` | NA | CPU 和内存实体的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要分配的 CPU 内核数。 默认值，`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 为此 web 服务分配的内存量（以 GB 为限）。 默认值，`0.5` |
| `location` | `location` | 要将此 Webservice 部署到的 Azure 区域。 如果未指定，将使用工作区位置。 有关可用区域的详细信息，请参阅： [ACI 区域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | 是否为此 Webservice 启用身份验证。 默认为 False |
| `sslEnabled` | `ssl_enabled` | 是否为此 Webservice 启用 SSL。 默认为 False。 |
| `appInsightsEnabled` | `enable_app_insights` | 是否为此 Webservice 启用 AppInsights。 默认为 False |
| `sslCertificate` | `ssl_cert_pem_file` | 启用 SSL 时所需的证书文件 |
| `sslKey` | `ssl_key_pem_file` | 启用 SSL 时所需的密钥文件 |
| `cname` | `ssl_cname` | 如果启用了 SSL，则为 cname |
| `dnsNameLabel` | `dns_name_label` | 评分终结点的 dns 名称标签。 如果未指定，则将为评分终结点生成唯一的 dns 名称标签。 |

以下 JSON 是用于 CLI 的示例部署配置：

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```