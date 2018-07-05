---
title: 在 Azure Kubernetes 服务 (AKS) 群集中配置入口
description: 在 Azure Kubernetes 服务 (AKS) 群集中安装和配置 NGINX 入口控制器。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bee80ebbaf0568706428d673d584819b1daa143
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096982"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的 HTTPS 入口

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个外部地址中将流量路由到 Kubernetes 群集中的多个服务。

本文档通过一个示例演示如何在 Azure Kubernetes 服务 (AKS) 群集中部署 [NGINX 入口控制器][nginx-ingress]。 此外，[证书管理器][cert-manager]项目用于自动生成和配置 [Let's Encrypt][lets-encrypt] 证书。 最后，在 AKS 群集中运行多个应用程序（可通过单个地址访问其中的每个应用程序）。

## <a name="install-an-ingress-controller"></a>安装入口控制器

使用 Helm 安装 NGINX 入口控制器。 有关详细的部署信息，请参阅 NGINX 入口控制器的[文档][nginx-ingress]。

本示例将控制器安装在 `kube-system` 命名空间中，可将其修改为所需的命名空间。 如果 AKS 群集未启用 RBAC，请将 `--set rbac.create=false` 添加到命令中。 有关详细信息，请参阅 [nginx 流入量图表][nginx-ingress]。

```bash
helm install stable/nginx-ingress --namespace kube-system
```

在安装过程中，将为入口控制器创建一个 Azure 公共 IP 地址。 若要获取公共 IP 地址，请使用 kubectl get service 命令。 将 IP 地址分配到服务可能需要一段时间。

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

由于尚未创建入口规则，在浏览到公共 IP 地址时，会路由到 NGINX 入口控制器的默认 404 页。

![默认的 NGINX 后端](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>配置 DNS 名称

由于使用了 HTTPS 证书，因此需要配置入口控制器 IP 地址的 FQDN 名称。 在此示例中，Azure FQDN 是使用 Azure CLI 创建的。 请使用入口控制器的 IP 地址以及要在 FQDN 中使用的名称更新脚本。

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

现在应该可以通过 FQDN 访问入口控制器了。

## <a name="install-cert-manager"></a>安装证书管理器

NGINX 入口控制器支持 TLS 终止。 尽管可以通过多种方式来检索和配置 HTTPS 证书，但本文档介绍的是如何使用[证书管理器][cert-manager]，它能自动生成和管理 [Lets Encrypt][lets-encrypt] 证书。

若要安装证书管理器控制器，请运行以下 Helm install 命令。

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

如果群集未启用 RBAC，请运行以下命令。

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

若要详细了解证书管理器配置，请参阅[证书管理器项目][cert-manager]。

## <a name="create-ca-cluster-issuer"></a>创建 CA 群集证书颁发者

证书管理器必须有 [Issuer][cert-manager-issuer] 或 [ClusterIssuer][cert-manager-cluster-issuer] 资源，才能颁发证书。 这两种资源的功能完全相同，区别在于 `Issuer` 适用于单一命名空间，而 `ClusterIssuer` 适用于所有命名空间。 有关详细信息，请参阅[证书管理器证书颁发者][cert-manager-issuer]文档。

使用以下清单创建群集证书颁发者。 将电子邮件地址更新为组织提供的有效地址。

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>创建证书对象

接下来，必须创建证书资源。 证书资源定义了必需的 X.509 证书。 有关详细信息，请参阅[证书管理器证书][cert-manager-certificates]。

使用以下清单创建证书资源。

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>运行应用程序

此时，已配置入口控制器和证书管理解决方案。 现在，请在 AKS 群集中运行几个应用程序。

此示例使用 Helm 来运行一个简单 hello world 应用程序的多个实例。

运行该应用程序之前，请在开发系统中添加 Azure 示例 Helm 存储库。

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

使用以下命令运行 AKS hello world 图表：

```bash
helm install azure-samples/aks-helloworld
```

现在安装 hello world 应用程序的第二个实例。

对于第二个实例，请指定新的标题，使两个应用程序在视觉上不同。 还需要指定唯一的服务名称。 可以使用以下命令查看这些配置。

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>创建入口路由

两个应用程序现在都在 Kubernetes 群集中运行，但其中配置了服务类型 `ClusterIP`。 因此，无法通过 Internet 访问它们。 若要通过 Internet 访问，请创建 Kubernetes 入口资源。 该入口资源配置将流量路由到这两个应用程序之一的规则。

创建名为 `hello-world-ingress.yaml` 的文件，并将其复制到以下 YAML 中。

注意传往地址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/` 的流量将路由到名为 `aks-helloworld` 的服务。 传往地址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` 的流量将路由到 `ingress-demo` 服务。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rewrite-target: /
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

- [Helm CLI][helm-cli]
- [NGINX 入口控制器][nginx-ingress]
- [证书管理器][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
