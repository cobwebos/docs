---
title: 在 Azure Kubernetes 服务 (AKS) 群集中创建 HTTPS 入口
description: 了解如何安装和配置 NGINX 入口控制器，该控制器使用 Let's Encrypt 在 Azure Kubernetes 服务 (AKS) 群集中自动生成 SSL 证书。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 7a7acb7da2e5999fc844fe6c6c2e191783df9190
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093949"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中创建 HTTPS 入口控制器

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。

本文介绍如何在 Azure Kubernetes 服务 (AKS) 群集中部署 [NGINX 入口控制器][nginx-ingress]。 [cert-manager][cert-manager] 项目用于自动生成和配置 [Let's Encrypt][lets-encrypt] 证书。 最后，在 AKS 群集中运行两个应用程序（可通过单个 IP 地址访问其中的每个应用程序）。

还可以：

- [使用外部网络连接创建基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- [创建使用你自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- [创建一个使用 Let's Encrypt 的入口控制器，以自动生成具有静态公共 IP 地址的 TLS 证书][aks-ingress-static-tls]

## <a name="before-you-begin"></a>开始之前

本文使用 Helm 安装 NGINX 入口控制器、cert-manager 和示例 Web 应用。 你需要在 AKS 群集中初始化 Helm 并使用 Tiller 服务帐户。 请确保使用 Helm 的最新版本。 有关升级说明，请参阅 [Helm 安装文档][helm-install]。有关配置和使用 Helm 的详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中使用 Helm 安装应用程序][use-helm]。

本文还要求运行 Azure CLI 2.0.41 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>创建入口控制器

若要创建入口控制器，请使用 `Helm` 来安装 *nginx-ingress*。 对于增加的冗余，NGINX 入口控制器的两个副本会在部署时具备 `--set controller.replicaCount` 参数。 若要充分利用正在运行的入口控制器副本，请确保 AKS 群集中有多个节点。

> [!TIP]
> 以下示例将入口控制器安装在 `kube-system` 命名空间中。 如果需要，可以为自己的环境指定不同的命名空间。 如果 AKS 群集未启用 RBAC，请将 `--set rbac.create=false` 添加到命令中。

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

在安装过程中，将为入口控制器创建一个 Azure 公共 IP 地址。 此公共 IP 地址在入口控制器的寿命期内是静态的。 如果你删除入口控制器，则公共 IP 地址分配会丢失。 如果你然后创建了另外的入口控制器，则会分配新的公共 IP 地址。 如果希望保持使用此公共 IP 地址，则可以改为[创建具有静态公共 IP 地址的入口控制器][aks-ingress-static-tls]。

若要获取公共 IP 地址，请使用 `kubectl get service` 命令。 将 IP 地址分配给服务需要几分钟时间。

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

尚未创建入口规则。 如果浏览到公共 IP 地址，将显示 NGINX 入口控制器的默认 404 页面。

## <a name="configure-a-dns-name"></a>配置 DNS 名称

若要使 HTTPS 证书正常工作，请为入口控制器 IP 地址配置 FQDN。 使用入口控制器的 IP 地址以及要用于 FQDN 的唯一名称更新以下脚本：

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

现在，可以通过 FQDN 访问入口控制器。

## <a name="install-cert-manager"></a>安装证书管理器

NGINX 入口控制器支持 TLS 终止。 可通过多种方法为 HTTPS 检索和配置证书。 本文演示如何使用 [cert-manager][cert-manager]，该项目提供自动 [Lets Encrypt][lets-encrypt] 证书生成和管理功能。

> [!NOTE]
> 本文为 Let's Encrypt 使用 `staging` 环境。 在生产部署中，请在安装 Helm 图表时使用资源定义中的 `letsencrypt-prod` 和 `https://acme-v02.api.letsencrypt.org/directory`。

若要在启用了 RBAC 的群集中安装 cert-manager 控制器，请使用以下 `helm install` 命令：

```console
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer
```

如果群集未启用 RBAC，请改用以下命令：

```console
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer \
    --set rbac.create=false \
    --set serviceAccount.create=false
```

若要详细了解证书管理器配置，请参阅[证书管理器项目][cert-manager]。

## <a name="create-a-ca-cluster-issuer"></a>创建 CA 群集证书颁发者

证书管理器必须有 [Issuer][cert-manager-issuer] 或 [ClusterIssuer][cert-manager-cluster-issuer] 资源，才能颁发证书。 这两种 Kubernetes 资源的功能完全相同，区别在于 `Issuer` 适用于单一命名空间，而 `ClusterIssuer` 适用于所有命名空间。 有关详细信息，请参阅[证书管理器证书颁发者][cert-manager-issuer]文档。

使用以下示例清单创建群集证书颁发者，例如 `cluster-issuer.yaml`。 将电子邮件地址更新为组织提供的有效地址：

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

若要创建证书颁发者，请使用 `kubectl apply -f cluster-issuer.yaml` 命令。

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>创建证书对象

接下来，必须创建证书资源。 证书资源定义了必需的 X.509 证书。 有关详细信息，请参阅 [cert-manager 证书][cert-manager-certificates]。

使用以下示例清单创建证书资源，例如 `certificates.yaml`。 将 *dnsNames* 和 *domains* 更新为在前面步骤中创建的 DNS 名称。 如果使用仅限内部使用的入口控制器，请指定服务的内部 DNS 名称。

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
    name: letsencrypt-staging
    kind: ClusterIssuer
```

若要创建证书资源，请使用 `kubectl apply -f certificates.yaml` 命令。

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>运行演示应用程序

入口控制器和证书管理解决方案已配置完毕。 现在让我们在你的 AKS 群集中运行两个演示应用程序。 此示例使用 Helm 来部署一个简单“Hello world”应用程序的两个实例。

需按下面所示将 Azure 示例存储库添加到 Helm 环境中，才能安装示例 Helm 图表：

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

使用以下命令根据 Helm 图表创建第一个演示应用程序：

```console
helm install azure-samples/aks-helloworld
```

现在安装演示应用程序的第二个实例。 对于第二个实例，请指定新的标题，使两个应用程序在视觉上不同。 还需要指定唯一的服务名称：

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>创建入口路由

两个应用程序现在都在 Kubernetes 群集中运行，但它们配置了服务类型 `ClusterIP`。 因此，无法通过 Internet 访问它们。 若要公开发布这两个应用程序，请创建 Kubernetes 入口资源。 该入口资源配置将流量路由到这两个应用程序之一的规则。

在以下示例中，传往地址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/` 的流量将路由到名为 `aks-helloworld` 的服务。 传往地址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` 的流量将路由到 `ingress-demo` 服务。 将 *hosts* 和 *host* 更新为在前面步骤中创建的 DNS 名称。

创建名为 `hello-world-ingress.yaml` 的文件，并将其复制到以下示例 YAML 中。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
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

使用 `kubectl apply -f hello-world-ingress.yaml` 命令创建入口资源。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>测试入口配置

打开 Web 浏览器，访问 Kubernetes 入口控制器的 FQDN，例如 *https://demo-aks-ingress.eastus.cloudapp.azure.com*。

由于这些示例使用 `letsencrypt-staging`，因此浏览器不信任颁发的 SSL 证书。 接受警告提示以继续运行应用程序。 证书信息显示这个*伪 LE 中间 X1* 证书是由 Let's Encrypt 颁发的。 此伪证书指出 `cert-manager` 正确处理了请求并接收了提供程序提供的证书：

![Let's Encrypt 临时证书](media/ingress/staging-certificate.png)

如果将 Let's Encrypt 更改为使用 `prod` 而不是 `staging`，则会使用由 Let's Encrypt 颁发的可信证书，如以下示例所示：

![Let's Encrypt 证书](media/ingress/certificate.png)

演示应用程序显示在 Web 浏览器中：

![应用程序示例 1](media/ingress/app-one.png)

现在向 FQDN 添加 */hello-world-two* 路径，例如 *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*。 下面显示了带自定义标题的第二个演示应用程序：

![应用程序示例 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>清理资源

本文使用 Helm 来安装入口组件、证书和示例应用。 在部署 Helm 图表时，会创建若干 Kubernetes 资源。 这些资源包括 pod、部署和服务。 若要进行清理，请首先删除证书资源：

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

现在使用 `helm list` 命令列出 Helm 版本。 查找名为“nginx-ingress”、“cert-manager”和“aks-helloworld”的图表，如以下示例输出中所示：

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Tue Oct 16 17:24:05 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
loitering-waterbuffalo  1           Tue Oct 16 17:26:16 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
flabby-deer             1           Tue Oct 16 17:27:06 2018    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Tue Oct 16 17:27:02 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

通过 `helm delete` 命令删除发布。 以下示例删除 NGINX 入口部署、证书管理器和两个示例 AKS hello world 应用。

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

接下来，删除 AKS hello world 应用的 Helm 存储库：

```console
helm repo remove azure-samples
```

最后，删除将流量定向到示例应用的入口路由：

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>后续步骤

本文包含 AKS 的一些外部组件。 若要详细了解这些组件，请参阅以下项目页面：

- [Helm CLI][helm-cli]
- [NGINX 入口控制器][nginx-ingress]
- [证书管理器][cert-manager]

还可以：

- [使用外部网络连接创建基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- [创建使用你自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- [创建一个使用 Let's Encrypt 的入口控制器，以自动生成具有静态公共 IP 地址的 TLS 证书][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
