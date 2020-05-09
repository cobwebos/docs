---
title: 使用 TLS 证书进行入口
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中安装和配置使用你自己的证书的 NGINX 入口控制器。
services: container-service
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: dce3cf4e7db45b00b29469524d7576f6065ebaf4
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561924"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>创建 HTTPS 入口控制器并在 Azure Kubernetes 服务 (AKS) 中使用自己的 TLS 证书

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。

本文介绍如何在 Azure Kubernetes 服务 (AKS) 群集中部署 [NGINX 入口控制器][nginx-ingress]。 生成自己的证书，并创建用于入口路由的 Kubernetes 机密。 最后，在 AKS 群集中运行两个应用程序（可通过单个 IP 地址访问其中的每个应用程序）。

也可执行以下操作：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- 创建一个使用 Let's Encrypt 的入口控制器，以自动生成[具有动态公共 IP 地址][aks-ingress-tls]或[具有静态公共 IP 地址][aks-ingress-static-tls]的 TLS 证书

## <a name="before-you-begin"></a>开始之前

本文使用[Helm 3][helm]来安装 NGINX 入口控制器。 请确保使用 Helm 的最新版本。 有关升级说明，请参阅 [Helm 安装文档][helm-install]。有关配置和使用 Helm 的详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中使用 Helm 安装应用程序][use-helm]。

本文还要求运行 Azure CLI 2.0.64 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>创建入口控制器

若要创建入口控制器，请使用 `Helm` 来安装 *nginx-ingress*。 对于增加的冗余，NGINX 入口控制器的两个副本会在部署时具备 `--set controller.replicaCount` 参数。 若要充分利用正在运行的入口控制器副本，请确保 AKS 群集中有多个节点。

还需要在 Linux 节点上计划入口控制器。 Windows Server 节点不应运行入口控制器。 使用 `--set nodeSelector` 参数指定节点选择器，以告知 Kubernetes 计划程序在基于 Linux 的节点上运行 NGINX 入口控制器。

> [!TIP]
> 以下示例为名为 *ingress-basic* 的入口资源创建 Kubernetes 命名空间。 根据需要为你自己的环境指定一个命名空间。 如果 AKS 群集未启用 RBAC，请将 `--set rbac.create=false` 添加到 Helm 命令中。

> [!TIP]
> 若要为对群集中容器的请求启用[客户端源 IP 保留][client-source-ip]，请将 `--set controller.service.externalTrafficPolicy=Local` 添加到 Helm install 命令中。 客户端源 IP 存储在 X-Forwarded-For** 下的请求头中。 使用启用了客户端源 IP 保存的入口控制器时，TLS 传递将不起作用。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

在安装过程中，将为入口控制器创建一个 Azure 公共 IP 地址。 此公共 IP 地址在入口控制器的寿命期内是静态的。 如果你删除入口控制器，则公共 IP 地址分配会丢失。 如果你然后创建了另外的入口控制器，则会分配新的公共 IP 地址。 如果希望保持使用此公共 IP 地址，则可以改为[创建具有静态公共 IP 地址的入口控制器][aks-ingress-static-tls]。

若要获取公共 IP 地址，请使用 `kubectl get service` 命令。 将 IP 地址分配给服务需要几分钟时间。

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

请记下此公共 IP 地址，因为在最后一个步骤中测试部署时需要用到。

尚未创建入口规则。 如果浏览到公共 IP 地址，将显示 NGINX 入口控制器的默认 404 页面。

## <a name="generate-tls-certificates"></a>生成 TLS 证书

在本文中，我们将生成使用 `openssl` 的自签名证书。 对于生产用途，应该通过提供商或你自己的证书颁发机构 (CA) 请求受信任的已签名证书。 下一步骤将使用 TLS 证书和 OpenSSL 生成的私钥来生成 Kubernetes 机密。**

以下示例生成有效期为 365 天的、名为 *aks-ingress-tls.crt* 的 2048 位 RSA X509 证书。 私钥文件名为 *aks-ingress-tls.key*。 Kubernetes TLS 机密需要这两个文件。

本文使用 *demo.azure.com* 使用者公用名，无需进行更改。 对于生产用途，请为 `-subj` 参数指定自己的组织值：

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>创建 TLS 证书的 Kubernetes 机密

若使 Kubernetes 能够对入口控制器使用 TLS 证书和私钥，需要创建并使用一个机密。 机密只需定义一次，它使用上一步骤中创建的证书和密钥文件。 然后，可以在定义入口路由时引用此机密。

以下示例创建名为 *aks-ingress-tls* 的机密：

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>运行演示应用程序

已配置入口控制器和包含你的证书的机密。 现在让我们在你的 AKS 群集中运行两个演示应用程序。 此示例使用 Helm 来部署一个简单“Hello world”应用程序的两个实例。

若要查看入口控制器的运行情况，请在 AKS 群集中运行两个演示应用程序。 在此示例中，使用`kubectl apply`部署一个简单的*Hello world*应用程序的两个实例。

创建*aks-helloworld yaml*文件并在下面的示例中复制 yaml：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

创建*yaml*文件，并复制以下示例 yaml：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

使用`kubectl apply`以下程序运行两个演示应用程序：

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>创建入口路由

两个应用程序现在都在 Kubernetes 群集中运行，但它们配置了服务类型 `ClusterIP`。 因此，无法通过 Internet 访问它们。 若要公开发布这两个应用程序，请创建 Kubernetes 入口资源。 该入口资源配置将流量路由到这两个应用程序之一的规则。

在以下示例中，传往地址 `https://demo.azure.com/` 的流量将路由到名为 `aks-helloworld` 的服务。 传往地址 `https://demo.azure.com/hello-world-two` 的流量将路由到 `ingress-demo` 服务。 在本文中，无需更改这些演示主机名。 对于生产用途，请提供在证书请求和生成过程中指定的名称。

> [!TIP]
> 如果在证书请求过程中指定的主机名（CN 名称）与在入口路由中定义的主机不匹配，则入口控制器将显示“Kubernetes 入口控制器虚构证书”警告。** 请确保证书和入口路由主机名匹配。

*tls* 节告知入口路由要对主机 *demo.azure.com* 使用名为 *aks-ingress-tls* 的机密。 同样，对于生产用途，请指定自己的主机地址。

创建名为 `hello-world-ingress.yaml` 的文件，并将其复制到以下示例 YAML 中。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

使用 `kubectl apply -f hello-world-ingress.yaml` 命令创建入口资源。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>测试入口配置

若要使用虚构的 *demo.azure.com* 主机测试证书，请使用 `curl` 并指定 *--resolve* 参数。 此参数告知要将 *demo.azure.com* 名称映射到入口控制器的公共 IP 地址。 请按以下示例中所示，指定自己的入口控制器的公共 IP 地址：

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

地址未提供其他路径，因此入口控制器默认*/* 路由。 第一个演示应用程序已返回，如以下精简版示例输出中所示：

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

`curl` 命令中的 *-v* 参数输出详细信息，包括收到的 TLS 证书。 在输出 curl 结果的中途，可以验证是否使用了你自己的 TLS 证书。 即使使用的是自签名证书，*-k* 参数也会继续加载页面。 以下示例显示使用了 *issuer: CN=demo.azure.com; O=aks-ingress-tls* 证书：

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

现在向地址添加“/hello-world-two”路径，例如 `https://demo.azure.com/hello-world-two`**。 第二个使用自定义标题的演示应用程序已返回，如以下精简版示例输出中所示：

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>清理资源

本文使用了 Helm 来安装入口组件和示例应用。 在部署 Helm 图表时，会创建若干 Kubernetes 资源。 这些资源包括 pod、部署和服务。 若要清理这些资源，可以删除整个示例命名空间，也可以删除单个资源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>删除示例命名空间以及所有资源

若要删除整个示例命名空间，请使用 `kubectl delete` 命令并指定命名空间名称。 将会删除命名空间中的所有资源。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>单独删除资源

也可采用更细致的方法来删除单个已创建的资源。 使用 `helm list` 命令列出 Helm 版本。 查找名为*nginx 的*图表，如以下示例输出所示：

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

用`helm uninstall`命令卸载发布。 下面的示例将卸载 NGINX 入口部署。

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

接下来，删除两个示例应用程序：

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

删除将流量定向到示例应用的入口路由：

```console
kubectl delete -f hello-world-ingress.yaml
```

删除证书机密：

```console
kubectl delete secret aks-ingress-tls
```

最后，可以删除自身命名空间。 使用 `kubectl delete` 命令并指定命名空间名称。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>后续步骤

本文包含 AKS 的一些外部组件。 若要详细了解这些组件，请参阅以下项目页面：

- [Helm CLI][helm-cli]
- [NGINX 入口控制器][nginx-ingress]

也可执行以下操作：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- 创建一个使用 Let's Encrypt 的入口控制器，以自动生成[具有动态公共 IP 地址][aks-ingress-tls]或[具有静态公共 IP 地址][aks-ingress-static-tls]的 TLS 证书

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
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
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
