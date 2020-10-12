---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79485975"
---
`deploymentconfig.json` 文档中的条目对应于 [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) 的参数。 下表描述了 JSON 文档中的实体与方法参数之间的映射：

| JSON 实体 | 方法参数 | 说明 |
| ----- | ----- | ----- |
| `computeType` | 不可用 | 计算目标。 对于 AKS，此值必须为 `aks`。 |
| `autoScaler` | 不可用 | 包含自动缩放的配置元素。 请参阅自动缩放程序表。 |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 是否为 Web 服务启用自动缩放。 如果 `numReplicas` = `0`，则为 `True`；否则为 `False`。 |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 自动缩放此 Web 服务时可使用的容器的最小数目。 默认值为 `1`。 |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 自动缩放此 Web 服务时可使用的容器的最大数目。 默认值为 `10`。 |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自动缩放程序尝试缩放此 Web 服务的频率。 默认值为 `1`。 |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 自动缩放程序应尝试维持的此 Web 服务的目标利用率（以低于 100 的百分比表示）。 默认值为 `70`。 |
| `dataCollection` | 不可用 | 包含数据集合的配置元素。 |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | 是否为 Web 服务启用模型数据收集。 默认值为 `False`。 |
| `authEnabled` | `auth_enabled` | 是否为 Web 服务启用密钥身份验证。 `tokenAuthEnabled` 和 `authEnabled` 均不能为 `True`。 默认值为 `True`。 |
| `tokenAuthEnabled` | `token_auth_enabled` | 是否为 Web 服务启用令牌身份验证。 `tokenAuthEnabled` 和 `authEnabled` 均不能为 `True`。 默认值为 `False`。 |
| `containerResourceRequirements` | 不可用 | CPU 和内存实体的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要分配给此 Web 服务的 CPU 核心数。 默认值为 `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 为此 Web 服务分配的内存量 (GB)。 默认值为 `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | 是否为 Web 服务启用 Application Insights 日志记录。 默认值为 `False`。 |
| `scoringTimeoutMs` | `scoring_timeout_ms` | 对 Web 服务调用的评分强制执行的超时时间。 默认值为 `60000`。 |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 此 Web 服务每个节点的最大并发请求数。 默认值为 `1`。 |
| `maxQueueWaitMs` | `max_request_wait_time` | 在返回 503 错误之前，请求在队列中停留的最长时间（毫秒）。 默认值为 `500`。 |
| `numReplicas` | `num_replicas` | 要分配给此 Web 服务的容器数量。 没有默认值。 如果未设置此参数，则默认启用自动缩放程序。 |
| `keys` | 不可用 | 包含密钥的配置元素。 |
| &emsp;&emsp;`primaryKey` | `primary_key` | 要用于此 Web 服务的主要身份验证密钥 |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | 要用于此 Web 服务的辅助身份验证密钥 |
| `gpuCores` | `gpu_cores` | 为此 Webservice 分配的 GPU 内核（每个容器的副本）数。 默认值为 1。 仅支持整数值。 |
| `livenessProbeRequirements` | 不可用 | 包含运行情况探测要求的配置元素。 |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | 执行运行情况探测的频率（秒）。 默认值为 10 秒。 最小值为 1。 |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 启动容器后，启动运行情况探测前的秒数。 默认值为 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 运行情况探测超时前等待的秒数。默认值为 2 秒。 最小值为 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 运行情况探测失败后，将其视为成功所需的最小连续成功次数。 默认值为 1。 最小值为 1。 |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | 当 Pod 启动而运行情况探测失败时，Kubernetes 将尝试 failureThreshold 次才会放弃。 默认值为 3。 最小值为 1。 |
| `namespace` | `namespace` | 将 Web 服务部署到的 Kubernetes 命名空间。 最多 63 个字符，可使用小写字母数字字符（“a”-“z”，“0”-“9”）和连字符（“-”）。 第一个和最后一个字符不能为连字符。 |

以下 JSON 是用于 CLI 的部署配置示例：

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
