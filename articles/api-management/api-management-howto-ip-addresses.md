---
title: Azure API 管理服务的 IP 地址 | Microsoft Docs
description: 了解如何检索 Azure API 管理服务的 IP 地址，及其何时发生变化。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80047742"
---
# <a name="ip-addresses-of-azure-api-management"></a>Azure API 管理的 IP 地址

本文介绍如何检索 Azure API 管理服务的 IP 地址。 如果该服务位于虚拟网络中，IP 地址可以是公共或专用地址。

可以使用 IP 地址来创建防火墙规则、筛选传入后端服务的流量，或者限制出站流量。

## <a name="ip-addresses-of-api-management-service"></a>API 管理服务的 IP 地址

“开发人员”、“基本”、“标准”或“高级”层中的每个 API 管理服务实例具有公共 IP 地址，这些地址专供该服务实例使用（不与其他资源共享）。 

可以通过 Azure 门户中资源的概述仪表板检索 IP 地址。

![API 管理 IP 地址](media/api-management-howto-ip-addresses/public-ip.png)

也可以使用以下 API 调用以编程方式提取 IP 地址：

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

公共 IP 地址将包含在响应中：

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

在[多区域部署](api-management-howto-deploy-multi-region.md)中，每个区域部署有一个公共 IP 地址。

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>VNet 中 API 管理服务的 IP 地址

如果 API 管理服务位于虚拟网络中，则有两种类型的 IP 地址：公共和专用。

公共 IP 地址用于端口 `3443` 上的内部通信 - 用于管理配置（例如，通过 Azure 资源管理器）。 在外部 VNet 配置中，它们也用于运行时 API 流量。 将请求从 API 管理发送到面向公众（面向 Internet）的后端时，公共 IP 地址将显示为请求来源。

专用虚拟 IP (VIP) 地址 **仅** 在 [内部 VNet 模式下](api-management-using-with-internal-vnet.md)提供，用于从网络内部连接到 API 管理终结点-网关、开发人员门户和用于直接 API 访问的管理平面。 可以使用 VIP 在网络内部设置 DNS 记录。

Azure 门户和 API 调用响应中会显示两种类型的地址：

![VNet IP 地址中的 API 管理](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API 管理对 VPN 外部的连接使用公共 IP 地址，对 VPN 内的连接使用专用 IP 地址。

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>“消耗”层 API 管理服务的 IP 地址

如果 API 管理服务是“消耗”层服务，则它没有专用 IP 地址。 “消耗”层服务在共享的基础结构上运行，没有确定性的 IP 地址。 

出于流量限制目的，可以使用 Azure 数据中心的 IP 地址范围。 有关具体步骤，请参阅 [Azure Functions 文档](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)。

## <a name="changes-to-the-ip-addresses"></a>对 IP 地址的更改

在 API 管理的“开发人员”、“基本”、“标准”和“高级”层中，公共 IP 地址 (VIP) 在服务的整个生存期内是静态的，但存在以下例外情况：

* 服务被删除，然后重新创建。
* 服务订阅被[暂停](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)或[警告](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)（例如，由于未付款），然后恢复。
* 在该服务中添加或删除 Azure 虚拟网络。
* API 管理服务在外部和内部 VNet 部署模式间切换。

在[多区域部署](api-management-howto-deploy-multi-region.md)中，如果某个区域搬迁然后重建，则区域 IP 地址将发生变化。
