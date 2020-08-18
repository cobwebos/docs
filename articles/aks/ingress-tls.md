---
title: 通过自动 TLS 创建入口
titleSuffix: Azure Kubernetes Service
description: 了解如何安装和配置 NGINX 入口控制器，该控制器使用 Let's Encrypt 在 Azure Kubernetes 服务 (AKS) 群集中自动生成 TLS 证书。
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: c86b4e921dce6258ac585375e686bec5fa44b211
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508951"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中创建 HTTPS 入口控制器

入口控制器是一个软件片段，为 Kubernetes 服务提供反向代理、可配置的流量路由和 TLS 终止。 Kubernetes 入口资源用于配置各个 Kubernetes 服务的入口规则和路由。 借助入口控制器和入口规则，可以使用单个 IP 地址将流量路由到 Kubernetes 群集中的多个服务。

本文介绍如何在 Azure Kubernetes 服务 (AKS) 群集中部署 [NGINX 入口控制器][nginx-ingress]。 [cert-manager][cert-manager] 项目用于自动生成和配置 [Let's Encrypt][lets-encrypt] 证书。 最后，在 AKS 群集中运行两个应用程序（可通过单个 IP 地址访问其中的每个应用程序）。

也可执行以下操作：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- [创建使用你自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- [创建一个使用 Let's Encrypt 的入口控制器，以自动生成具有静态公共 IP 地址的 TLS 证书][aks-ingress-static-tls]

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

本文还假定你在 AKS 群集所在的资源组中有一个包含[DNS 区域][dns-zone]的[自定义域][custom-domain]。

本文使用 [Helm 3][helm] 安装 NGINX 入口控制器和证书管理器。 请确保使用最新版本的 Helm，并且有权访问 *稳定* 和 *jetstack* Helm 存储库。 有关升级说明，请参阅 [Helm 安装文档][helm-install]。有关配置和使用 Helm 的详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中使用 Helm 安装应用程序][use-helm]。

本文还要求运行 Azure CLI 2.0.64 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>创建入口控制器

若要创建入口控制器，请使用 `helm` 命令来安装 *nginx-ingress*。 对于增加的冗余，NGINX 入口控制器的两个副本会在部署时具备 `--set controller.replicaCount` 参数。 若要充分利用正在运行的入口控制器副本，请确保 AKS 群集中有多个节点。

还需要在 Linux 节点上计划入口控制器。 Windows Server 节点不应运行入口控制器。 使用 `--set nodeSelector` 参数指定节点选择器，以告知 Kubernetes 计划程序在基于 Linux 的节点上运行 NGINX 入口控制器。

> [!TIP]
> 以下示例为名为 *ingress-basic* 的入口资源创建 Kubernetes 命名空间。 根据需要为你自己的环境指定一个命名空间。

> [!TIP]
> 若要为对群集中容器的请求启用[客户端源 IP 保留][client-source-ip]，请将 `--set controller.service.externalTrafficPolicy=Local` 添加到 Helm install 命令中。 客户端源 IP 存储在 X-Forwarded-For 下的请求头中。 使用启用了“客户端源 IP 保留”的入口控制器时，TLS 直通将不起作用。

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

在安装过程中，将为入口控制器创建一个 Azure 公共 IP 地址。 此公共 IP 地址在入口控制器的寿命期内是静态的。 如果你删除入口控制器，则公共 IP 地址分配会丢失。 如果你然后创建了另外的入口控制器，则会分配新的公共 IP 地址。 如果希望保持使用此公共 IP 地址，则可以改为[创建具有静态公共 IP 地址的入口控制器][aks-ingress-static-tls]。

若要获取公共 IP 地址，请使用 `kubectl get service` 命令。 将 IP 地址分配给服务需要几分钟时间。

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

尚未创建入口规则。 如果浏览到公共 IP 地址，将显示 NGINX 入口控制器的默认 404 页面。

## <a name="add-an-a-record-to-your-dns-zone"></a>将 A 记录添加到 DNS 区域

使用 [az network dns record-set a add-record][az-network-dns-record-set-a-add-record] 将 *A* 记录添加到使用 NGINX 服务的外部 IP 地址的 DNS 区域。

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name '*' \
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

NGINX 入口控制器支持 TLS 终止。 可通过多种方法为 HTTPS 检索和配置证书。 本文演示如何使用[证书管理器][cert-manager]，该管理器提供自动 [Lets Encrypt][lets-encrypt] 证书生成和管理功能。

若要安装证书管理器控制器，请执行以下命令：

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

若要详细了解证书管理器配置，请参阅[证书管理器项目][cert-manager]。

## <a name="create-a-ca-cluster-issuer"></a>创建 CA 群集证书颁发者

证书管理器需要 [Issuer][cert-manager-issuer] 或 [ClusterIssuer][cert-manager-cluster-issuer] 资源，才能颁发证书。 这两种 Kubernetes 资源的功能完全相同，区别在于 `Issuer` 适用于单一命名空间，而 `ClusterIssuer` 适用于所有命名空间。 有关详细信息，请参阅[证书管理器颁发者][cert-manager-issuer]文档。

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
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

若要创建证书颁发者，请使用 `kubectl apply` 命令。

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>运行演示应用程序

入口控制器和证书管理解决方案已配置完毕。 现在让我们在你的 AKS 群集中运行两个演示应用程序。 此示例使用 Helm 来部署一个简单“Hello world”应用程序的两个实例。

若要查看运行中的入口控制器，请在 AKS 群集中运行两个演示应用程序。 此示例使用 `kubectl apply` 来部署一个简单“Hello world”应用程序的两个实例。

创建“aks-helloworld-one.yaml”文件，并将其复制到以下示例 YAML 中：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

创建“aks-helloworld-two.yaml”文件，并将其复制到以下示例 YAML 中：

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

使用 `kubectl apply` 运行这两个演示应用程序：

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>创建入口路由

两个应用程序现在都在 Kubernetes 群集中运行。 但是，它们使用类型为 `ClusterIP` 的服务进行配置，并且无法从 Internet 进行访问。 若要公开发布这两个应用程序，请创建 Kubernetes 入口资源。 该入口资源配置将流量路由到这两个应用程序之一的规则。

在以下示例中，到地址 *hello-world-ingress.MY_CUSTOM_DOMAIN* 的流量会路由到 *aks-helloworld* 服务。 到地址 *hello-world-ingress.MY_CUSTOM_DOMAIN/hello-world-two* 的流量会路由到 *aks-helloworld-two* 服务。 到 *hello-world-ingress.MY_CUSTOM_DOMAIN/static* 的流量会路由到静态资产的名为 *aks-helloworld* 的服务。

> [!NOTE]
> 如果为入口控制器 IP 地址（而不是自定义域）配置了 FQDN，请使用 FQDN 而不是 hello-world-ingress.MY_CUSTOM_DOMAIN。 例如，如果你的 FQDN 是*demo-aks-ingress.eastus.cloudapp.azure.com*，则替换*hello-world。MY_CUSTOM_DOMAIN*中*demo-aks-ingress.eastus.cloudapp.azure.com*的 demo-aks-ingress.eastus.cloudapp.azure.com `hello-world-ingress.yaml` 。

使用下面的示例 YAML 创建名为 `hello-world-ingress.yaml` 的文件。 将 *hosts* 和 *host* 更新为在前面步骤中创建的 DNS 名称。

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
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
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
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
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

使用 `kubectl apply` 命令创建入口资源。

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>验证是否已创建证书对象

接下来，必须创建证书资源。 证书资源定义了必需的 X.509 证书。 有关详细信息，请参阅[证书管理器证书][cert-manager-certificates]。 证书管理器已使用 ingress-shim（自 v0.2.2 以来随证书管理器自动部署）为你自动创建了证书对象。 有关详细信息，请参阅 [ingress-shim 文档][ingress-shim]。

若要验证证书是否已成功创建，请使用 `kubectl get certificate --namespace ingress-basic` 命令，并验证 *READY* 是否为 *True*，这可能需要数分钟。

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>测试入口配置

将 Web 浏览器打开到 Kubernetes 入口控制器的 *hello-world-ingress.MY_CUSTOM_DOMAIN*。 请注意，系统会将你重定向，让你使用 HTTPS。证书是受信任的，演示应用程序显示在 Web 浏览器中。 添加 */hello-world-two* 路径，并注意显示了自定义标题的第二个演示应用程序。

## <a name="clean-up-resources"></a>清理资源

本文使用 Helm 来安装入口组件、证书和示例应用。 在部署 Helm 图表时，会创建若干 Kubernetes 资源。 这些资源包括 pod、部署和服务。 若要清理这些资源，可以删除整个示例命名空间，也可以删除单个资源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>删除示例命名空间以及所有资源

若要删除整个示例命名空间，请使用 `kubectl delete` 命令并指定命名空间名称。 将会删除命名空间中的所有资源。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>单独删除资源

也可采用更细致的方法来删除单个已创建的资源。 首先，删除群集颁发者资源：

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

使用 `helm list` 命令列出 Helm 版本。 查找名为“nginx”和“cert-manager”的图表，如以下示例输出中所示 ：

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

使用 `helm uninstall` 命令卸载这些版本。 以下示例将卸载 NGINX 入口和证书管理器部署。

```
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

接下来，删除两个示例应用程序：

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

删除将流量定向到示例应用的入口路由：

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

最后，可以删除自身命名空间。 使用 `kubectl delete` 命令并指定命名空间名称。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>后续步骤

本文包含 AKS 的一些外部组件。 若要详细了解这些组件，请参阅以下项目页面：

- [Helm CLI][helm-cli]
- [NGINX 入口控制器][nginx-ingress]
- [证书管理器][cert-manager]

也可执行以下操作：

- [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
- [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
- [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
- [创建使用你自己的 TLS 证书的入口控制器][aks-ingress-own-tls]
- [创建一个使用 Let's Encrypt 的入口控制器，以自动生成具有静态公共 IP 地址的 TLS 证书][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
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
