---
title: Azure API 管理服务的 IP 地址 |Microsoft Docs
description: 了解如何在 Azure API 管理服务发生更改时检索其 IP 地址。
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
ms.openlocfilehash: 64bd71d89446a19d2afe56a32b0c7124e897cb48
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072415"
---
# <a name="ip-addresses-of-azure-api-management"></a>Azure API 管理的 IP 地址

本文介绍如何检索 Azure API 管理服务的 IP 地址。 如果服务在虚拟网络中, 则 IP 地址可以是公用或专用。

您可以使用 IP 地址创建防火墙规则, 筛选出到后端服务的传入流量, 或限制出站流量。

## <a name="ip-addresses-of-api-management-service"></a>API 管理服务的 IP 地址

如果你的 API 管理服务是开发人员、基本、标准或高级层服务, 则可以在 Azure 门户的资源的 "概述" 仪表板中检索 IP 地址。

![API 管理 IP 地址](media/api-management-howto-ip-addresses/public-ip.png)

还可以通过以下 API 调用以编程方式提取它们:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

公共 IP 地址将是响应的一部分:

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

在[多区域部署](api-management-howto-deploy-multi-region.md)中, 每个区域部署都有一个公共 IP 地址。

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>VNET 中 API 管理服务的 IP 地址

如果 API 管理服务在虚拟网络中, 则会有两种类型的 IP 地址: 公用和专用。

公共 IP 地址用于端口`3443`上的内部通信, 用于管理配置 (例如, 通过 Azure 资源管理器)。 此外, 当请求从 API 管理发送到面向公众的 (面向 Internet) 后端时, 公共 IP 地址将显示为请求的源。

专用虚拟 IP (VIP) 地址用于从网络内部连接到 API 管理终结点-网关、开发人员门户和用于直接 API 访问的管理平面。 你可以使用它们来设置网络中的 DNS 记录。

你将在 Azure 门户和 API 调用的响应中看到两种类型的地址:

![VNET IP 地址中的 API 管理](media/api-management-howto-ip-addresses/vnet-ip.png)


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

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>消耗层 API 管理服务的 IP 地址

如果 API 管理服务是消耗层服务, 则没有专用 IP 地址。 消耗层服务在共享基础结构上运行, 没有确定性的 IP 地址。 

出于流量限制目的, 可以使用 Azure 数据中心的 IP 地址范围。 有关具体步骤, 请参阅[Azure Functions 文档文章](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)。

## <a name="changes-to-the-ip-addresses"></a>IP 地址的更改

在 API 管理的 "开发人员"、"基本"、"标准" 和 "高级" 层中, 公共 IP 地址 (VIP) 在服务生存期内是静态的, 但有以下例外:

* 服务被删除，然后重新创建。
* 服务订阅被[暂停](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)或[警告](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)（例如，由于未付款），然后恢复。
* Azure 虚拟网络将添加到服务或从服务中删除。

在[多区域部署](api-management-howto-deploy-multi-region.md)中, 如果某个区域已空出然后恢复, 则区域 IP 地址会发生更改。
