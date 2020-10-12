---
title: 为 Azure API 管理自承载网关配置云指标和日志 | Microsoft Docs
description: 了解如何为 Azure API 管理自承载网关配置云指标和日志
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
ms.openlocfilehash: 3bbfd167e89ae1b5f9b7de1df5fd1cb72c720cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254518"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>为 Azure API 管理自承载网关配置云指标和日志

本文详细介绍了如何为[自承载网关](./self-hosted-gateway-overview.md)配置云指标和日志。

自承载网关必须与 API 管理服务关联，并要求在端口 443 上与 Azure 建立出站 TCP/IP 连接。 该网关利用出站连接将遥测发送到 Azure（如果配置为这样做）。 

## <a name="metrics"></a>指标
默认情况下，自承载网关通过 [Azure Monitor](https://azure.microsoft.com/services/monitor/) 发出大量指标，与[云中](api-management-howto-use-azure-monitor.md)的托管网关相同。 

可使用网关部署的 ConfigMap 中的 `telemetry.metrics.cloud` 键启用或禁用该功能。 以下是可用配置的详细信息：

| 字段  | 默认 | 说明 |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | 通过 Azure Monitor 启用日志记录。 值可以是 `true`、`false`。 |


下面是示例配置：

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

自承载网关当前通过 Azure Monitor 发出以下指标：

| 指标  | 说明 |
| ------------- | ------------- |
| 请求  | 期间内的 API 请求数 |
| 网关请求的持续时间 | 从网关收到请求到响应全部发送出去经过的时间（毫秒） |
| 后端请求持续时间 | 花在整个后端 IO（连接、发送和接收字节）上的时间（毫秒）  |

## <a name="logs"></a>日志

自承载网关当前不向云发送[诊断日志](./api-management-howto-use-azure-monitor.md#activity-logs)。 但是，可以在部署自承载网关[本地配置和保存日志](how-to-configure-local-metrics-logs.md)。 

如果网关部署在 [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)中，则可以启用[适用于容器的 Azure Monitor](../azure-monitor/insights/container-insights-overview.md)，从容器收集日志并查看 Log Analytics 中的日志。 


## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)
* 了解[在本地配置和保存日志](how-to-configure-local-metrics-logs.md)
