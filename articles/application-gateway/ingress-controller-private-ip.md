---
title: 将专用 IP 地址用于入站终结点的内部路由
description: 本文介绍了如何使用专用 Ip 进行内部路由，从而将群集内的入口终结点公开给 VNet 的其余部分。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 13b8cc5c346febe9bd6e86be1ad8157464c83536
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513219"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>为入口终结点使用专用 IP 进行内部路由 

此功能允许使用专用 IP 在 `Virtual Network` 中公开入口终结点。

## <a name="pre-requisites"></a>先决条件  
具有[专用 IP 配置](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)的应用程序网关

可以通过两种方法将控制器配置为使用专用 IP 进行入口，

## <a name="assign-to-a-particular-ingress"></a>分配给特定入口
若要公开专用 IP 上的特定入口，请在入口中使用批注[`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) 。

### <a name="usage"></a>使用情况
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

对于没有专用 IP 的应用程序网关，将忽略使用 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 批注的恒温器。 这会在入口事件和 AGIC pod 日志中指明。

* 在入口事件中指示的错误

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* AGIC 日志中所示的错误

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>全局分配
在这种情况下，要求是限制通过专用 IP 公开的所有恒温器，使用 `helm` config 中的 `appgw.usePrivateIP: true`。

### <a name="usage"></a>使用情况
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

在应用程序网关上配置前端侦听器时，这会使入口控制器筛选专用 IP 的 IP 地址配置。
如果 `usePrivateIP: true` 未分配任何专用 IP，则 AGIC 将死机并崩溃。

> [!NOTE]
> 应用程序网关 v2 SKU 需要公共 IP。 如果要求应用程序网关是专用的，请将[`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview)附加到应用程序网关的子网，以限制流量。
