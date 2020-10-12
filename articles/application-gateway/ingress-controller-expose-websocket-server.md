---
title: 向应用程序网关公开 WebSocket 服务器
description: 本文介绍如何使用 AKS 群集的入口控制器向应用程序网关公开 WebSocket 服务器。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85207781"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>向应用程序网关公开 WebSocket 服务器

如应用程序网关 v2 文档中所述，它[为 WebSocket 和 HTTP/2 协议提供本机支持](features.md#websocket-and-http2-traffic)。 请注意，对于应用程序网关和 Kubernetes 入口，用户都无法通过配置设置来选择性地启用或禁用 WebSocket 支持。

下面的 Kubernetes 部署 YAML 显示了用于部署 WebSocket 服务器的最小配置，它与部署常规 Web 服务器相同：
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

由于所有先决条件都已满足，并且你有一个受 AKS 中的 Kubernetes 入口控制的应用程序网关，因此上面的部署会导致在应用程序网关的公共 IP 和 `ws.contoso.com` 域的端口 80 上公开 WebSockets 服务器。

以下 cURL 命令会测试 WebSocket 服务器部署：
```shell
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket 运行状况探测

如果部署未显式定义运行状况探测，应用程序网关会在 WebSocket 服务器终结点上尝试 HTTP GET。
根据服务器实现情况（[这是我们喜欢的](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)），可能需要特定于 WebSocket 的标头（例如 `Sec-Websocket-Version`）。
由于应用程序网关不添加 WebSocket 标头，因此应用程序网关的运行状况探测响应（来自 WebSocket 服务器）最可能是 `400 Bad Request`。
因此，应用程序网关会将你的 Pod 标记为运行不正常，这最终会导致 WebSocket 服务器的使用者收到 `502 Bad Gateway` 错误。
为了避免此情况，可能需要将运行状况检查的 HTTP GET 处理程序添加到服务器（例如 `/health`，会返回 `200 OK`）。
