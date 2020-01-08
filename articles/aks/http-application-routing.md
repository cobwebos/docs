---
title: Azure Kubernetes 服务 (AKS) 中的 HTTP 应用程序路由加载项
description: 使用 Azure Kubernetes 服务 (AKS) 中的 HTTP 应用程序路由加载项。
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/06/2019
ms.author: laevenso
ms.openlocfilehash: cfd69ebf6408acaa2938271ba87f36768416de80
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442948"
---
# <a name="http-application-routing"></a>HTTP 应用程序路由

可以通过 HTTP 应用程序路由解决方案轻松地访问部署到 Azure Kubernetes 服务 (AKS) 群集的应用程序。 启用该解决方案后，它将在 AKS 群集中配置[入口控制器](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)。 在部署应用程序以后，此解决方案还可以为应用程序终结点创建可公开访问的 DNS 名称。

启用此加载项后，它会在订阅中创建 DNS 区域。 有关 DNS 开销的详细信息，请参阅[dns 定价][dns-pricing]。

> [!CAUTION]
> HTTP 应用程序路由加载项旨在可以快速创建入口控制器并访问应用程序。 建议不要将此加载项用于生产。 对于包含多个副本和 TLS 支持的生产就绪入口部署，请参阅[创建 HTTPS 入口控制器](https://docs.microsoft.com/azure/aks/ingress-tls)。

## <a name="http-routing-solution-overview"></a>HTTP 路由解决方案概述

外接程序部署两个组件：一个[Kubernetes 入口控制器][ingress]和一个[外部 DNS][external-dns]控制器。

- **入口控制器**：入口控制器通过类型为 LoadBalancer 的 Kubernetes 服务公开给 Internet。 入口控制器监视并实现[Kubernetes 入口资源][ingress-resource]，这些资源创建指向应用程序终结点的路由。
- **External-DNS 控制器**：监视 Kubernetes 入口资源并在特定于群集的 DNS 区域中创建 DNS A 记录。

## <a name="deploy-http-routing-cli"></a>部署 HTTP 路由：CLI

HTTP 应用程序路由加载项可以在部署 AKS 群集时通过 Azure CLI 来启用。 为此，请将[az aks create][az-aks-create]命令与 `--enable-addons` 参数一起使用。

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> 如果要启用多个加载项，请以逗号分隔列表的形式提供。 例如，要启用 HTTP 应用程序路由和监视，请使用格式 `--enable-addons http_application_routing,monitoring`。

还可以使用[az AKS enable-加载项][az-aks-enable-addons]命令在现有 AKS 群集上启用 HTTP 路由。 若要在现有群集上启用 HTTP 路由，请添加 `--addons` 参数并指定 *http_application_routing*，如以下示例所示：

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

部署或更新群集后，使用[az aks show][az-aks-show]命令检索 DNS 区域名称。 将应用程序部署到 AKS 群集时，需要此名称。

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpapplicationrouting.config.HTTPApplicationRoutingZoneName -o table
```

结果

9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io


## <a name="deploy-http-routing-portal"></a>部署 HTTP 路由：门户

HTTP 应用程序路由加载项可以在部署 AKS 群集时通过 Azure 门户来启用。

![启用 HTTP 路由功能](media/http-routing/create.png)

部署群集以后，浏览到自动创建的 AKS 资源组，然后选择 DNS 区域。 记下 DNS 区域名称。 将应用程序部署到 AKS 群集时，需要此名称。

![获取 DNS 区域名称](media/http-routing/dns.png)

## <a name="use-http-routing"></a>使用 HTTP 路由

只能在具有如下注释的入口资源上触发 HTTP 应用程序路由解决方案：

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

创建名为 **samples-http-application-routing.yaml** 的文件，并将其复制到以下 YAML 中。 在第 43 行，将 `<CLUSTER_SPECIFIC_DNS_ZONE>` 更新为在本文上一步中收集的 DNS 区域名称。


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

使用[kubectl apply][kubectl-apply]命令创建资源。

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

使用 cURL 或浏览器导航到在 samples-http-application-routing.yaml 文件的 host 节中指定的主机名。 可能需要等待长达 1 分钟的时间才能通过 Internet 访问应用程序。

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>删除 HTTP 路由

可以使用 Azure CLI 删除 HTTP 路由解决方案。 为此，请运行以下命令，并替换 AKS 群集和资源组名称。

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

当禁用了 HTTP 应用程序路由加载项时，某些 Kubernetes 资源可能会保留在群集中。 这些资源包括 *configMap* 和 *secret*，并且是在 *kube-system* 命名空间中创建的。 为了维护一个干净的群集，你可能想要删除这些资源。

使用以下[kubectl get][kubectl-get]命令查找*加载项-http 应用程序-路由*资源：

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

以下示例输出显示了应当删除的 configMap：

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

若要删除资源，请使用[kubectl delete][kubectl-delete]命令。 指定资源类型、资源名称和命名空间。 以下示例删除上面的 configmap 之一：

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

针对群集中剩余的所有 *addon-http-application-routing* 资源重复前面的 `kubectl delete` 步骤。

## <a name="troubleshoot"></a>故障排除

使用[kubectl logs][kubectl-logs]命令查看外部 DNS 应用程序的应用程序日志。 这些日志应确认已成功创建 A 和 TXT DNS 记录。

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

也可在 Azure 门户的 DNS 区域资源上查看这些记录。

![获取 DNS 记录](media/http-routing/clippy.png)

使用[kubectl logs][kubectl-logs]命令查看 Nginx 入口控制器的应用程序日志。 这些日志应确认入口资源的 `CREATE` 操作和控制器的重新加载操作。 所有 HTTP 活动都会记录到日志中。

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>清除

删除在本文中创建的相关 Kubernetes 对象。

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>后续步骤

有关如何在 AKS 中安装受 HTTPS 保护的入口控制器的信息，请参阅[Azure Kubernetes 服务（AKS）上的 Https 入口][ingress-https]。

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
