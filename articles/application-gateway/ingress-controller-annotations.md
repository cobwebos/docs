---
title: 应用程序网关入口控制器批注
description: 本文提供了特定于应用程序网关入口控制器的批注的相关文档。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a3583a5efd120733ce7f6b71a7594b5636593f99
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373493"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>应用程序网关入口控制器的批注 

## <a name="introductions"></a>介绍

可以用任意键/值对来批注 Kubernetes 入口资源。 AGIC 依赖于应用程序网关功能的批注，这些功能无法通过入口 YAML 进行配置。 入口注释适用于所有 HTTP 设置、后端池和派生自入口资源的侦听器。

## <a name="list-of-supported-annotations"></a>支持的批注列表

对于要由 AGIC 观察的入口资源，必须使用 `kubernetes.io/ingress.class: azure/application-gateway`对其进行**批注**。 只有 AGIC 将与相关的入口资源一起工作。

| 批注键 | 值类型 | 默认值 | 允许的值
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#ssl-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` （秒） | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` （秒） | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`、`https` |

## <a name="backend-path-prefix"></a>后端路径前缀

此批注允许用此注释中指定的前缀重写入口资源中指定的后端路径。 这样，用户就可以公开其终结点与用于公开入口资源中服务的终结点名称不同的服务。

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
在上面的示例中，我们使用批注 `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` 定义了一个名为 `go-server-ingress-bkprefix` 的入口资源。 批注会告诉应用程序网关创建一个 HTTP 设置，该设置会将路径前缀替代为路径 `/hello` `/test/`。

> [!NOTE] 
> 在上面的示例中，我们只定义了一个规则。 但是，批注适用于整个进入资源，因此，如果用户定义了多个规则，则会为指定的每个路径设置后端路径前缀。 因此，如果用户需要不同路径前缀的不同规则（即使是在同一服务中），则需要定义不同的入口资源。

## <a name="ssl-redirect"></a>SSL 重定向

[可将](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview)应用程序网关配置为将 HTTP url 自动重定向到其 HTTPS 对应项。 如果此批注存在并且已正确配置 TLS，Kubernetes 入口控制器将[使用重定向配置创建路由规则](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration)，并将更改应用到应用程序网关。 创建的重定向将是 HTTP `301 Moved Permanently`。

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

## <a name="connection-draining"></a>连接排出

`connection-draining`：此批注允许用户指定是否启用连接排出。
`connection-draining-timeout`：此批注允许用户指定超时后，应用程序网关将终止请求排出后端终结点。

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

此批注允许指定是否启用基于 cookie 的相关性。

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

此批注允许指定请求超时（以秒为单位），在此时间之后，如果未收到响应，应用程序网关将导致请求失败。

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

此批注允许我们指定是否在应用程序网关的专用 IP 上公开此终结点。

> [!NOTE]
> * 应用程序网关不支持同一端口上的多个 Ip （例如：80/443）。 带批注的入口 `appgw.ingress.kubernetes.io/use-private-ip: "false"`，另一个在 `HTTP` 上 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 会导致 AGIC 在更新应用程序网关时失败。
> * 对于没有专用 IP 的应用程序网关，将忽略带有 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 的恒温器。 这会在控制器日志和这些恒温器的入口事件中反映 `NoPrivateIP` 警告。


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

使用此注释，我们可以指定应用程序网关在与盒通信时应使用的协议。 支持的协议： `http`、`https`

> [!NOTE]
> * 当应用程序网关上支持自签名证书时，AGIC 仅在 pod 使用由知名 CA 签名的证书时才支持 `https`。
> * 请确保在 pod 上不将端口80与 HTTPS 和端口443一起使用。

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