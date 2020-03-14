---
title: 向应用程序网关公开 WebSocket 服务器
description: 本文介绍如何使用 AKS 群集的入口控制器向应用程序网关公开 WebSocket 服务器。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297826"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>向应用程序网关公开 WebSocket 服务器

如应用程序网关 v2 文档中所述，它为[WebSocket 和 HTTP/2 协议提供本机支持](features.md#websocket-and-http2-traffic)。 请注意，对于应用程序网关和 Kubernetes 入口，无用户可配置的设置来选择性地启用或禁用 WebSocket 支持。

下面的 Kubernetes 部署 YAML 显示了用于部署 WebSocket 服务器的最低配置，这与部署常规 web 服务器相同：
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

假设已满足所有先决条件，并且你的应用程序网关由 AKS 中的 Kubernetes 入口控制，以上部署会导致在应用程序网关的公共 IP 和 `ws.contoso.com` 域的端口80上公开 Websocket 服务器。

以下卷命令将测试 WebSocket 服务器部署：
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket 运行状况探测

如果你的部署未显式定义运行状况探测，应用程序网关会在你的 WebSocket 服务器终结点上尝试 HTTP GET。
可能需要 WebSocket 特定标头（例如`Sec-Websocket-Version`），具体取决于服务器实现（[这是我们喜欢的一个](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)）。
由于应用程序网关不会添加 WebSocket 标头，因此，来自 WebSocket 服务器的应用程序网关的运行状况探测响应很有可能会被 `400 Bad Request`。
因此，应用程序网关会将盒标记为不正常，最终将导致 WebSocket 服务器的使用者 `502 Bad Gateway`。
若要避免出现这种情况，可能需要将用于运行状况检查的 HTTP GET 处理程序添加到服务器（`/health` 例如返回 `200 OK`）。
