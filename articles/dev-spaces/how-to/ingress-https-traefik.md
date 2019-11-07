---
title: 使用自定义 traefik 入口控制器并配置 HTTPS
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: 了解如何配置 Azure Dev Spaces 以使用自定义 traefik 入口控制器并使用该入口控制器配置 HTTPS
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
ms.openlocfilehash: c015fe8e7108f07d66d2464c4f8b6287e8f54446
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582322"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>使用自定义 traefik 入口控制器并配置 HTTPS

本文介绍如何将 Azure Dev Spaces 配置为使用自定义 traefik 入口控制器。 本文还介绍如何将该自定义入口控制器配置为使用 HTTPS。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有帐户，可以[创建一个免费帐户][azure-account-create]。
* [已安装 Azure CLI][az-cli]。
* [启用 Azure Dev Spaces 的 Azure Kubernetes 服务（AKS）群集][qs-cli]。
* 已安装[kubectl][kubectl] 。
* [已安装 Helm 2.13 或更高版本][helm-installed]。
* [一个自定义域][custom-domain]，其中包含与你的 AKS 群集位于同一资源组中的[DNS 区域][dns-zone]。

## <a name="configure-a-custom-traefik-ingress-controller"></a>配置自定义 traefik 入口控制器

使用[kubectl][kubectl]和 Kubernetes 命令行客户端连接到群集。 若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点的列表。

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

为 traefik 入口控制器创建 Kubernetes 命名空间，并使用 `helm`安装该命名空间。

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

使用[kubectl get][kubectl-get]获取 traefik 入口控制器服务的 IP 地址。

```console
kubectl get svc -n traefik --watch
```

示例输出显示*traefik*命名空间中所有服务的 IP 地址。

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

使用[az network DNS record][az-network-dns-record-set-a-add-record]将*a*记录添加到 DNS 区域，使用 TRAEFIK 服务的外部 IP 地址，并设置添加记录。

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

上面的示例将*A*记录添加到*MY_CUSTOM_DOMAIN* DNS 区域。

本文使用 [Azure Dev Spaces 单车共享示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)来演示 Azure Dev Spaces 的用法。 从 GitHub 克隆此应用程序，然后导航到其目录中：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

打开[yaml][values-yaml] ，并将 *< REPLACE_ME_WITH_HOST_SUFFIX >* 的所有实例替换为*traefik。MY_CUSTOM_DOMAIN*将你的域用于*MY_CUSTOM_DOMAIN*。 另外，请将*kubernetes.io/ingress.class： traefik-azds # Dev Spaces 指定*为*kubernetes.io/ingress.class： Traefik # Custom 入口*。 下面是更新的 `values.yaml` 文件的示例：

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

保存更改并关闭该文件。

使用 `helm install`部署示例应用程序。

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

上面的示例将示例应用程序部署到*dev*命名空间。

使用 `azds space select` 选择与示例应用程序的*开发*环境，并显示 url 以使用 `azds list-uris`访问示例应用程序。

```console
azds space select -n dev
azds list-uris
```

下面的输出显示来自 `azds list-uris`的示例 Url。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

通过 *命令打开公共 URL，导航到*bikesharingweb`azds list-uris` 服务。 在以上示例中，*bikesharingweb* 服务的公共 URL 为 `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`。

使用 `azds space select` 命令在 "*开发*" 下创建子空间，并列出用于访问子 dev 空间的 url。

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

下面的输出显示 `azds list-uris` 中用于访问*azureuser1*子开发人员空间中的示例应用程序的示例 url。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

通过从 "`azds list-uris`" 命令打开 "公共 URL"，导航到*azureuser1*子 dev 空间中的*bikesharingweb*服务。 在上面的示例中， *bikesharingweb*服务的公共 URL 在*azureuser1*子开发人员空间中 `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`。

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>将 traefik 入口控制器配置为使用 HTTPS

创建类似于以下示例的 `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` 文件。 使用你自己的电子邮件更新 "*电子邮件*" 值，此电子邮件用于生成证书，并使用 "加密"。

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

使用 `helm repo update` 更新你的*traefik*服务，并包括你创建的*traefik yaml*文件。

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

上述命令使用*traefik 值 yaml*中的值运行 traefik 服务的新版本，并删除以前的服务。 Traefik 服务还会创建一个 TLS 证书，使用我们的加密，并开始重定向 web 流量以使用 HTTPS。

> [!NOTE]
> 新版本的 traefik 服务可能需要几分钟时间才能启动。 您可以使用 `kubectl get pods --namespace traefik --watch`来检查进度。

导航到*dev/azureuser1*子空间中的示例应用程序，注意将重定向到 "使用 HTTPS"。 另请注意，页面加载，但浏览器显示一些错误。 打开浏览器控制台时，将显示与尝试加载 HTTP 资源的 HTTPS 页相关的错误。 例如：

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

若要修复此错误，请更新[BikeSharingWeb/azds yaml][azds-yaml] ，以将*traefik*用于*kubernetes.io/ingress.class* ，并将自定义域用于 *$ （hostSuffix）* 。 例如：

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

使用*url*包的依赖项更新[BikeSharingWeb/package。][package-json]

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

将[BikeSharingWeb/pages/][helpers-js] *getApiHostAsync*中的方法更新为使用 HTTPS：

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

导航到 `BikeSharingWeb` 目录，并使用 `azds up` 运行更新的 BikeSharingWeb 服务。

```console
cd BikeSharingWeb/
azds up
```

导航到*dev/azureuser1*子空间中的示例应用程序，请注意，将重定向到使用 HTTPS，但不会出现任何错误。

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 中的团队开发][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml