---
title: 使用入口终结点的专用 IP 地址进行内部路由
description: 本文介绍如何使用专用 IP 进行内部路由，以便将群集中的入口终结点公开给 VNet 的其余部分。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795487"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>使用入口终结点的专用 IP 进行内部路由 

此功能允许使用专用 IP 在 `Virtual Network` 中公开入口终结点。

## <a name="pre-requisites"></a>先决条件  
使用[专用 IP 配置](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)的应用程序网关

可通过两种方法配置控制器以使用入口专用 IP

## <a name="assign-to-a-particular-ingress"></a>分配到特定入口
要公开专用 IP 上的特定入口，请使用入口[`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip)中的注释。

### <a name="usage"></a>使用情况
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

对于没有专用 IP 的应用程序网关，将忽略使用 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 进行注释的入口。 这会在入口事件和 AGIC Pod 日志中指示。

* 错误，如入口事件中指示的那样

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* 错误，如 AGIC 日志中指示的那样

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>全局分配
如果要求是限制所有入口通过专用 IP 进行公开，请在 `helm` 配置中使用 `appgw.usePrivateIP: true`。

### <a name="usage"></a>使用情况
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

在应用程序网关上配置前端侦听器时，这会使得入口控制器筛选专用 IP 的 IP 地址配置。
如果 `usePrivateIP: true` 且没有分配任何专用 IP，则 AGIC 会死机并崩溃。

> [!NOTE]
> 应用程序网关 v2 SKU 要求公共 IP。 如果要求应用程序网关为私有，请将 附加到[`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview)应用程序网关的子网以限制流量。
