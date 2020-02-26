---
title: 在 Azure Kubernetes 服务 (AKS) 群集中创建 HTTPS 入口
description: 了解如何安装和配置 NGINX 入口控制器，该控制器使用在 Azure Kubernetes 服务（AKS）群集中生成的自动 TLS 证书。
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 6b465aeb08f14e48249be9587e5fdee1f4b08ba4
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595529"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中创建 HTTPS 入口控制器

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。

本文介绍如何在 Azure Kubernetes 服务（AKS）群集中部署[NGINX 入口控制器][nginx-ingress]。 [证书管理器][cert-manager]项目用于自动生成并配置[我们的加密][lets-encrypt]证书。 最后，在 AKS 群集中运行两个应用程序（可通过单个 IP 地址访问其中的每个应用程序）。

你还可以：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由外接程序][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- [创建使用自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- [创建一个入口控制器，该控制器使用我们的加密来自动生成具有静态公共 IP 地址的 TLS 证书][aks-ingress-static-tls]

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门，并[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 门户][aks-quickstart-portal]。

本文还假定你在 AKS 群集所在的资源组中有一个包含[DNS 区域][dns-zone]的[自定义域][custom-domain]。

本文使用 Helm 安装 NGINX 入口控制器、cert-manager 和示例 Web 应用。 请确保使用 Helm 的最新版本。 有关升级说明，请参阅[Helm 安装文档][helm-install]。有关配置和使用 Helm 的详细信息，请参阅[在 Azure Kubernetes 服务（AKS）中使用 Helm 安装应用程序][use-helm]。

本文还要求运行 Azure CLI 版本2.0.64 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>创建入口控制器

若要创建入口控制器，请使用 `helm` 命令安装*nginx*。 对于增加的冗余，NGINX 入口控制器的两个副本会在部署时具备 `--set controller.replicaCount` 参数。 若要充分利用正在运行的入口控制器副本，请确保 AKS 群集中有多个节点。

还需要在 Linux 节点上计划入口控制器。 Windows Server 节点（当前在 AKS 中为预览版）不应运行入口控制器。 节点选择器是使用 `--set nodeSelector` 参数指定的，用于告知 Kubernetes 计划程序在基于 Linux 的节点上运行 NGINX 入口控制器。

> [!TIP]
> 以下示例创建名为 "*入口-基本*" 的入口资源的 Kubernetes 命名空间。 根据需要为自己的环境指定一个命名空间。

> [!TIP]
> 如果要对群集中的容器的请求启用[客户端源 IP 保留][client-source-ip]，请将 `--set controller.service.externalTrafficPolicy=Local` 添加到 Helm install 命令。 客户端源 IP 存储在请求标头中的*X-转发*的下。 使用启用了客户端源 IP 保存的入口控制器时，SSL 传递将不起作用。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

在安装过程中，将为入口控制器创建一个 Azure 公共 IP 地址。 此公共 IP 地址在入口控制器的寿命期内是静态的。 如果你删除入口控制器，则公共 IP 地址分配会丢失。 如果你然后创建了另外的入口控制器，则会分配新的公共 IP 地址。 如果希望保留使用公共 IP 地址，可以改为使用[静态公共 ip 地址创建入口控制器][aks-ingress-static-tls]。

若要获取公共 IP 地址，请使用 `kubectl get service` 命令。 将 IP 地址分配给服务需要几分钟时间。

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

尚未创建入口规则。 如果浏览到公共 IP 地址，将显示 NGINX 入口控制器的默认 404 页面。

## <a name="add-an-a-record-to-your-dns-zone"></a>将 A 记录添加到 DNS 区域

使用[az network DNS record][az-network-dns-record-set-a-add-record]将*a*记录添加到 DNS 区域，使用 NGINX 服务的外部 IP 地址，并设置添加记录。

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> （可选）可以为入口控制器 IP 地址而不是自定义域配置 FQDN。 请注意，此示例适用于 Bash shell。
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>安装证书管理器

NGINX 入口控制器支持 TLS 终止。 可通过多种方法为 HTTPS 检索和配置证书。 本文演示如何使用[证书管理器，该管理器][cert-manager]提供自动[允许加密][lets-encrypt]证书生成和管理功能。

安装证书管理器控制器：

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace ingress-basic

# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager --namespace ingress-basic --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

有关 cert manager 配置的详细信息，请参阅[证书管理器项目][cert-manager]。

## <a name="create-a-ca-cluster-issuer"></a>创建 CA 群集证书颁发者

证书管理器需要[颁发者][cert-manager-issuer]或[ClusterIssuer][cert-manager-cluster-issuer]资源，才能颁发证书。 这两种 Kubernetes 资源的功能完全相同，区别在于 `Issuer` 适用于单一命名空间，而 `ClusterIssuer` 适用于所有命名空间。 有关详细信息，请参阅[证书管理器颁发者][cert-manager-issuer]文档。

使用以下示例清单创建群集证书颁发者，例如 `cluster-issuer.yaml`。 将电子邮件地址更新为组织提供的有效地址：

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
```

若要创建证书颁发者，请使用 `kubectl apply` 命令。

```console
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

## <a name="run-demo-applications"></a>运行演示应用程序

入口控制器和证书管理解决方案已配置完毕。 现在让我们在你的 AKS 群集中运行两个演示应用程序。 在此示例中，Helm 用于部署一个简单的*Hello world*应用程序的两个实例。

在安装示例 Helm 图表之前，请将 Azure 示例存储库添加到你的 Helm 环境。

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

使用*azure-samples/aks-helloworld* Helm 图表创建名为*aks-helloworld*的演示应用程序。

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

创建名为*aks-helloworld*的演示应用程序的第二个实例。 指定新的标题和唯一的服务名称，以使这两个应用程序在外观上与 *--set*不同。

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>创建入口路由

两个应用程序现在都在 Kubernetes 群集中运行。 但是，它们使用类型 `ClusterIP` 的服务进行配置，并且无法从 internet 进行访问。 若要公开发布这两个应用程序，请创建 Kubernetes 入口资源。 该入口资源配置将流量路由到这两个应用程序之一的规则。

在下面的示例中，流量为 " *hello-世界"。MY_CUSTOM_DOMAIN*将路由到*helloworld*服务。 发往地址的流量 *。MY_CUSTOM_DOMAIN/hello-world-two*将路由到*aks-helloworld-2*服务。 到*世界各地的流量。MY_CUSTOM_DOMAIN/静态*将路由到 aks 的服务 *-helloworld*用于静态资产。

使用下面的示例 YAML 创建名为 `hello-world-ingress.yaml` 的文件。 将 *hosts* 和 *host* 更新为在前面步骤中创建的 DNS 名称。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

使用 `kubectl apply` 命令创建入口资源。

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>验证是否已创建证书对象

接下来，必须创建证书资源。 证书资源定义了必需的 X.509 证书。 有关详细信息，请参阅[cert-manager 证书][cert-manager-certificates]。 Cert 管理器已自动为你创建了一个证书对象，并使用了入口填充程序，该程序会在0.2.2 后自动部署到证书管理器。 有关详细信息，请参阅[入口填充文档][ingress-shim]。

若要验证证书是否已成功创建，请使用 `kubectl get certificate --namespace ingress-basic` 命令，并验证*READY*是否为*True*，这可能需要几分钟的时间。

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>测试入口配置

将 web 浏览器打开到 " *hello-世界"。* Kubernetes 入口控制器的 MY_CUSTOM_DOMAIN。 请注意，会重定向到 "使用 HTTPS"，证书受信任，演示应用程序将显示在 web 浏览器中。 添加 */hello-world-two*路径，并注意显示了具有自定义标题的第二个演示应用程序。

## <a name="clean-up-resources"></a>清理资源

本文使用 Helm 来安装入口组件、证书和示例应用。 在部署 Helm 图表时，会创建若干 Kubernetes 资源。 这些资源包括 pod、部署和服务。 若要清理这些资源，可以删除整个示例命名空间或单独的资源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>删除示例命名空间和所有资源

若要删除整个示例命名空间，请使用 `kubectl delete` 命令，并指定你的命名空间名称。 命名空间中的所有资源都将被删除。

```console
kubectl delete namespace ingress-basic
```

然后，删除 AKS hello world 应用的 Helm 存储库：

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>单独删除资源

另外，更精细的方法是删除创建的单个资源。 首先，删除群集颁发者资源：

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

列出带有 `helm list` 命令的 Helm 版本。 查找名为“nginx-ingress”和“aks-helloworld”的图表，如以下示例输出中所示：

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.12.0    v0.12.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

通过 `helm delete` 命令删除发布。 以下示例删除 NGINX 入口部署和两个示例 AKS hello world 应用。

```
$ helm delete aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

接下来，删除 AKS hello world 应用的 Helm 存储库：

```console
helm repo remove azure-samples
```

删除将流量定向到示例应用的入口路由：

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

最后，可以删除自身的命名空间。 使用 `kubectl delete` 命令并指定你的命名空间名称：

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>后续步骤

本文包含 AKS 的一些外部组件。 若要详细了解这些组件，请参阅以下项目页面：

- [Helm CLI][helm-cli]
- [NGINX 入口控制器][nginx-ingress]
- [证书管理器][cert-manager]

你还可以：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由外接程序][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- [创建使用自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- [创建一个入口控制器，该控制器使用我们的加密来自动生成具有静态公共 IP 地址的 TLS 证书][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
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
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
