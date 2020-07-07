---
title: 应用程序网关入口控制器批注
description: 本文提供有关特定于应用程序网关入口控制器的批注的文字说明。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335827"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>应用程序网关入口控制器的批注 

## <a name="introductions"></a>简介

可以用任意键/值对来批注 Kubernetes 入口资源。 AGIC 依赖使用批注来对应用程序网关功能进行编程，而无法通过入口 YAML 配置这些功能。 入口批注适用于派生自入口资源的所有 HTTP 设置、后端池和侦听器。

## <a name="list-of-supported-annotations"></a>支持的批注列表

对于 AGIC 要观察的入口资源，**必须**使用 `kubernetes.io/ingress.class: azure/application-gateway` 对其进行批注。 只有在批注之后，AGIC 才能使用相关的入口资源。

| 批注键 | 值类型 | 默认值 | 允许的值
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`（秒） | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`（秒） | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>后端路径前缀

此批注允许使用其中指定的前缀重新编写入口资源中指定的后端路径。 这样，用户就可以公开终结点与用于公开入口资源中服务的终结点名称不同的服务。

### <a name="usage"></a>使用情况

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>示例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
在以上示例中，我们使用批注 `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` 定义了名为 `go-server-ingress-bkprefix` 的入口资源。 该批注告知应用程序网关创建一个 HTTP 设置，该设置包含路径 `/hello` 到 `/test/` 的路径前缀重写。

> [!NOTE] 
> 在以上示例中，我们只定义了一个规则。 但是，批注适用于整个入口资源，因此，如果用户定义了多个规则，则会为指定的每个路径设置后端路径前缀。 因此，如果用户想要创建包含不同路径前缀的不同规则（即使是对于同一服务），需要定义不同的入口资源。

## <a name="tls-redirect"></a>TLS 重定向

可将应用程序网关[配置](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview)为向其 HTTPS 对等方自动重定向 HTTP URL。 如果此批注存在且已正确配置 TLS，Kubernetes 入口控制器将创建[采用某种重定向配置的路由规则](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration)，并将更改应用到应用程序网关。 创建的重定向是 HTTP `301 Moved Permanently`。

### <a name="usage"></a>使用情况

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>示例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>连接清空

`connection-draining`：此批注可让用户指定是否启用连接清空。
`connection-draining-timeout`：此批注可让用户指定超时，在此超时过后，应用程序网关将终止对清空后端终结点的请求。

### <a name="usage"></a>使用情况

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>示例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>基于 Cookie 的相关性

此批注可用于指定是否启用基于 Cookie 的相关性。

### <a name="usage"></a>使用情况

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>示例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>请求超时

此批注可用于指定请求超时（以秒为单位），在此超时过后，如果未收到响应，应用程序网关将使请求失败。

### <a name="usage"></a>使用情况

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>示例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>使用专用 IP

此批注可用于指定是否在应用程序网关的专用 IP 上公开此终结点。

> [!NOTE]
> * 应用程序网关不支持同一端口上的多个 IP（例如：80/443）。 在 `HTTP` 上使用带有批注 `appgw.ingress.kubernetes.io/use-private-ip: "false"` 的入口和带有 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 的另一个入口会导致 AGIC 在更新应用程序网关时失败。
> * 对于没有专用 IP 的应用程序网关，将忽略带有 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 的入口。 这些入口的控制器日志和入口事件中会通过 `NoPrivateIP` 警告反映这种情况。


### <a name="usage"></a>使用情况
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>示例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>后端协议

使用此注释可以指定应用程序网关在与 Pod 通信时应使用的协议。 支持的协议：`http`、`https`

> [!NOTE]
> * 应用程序网关支持自签名的证书，不过，目前仅当 Pod 使用已知 CA 签名的证书时，AGIC 才支持 `https`。
> * 请务必不要在 Pod 上将端口 80 与 HTTPS 配合使用，或者将端口 443 与 HTTP 配合使用。

### <a name="usage"></a>使用情况
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>示例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```