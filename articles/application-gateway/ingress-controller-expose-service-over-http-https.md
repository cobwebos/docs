---
title: 使用应用程序网关通过 HTTP 或 HTTPS 公开 AKS 服务
description: 本文介绍如何使用应用程序网关通过 HTTP 或 HTTPS 公开 AKS 服务。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85850361"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>使用应用程序网关通过 HTTP 或 HTTPS 公开 AKS 服务 

这些教程有助于演示如何使用 [Kubernetes 入口资源](https://kubernetes.io/docs/concepts/services-networking/ingress/)，以便通过 [Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/)经 HTTP 或 HTTPS 公开示例 Kubernetes 服务。

## <a name="prerequisites"></a>先决条件

- 已安装 `ingress-azure` Helm Chart。
  - [ **“绿色地带”部署**](ingress-controller-install-new.md)：如果是从头开始，请参阅这些安装说明，大致了解如何通过应用程序网关部署 AKS 群集，以及如何在 AKS 群集上安装应用程序网关入口控制器。
  - [ **“棕色地带”部署**](ingress-controller-install-existing.md)：如果有现有的 AKS 群集和应用程序网关，请参阅这些说明，了解如何在 AKS 群集上安装应用程序网关入口控制器。
- 若要在此应用程序上使用 HTTPS，则需 x509 证书及其私钥。

## <a name="deploy-guestbook-application"></a>部署 `guestbook` 应用程序

guestbook 应用程序是一个规范的 Kubernetes 应用程序，由 Web UI 前端、后端和 Redis 数据库组成。 默认情况下，`guestbook` 在端口 `80` 上通过名为 `frontend` 的服务公开其应用程序。 在没有 Kubernetes 入口资源的情况下，该服务不能从 AKS 群集外部访问。 我们将使用此应用程序并设置入口资源，以便通过 HTTP 和 HTTPS 来访问此应用程序。

请按以下说明部署 guestbook 应用程序。

1. 从[此处](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)下载 `guestbook-all-in-one.yaml`
1. 通过运行以下命令将 `guestbook-all-in-one.yaml` 部署到 AKS 群集中

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

`guestbook` 应用程序现已部署。

## <a name="expose-services-over-http"></a>通过 HTTP 公开服务

我们将使用以下入口资源公开 guestbook 应用程序：

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

此入口会将 `guestbook-all-in-one` 部署的 `frontend` 服务作为应用程序网关的默认后端公开。

请将以上入口资源另存为 `ing-guestbook.yaml`。

1. 通过运行以下命令部署 `ing-guestbook.yaml`：

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. 检查入口控制器的日志以了解部署状态。

`guestbook` 应用程序现在应该可用。 可以通过访问应用程序网关的公共地址来检查它。

## <a name="expose-services-over-https"></a>通过 HTTPS 公开服务

### <a name="without-specified-hostname"></a>没有指定的主机名

在不指定主机名的情况下，guestbook 服务会在所有指向应用程序网关的主机名上可用。

1. 在部署入口之前，需创建 Kubernetes 机密来托管证书和私钥。 可通过运行以下命令来创建 Kubernetes 机密

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. 定义以下入口。 在入口的 `secretName` 节中指定机密的名称。

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
    > 请将以上入口资源中的 `<guestbook-secret-name>` 替换为你的机密的名称。 将以上入口资源存储在名为 `ing-guestbook-tls.yaml` 的文件中。

1. 通过运行以下命令部署 ing-guestbook-tls.yaml

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. 检查入口控制器的日志以了解部署状态。

现在，`guestbook` 应用程序会在 HTTP 和 HTTPS 上均可用。

### <a name="with-specified-hostname"></a>使用指定的主机名

也可在入口上指定主机名，以便将 TLS 配置和服务多路复用。
指定主机名后，guestbook 服务将只能在指定主机上使用。

1. 定义以下入口。
    在入口的 `secretName` 节中指定机密的名称，并对 `hosts` 节中的主机名进行相应的替换。

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

1. 通过运行以下命令部署 `ing-guestbook-tls-sni.yaml`

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. 检查入口控制器的日志以了解部署状态。

现在，`guestbook` 应用程序会在 HTTP 和 HTTPS 上均可用，但仅限于指定的主机（在此示例中为 `<guestbook.contoso.com>`）。

## <a name="integrate-with-other-services"></a>与其他服务集成

以下入口允许你将其他路径添加到此入口中，并将这些路径重定向到其他服务：

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
