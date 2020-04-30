---
title: 为 Azure API 管理的自承载网关配置云指标和日志 |Microsoft Docs
description: 了解如何配置 Azure API 管理自承载网关的云指标和日志
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: d0fbdcb877e91a703306f15fdc7507fd19d534f4
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205126"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>为 Azure API 管理的自承载网关配置云指标和日志

本文提供了有关为[自承载网关](./self-hosted-gateway-overview.md)配置云指标和日志的详细信息。

自承载的网关必须与 API 管理服务相关联，并且需要端口443上到 Azure 的出站 TCP/IP 连接。 网关利用出站连接将遥测发送到 Azure （如果配置为这样做）。 

## <a name="metrics"></a>指标
默认情况下，自承载网关通过[Azure Monitor](https://azure.microsoft.com/services/monitor/)（与[云中](api-management-howto-use-azure-monitor.md)托管的网关）发出大量指标。 

使用网关部署的 ConfigMap 中的`telemetry.metrics.cloud`密钥可以启用或禁用此功能。 下面是可用配置的细目：

| 字段  | 默认 | 说明 |
| ------------- | ------------- | ------------- |
| 遥测. cloud  | `true` | 通过 Azure Monitor 启用日志记录。 值可以为`true`、 `false`。 |


下面是一个示例配置：

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

自承载的网关当前通过 Azure Monitor 发出以下度量值：

| 指标  | 说明 |
| ------------- | ------------- |
| Requests  | 期间内的 API 请求数 |
| 网关请求的持续时间 | 从网关收到请求到响应全部发送出去经过的时间（毫秒） |
| 后端请求的持续时间 | 花在整个后端 IO（连接、发送和接收字节）上的时间（毫秒）  |

## <a name="logs"></a>日志

自承载的网关当前不会将[诊断日志](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)发送到云。 但是，可以在[本地配置和保存](how-to-configure-local-metrics-logs.md)在其中部署了自承载网关的日志。 

如果网关部署在[Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)中，则可以[为容器启用 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)以便从容器中收集日志并在 Log Analytics 中查看。 


## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)
* 了解如何在[本地配置和保存日志](how-to-configure-local-metrics-logs.md)


