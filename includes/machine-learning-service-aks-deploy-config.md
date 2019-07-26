---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 31d20f4824b034230bc941858e8ecb20cc00b6b2
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348525"
---
`deploymentconfig.json`文档中的项将映射到 AksWebservice 的参数[。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) 下表描述了 JSON 文档中的实体与方法的参数之间的映射:

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `computeType` | 不可用 | 计算目标。 对于 AKS, 该值必须为`aks`。 |
| `autoScaler` | 不可用 | 包含自动缩放的配置元素。 请参阅自动缩放程序表。 |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 是否为 web 服务启用自动缩放。 如果`numReplicas`  = 为,`True`则为;`False`否则为。 `0` |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 自动缩放此 web 服务时要使用的容器的最小数目。 默认值为。 `1` |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 自动缩放此 web 服务时要使用的最大容器数。 默认值为。 `10` |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自动缩放程序尝试缩放此 web 服务的频率。 默认值为。 `1` |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 自动缩放程序应为此 web 服务尝试维护的目标利用率 (百分比为 100)。 默认值为。 `70` |
| `dataCollection` | 不可用 | 包含数据集合的配置元素。 |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | 是否为 web 服务启用模型数据收集。 默认值为。 `False` |
| `authEnabled` | `auth_enabled` | 是否为 web 服务启用身份验证。 默认值为。 `True` |
| `containerResourceRequirements` | 不可用 | CPU 和内存实体的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要分配给此 web 服务的 CPU 内核数。 出厂`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 为此 web 服务分配的内存量 (以 GB 为限)。 缺省值`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | 是否为 web 服务启用 Application Insights 日志记录。 默认值为。 `False` |
| `scoringTimeoutMs` | `scoring_timeout_ms` | 对 web 服务的评分调用强制执行的超时值。 默认值为。 `60000` |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 此 web 服务每个节点的最大并发请求数。 默认值为。 `1` |
| `maxQueueWaitMs` | `max_request_wait_time` | 在返回503错误之前, 请求将在三个队列中停留的最长时间 (毫秒)。 默认值为。 `500` |
| `numReplicas` | `num_replicas` | 要分配给此 web 服务的容器数。 没有默认值。 如果未设置此参数, 则默认情况下将启用自动缩放程序。 |
| `keys` | 不可用 | 包含键的配置元素。 |
| &emsp;&emsp;`primaryKey` | `primary_key` | 要用于此 Webservice 的主要身份验证密钥 |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | 要用于此 Webservice 的辅助身份验证密钥 |
| `gpuCores` | `gpu_cores` | 要分配给此 Webservice 的 GPU 核心数。 默认值为1。 仅支持整数值。 |
| `livenessProbeRequirements` | 不可用 | 包含活动探测要求的配置元素。 |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | 执行活动探测的频率 (以秒为单位)。 默认值为10秒。 最小值为1。 |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 启动活动探测之前启动容器的秒数。 默认值为310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 活动探测超时前等待的秒数。默认值为2秒。 最小值为1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 在失败后将活动探测视为成功的最小连续尝试次数。 默认值为 1。 最小值为1。 |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | 当 Pod 启动并且活动探测失败时, Kubernetes 将在放弃之前尝试 failureThreshold 次。 默认值为3。 最小值为1。 |
| `namespace` | `namespace` | 将 webservice 部署到的 Kubernetes 命名空间。 最多63小写字母数字 (' a-z ', ' 0 '-' 9 ') 和连字符 ('-') 字符。 第一个和最后一个字符不能是连字符。 |

以下 JSON 是用于 CLI 的示例部署配置:

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
