---
title: 使用应用程序网关通过 HTTP 或 HTTPS 公开 AKS 服务
description: 本文介绍如何使用应用程序网关通过 HTTP 或 HTTPS 公开 AKS 服务。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795569"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>使用应用程序网关通过 HTTP 或 HTTPS 公开 AKS 服务 

这些教程有助于说明[Kubernetes 入口资源](https://kubernetes.io/docs/concepts/services-networking/ingress/)的使用情况，以通过 HTTP 或 HTTPS 通过[Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/)公开示例 Kubernetes 服务。

## <a name="prerequisites"></a>先决条件

- 已 `ingress-azure` helm 图安装。
  - [**领域部署**](ingress-controller-install-new.md)：若要从头开始，请参阅这些安装说明，其中概述了使用应用程序网关部署 AKS 群集和在 AKS 群集上安装应用程序网关入口控制器的步骤。
  - [**要重建 mqtt 部署**](ingress-controller-install-existing.md)：如果你有现成的 AKS 群集和应用程序网关，请参阅这些说明，在 AKS 群集上安装应用程序网关入口控制器。
- 如果要在此应用程序上使用 HTTPS，则需要 x509 证书及其私钥。

## <a name="deploy-guestbook-application"></a>部署 `guestbook` 应用程序

留言簿应用程序是一个规范的 Kubernetes 应用程序，用于撰写 Web UI 前端、后端和 Redis 数据库。 默认情况下，`guestbook` 通过端口 `80`上具有名称 `frontend` 的服务公开其应用程序。 如果没有 Kubernetes 入口资源，则无法从 AKS 群集外部访问该服务。 我们将使用应用程序并设置入口资源，通过 HTTP 和 HTTPS 访问应用程序。

按照以下说明部署留言簿应用程序。

1. 从[此处](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)下载 `guestbook-all-in-one.yaml`
1. 通过运行将 `guestbook-all-in-one.yaml` 部署到 AKS 群集

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

现在，已部署 `guestbook` 应用程序。

## <a name="expose-services-over-http"></a>通过 HTTP 公开服务

为了公开留言簿应用程序，我们将使用以下入口资源：

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

这种入口会将 `guestbook-all-in-one` 部署的 `frontend` 服务作为应用程序网关的默认后端公开。

将上述入口资源保存为 `ing-guestbook.yaml`。

1. 通过运行以下内容来部署 `ing-guestbook.yaml`：

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. 检查入口控制器的日志以了解部署状态。

现在 `guestbook` 应用程序应该可用。 可以通过访问应用程序网关的公共地址来进行检查。

## <a name="expose-services-over-https"></a>通过 HTTPS 公开服务

### <a name="without-specified-hostname"></a>无指定主机名

如果不指定 hostname，留言簿服务将在指向应用程序网关的所有主机名上可用。

1. 在部署入口之前，需要创建 kubernetes 机密来托管证书和私钥。 可以通过运行来创建 kubernetes 机密

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. 定义以下入口。 在入口中，在 "`secretName`" 部分指定机密的名称。

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > 将上述入口资源中的 `<guestbook-secret-name>` 替换为机密的名称。 将以上入口资源存储在文件名 `ing-guestbook-tls.yaml`中。

1. 通过运行来部署 yaml

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. 检查入口控制器的日志以了解部署状态。

现在，可以在 HTTP 和 HTTPS 上使用 `guestbook` 应用程序。

### <a name="with-specified-hostname"></a>具有指定主机名

你还可以指定入口上的主机名，以便多路复用 TLS 配置和服务。
通过指定 hostname，留言簿服务将仅在指定的主机上可用。

1. 定义以下入口。
    在入口中，在 "`secretName`" 部分指定机密的名称，并相应地替换 `hosts` 部分中的主机名。

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. 通过运行部署 `ing-guestbook-tls-sni.yaml`

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. 检查入口控制器的日志以了解部署状态。

现在，只能在指定的主机上的 HTTP 和 HTTPS 上使用 `guestbook` 应用程序（在本示例中为`<guestbook.contoso.com>`）。

## <a name="integrate-with-other-services"></a>与其他服务集成

下面的入口允许您向此入口添加其他路径，并将这些路径重定向到其他服务：

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
