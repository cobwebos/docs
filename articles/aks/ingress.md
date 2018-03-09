---
title: "在 Azure 容器服务 (AKS) 群集中配置入口"
description: "在 Azure 容器服务 (AKS) 群集中安装和配置 NGINX 入口控制器。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c25a0171bd412050a7c94e9b077436cd1ebe893b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="https-ingress-on-azure-container-service-aks"></a>Azure 容器服务 (AKS) 中的 HTTPS 入口

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个外部地址中将流量路由到 Kubernetes 群集中的多个服务。

本文档通过一个示例演示如何在 Azure 容器服务 (AKS) 群集中部署 [NGINX 入口控制器][nginx-ingress]。 此外，使用 [KUBE-LEGO][kube-lego] 项目自动生成并配置 [Let's Encrypt][lets-encrypt] 证书。 最后，在 AKS 群集中运行多个应用程序（可通过单个地址访问其中的每个应用程序）。

## <a name="install-an-ingress-controller"></a>安装入口控制器

使用 Helm 安装 NGINX 入口控制器。 有关详细的部署信息，请参阅 NGINX 入口控制器的[文档][nginx-ingress]。 

```
helm install stable/nginx-ingress
```

在安装过程中，将为入口控制器创建一个 Azure 公共 IP 地址。 若要获取公共 IP 地址，请使用 kubectl get service 命令。 将 IP 地址分配到服务可能需要一段时间。

```console
$ kubectl get service

NAME                                          TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
kubernetes                                    ClusterIP      10.0.0.1       <none>           443/TCP                      3d
toned-spaniel-nginx-ingress-controller        LoadBalancer   10.0.236.223   52.224.125.195   80:30927/TCP,443:31144/TCP   18m
toned-spaniel-nginx-ingress-default-backend   ClusterIP      10.0.5.86      <none>           80/TCP                       18m
```

由于尚未创建入口规则，在浏览到公共 IP 地址时，会路由到 NGINX 入口控制器的默认 404 页。

![默认的 NGINX 后端](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>配置 DNS 名称

由于使用了 HTTPS 证书，因此需要配置入口控制器 IP 地址的 FQDN 名称。 在此示例中，Azure FQDN 是使用 Azure CLI 创建的。 请使用入口控制器的 IP 地址以及要在 FQDN 中使用的名称更新脚本。

```
#!/bin/bash

# Public IP address
IP="52.224.125.195"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get resource group and public ip name
RESOURCEGROUP=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[resourceGroup]" --output tsv)
PIPNAME=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[name]" --output tsv)

# Update public ip address with dns name
az network public-ip update --resource-group $RESOURCEGROUP --name  $PIPNAME --dns-name $DNSNAME
```

根据需要运行以下命令来检索 FQDN。 将 IP 地址值更新为入口控制器的 IP 地址。

```azurecli
az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '52.224.125.195')].[dnsSettings.fqdn]" --output tsv
```

现在，可以通过 FQDN 访问入口控制器。

## <a name="install-kube-lego"></a>安装 KUBE-LEGO

NGINX 入口控制器支持 TLS 终止。 尽管可以通过多种方式检索和配置 HTTPS 的证书，但本文档只演示如何使用提供自动 [Lets Encrypt][lets-encrypt] 证书生成和管理功能的 [KUBE-LEGO][kube-lego]。

若要安装 KUBE-LEGO 控制器，请使用以下 Helm install 命令。 将电子邮件地址更新为组织中的某个电子邮件地址。

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=user@contoso.com \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory
```

有关 KUBE-LEGO 配置的详细信息，请参阅 [KUBE-LEGO 文档][kube-lego]。

## <a name="run-application"></a>运行应用程序

此时，已配置入口控制器和证书管理解决方案。 现在，请在 AKS 群集中运行几个应用程序。

此示例使用 Helm 来运行一个简单 hello world 应用程序的多个实例。

运行该应用程序之前，请在开发系统中添加 Azure 示例 Helm 存储库。

```
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

 使用以下命令运行 AKS hello world 图表：

```
helm install azure-samples/aks-helloworld
```

现在安装 hello world 应用程序的第二个实例。

对于第二个实例，请指定新的标题，使两个应用程序在视觉上不同。 还需要指定唯一的服务名称。 可以使用以下命令查看这些配置。

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>创建入口路由

两个应用程序现在都在 Kubernetes 群集中运行，但其中配置了服务类型 `ClusterIP`。 因此，无法通过 Internet 访问它们。 若要通过 Internet 访问，请创建 Kubernetes 入口资源。 该入口资源配置将流量路由到这两个应用程序之一的规则。

创建名为 `hello-world-ingress.yaml` 的文件，并将其复制到以下 YAML 中。

注意传往地址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/` 的流量将路由到名为 `aks-helloworld` 的服务。 传往地址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` 的流量将路由到 `ingress-demo` 服务。

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/tls-acme: "true"
    ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

使用 `kubectl apply` 命令创建入口资源。

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>测试入口配置

浏览到 Kubernetes 入口控制器的 FQDN，应会看到该 hello world 应用程序。

![应用程序示例 1](media/ingress/app-one.png)

现在，使用 `/hello-world-two` 路径浏览到入口控制器的 FQDN，应会看到显示自定义标题的 hello world 应用程序。

![应用程序示例 2](media/ingress/app-two.png)

另请注意连接已加密，并且使用了 Let's Encrypt 颁发的证书。

![Lets Encrypt 证书](media/ingress/certificate.png)

## <a name="next-steps"></a>后续步骤

详细了解本文档中演示的软件。 

- [NGINX 入口控制器][nginx-ingress]
- [KUBE-LEGO][kube-lego]

<!-- LINKS - external -->
[kube-lego]: https://github.com/jetstack/kube-lego
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
