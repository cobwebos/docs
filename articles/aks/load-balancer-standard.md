---
title: 预览-在 Azure Kubernetes 服务中使用标准 SKU 负载均衡器 (AKS)
description: 了解如何结合使用负载均衡器和标准 SKU 来向 Azure Kubernetes 服务公开服务 (AKS)。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: 422189952096ef25b69e62aa2708c59385b0637a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898959"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>预览-在 Azure Kubernetes 服务中使用标准 SKU 负载均衡器 (AKS)

若要在 Azure Kubernetes Service (AKS) 中提供对应用程序的访问权限, 可以创建和使用 Azure 负载均衡器。 在 AKS 上运行的负载均衡器可用作内部或外部负载均衡器。 内部负载均衡器使 Kubernetes 服务仅可用于在与 AKS 群集相同的虚拟网络中运行的应用程序。 外部负载均衡器接收一个或多个公共 Ip 用于入口, 并使 Kubernetes 服务可以使用公共 Ip 进行外部访问。

Azure 负载均衡器以两种 SKU 提供：“基本”和“标准”。 默认情况下，使用服务清单在 AKS 上创建负载均衡器时，将使用基本 SKU。 使用*标准*SKU 负载均衡器可提供其他特性和功能, 如更大的后端池大小和可用性区域。 在选择要使用的负载均衡器之前, 请务必了解*标准*负载均衡器与*基本*负载均衡器之间的差异。 创建 AKS 群集后, 将无法更改该群集的负载均衡器 SKU。 有关*基本*和*标准*sku 的详细信息, 请参阅[Azure 负载均衡器 SKU 比较][azure-lb-comparison]。

本文介绍如何通过 Azure Kubernetes 服务 (AKS) 创建和使用带有*标准*SKU 的 Azure 负载均衡器。

本文假定你基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息, 请参阅[Azure Kubernetes Service (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]和[什么是 Azure 负载均衡器？][azure-lb]。

此功能目前处于预览状态。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI, 本文要求运行 Azure CLI 版本2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>开始之前

如果使用现有子网或资源组，则 AKS 群集服务主体需要管理网络资源的权限。 通常，将“网络参与者”角色分配给委派资源上的服务主体。 有关权限的详细信息，请参阅[委派 AKS 访问其他 Azure 资源][aks-sp]。

必须创建 AKS 群集, 将负载均衡器的 SKU 设置为*Standard* , 而不是使用默认的*基本*值。 稍后的步骤中介绍了如何创建 AKS 群集, 但首先需要启用一些预览功能。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供, 并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此, 这些功能并不用于生产。 有关其他信息, 请参阅以下支持文章:
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用 Azure 负载均衡器标准版 SKU, 需要*aks* CLI 扩展版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展, 然后使用[az extension update][az-extension-update]命令检查是否有任何可用更新:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>注册 AKSAzureStandardLoadBalancer 预览功能

若要创建可将负载均衡器与*标准*SKU 一起使用的 AKS 群集, 必须在订阅上启用*AKSAzureStandardLoadBalancer*功能标志。 *AKSAzureStandardLoadBalancer*功能还在使用虚拟机规模集创建群集时使用*VMSSPreview* 。 此功能在配置群集时提供了最新的服务增强功能。 尽管这不是必需的, 但建议同时启用*VMSSPreview*功能标志。

> [!CAUTION]
> 在订阅上注册功能时, 当前无法注册该功能。 启用某些预览功能后, 默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

使用[az feature register][az-feature-register]命令注册*VMSSPreview*和*AKSAzureStandardLoadBalancer*功能标志, 如以下示例中所示:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> 成功注册*VMSSPreview*或*AKSAzureStandardLoadBalancer*功能标志后创建的任何 AKS 群集都将使用此预览版群集体验。 若要继续创建常规且完全受支持的群集, 请不要对生产订阅启用预览功能。 使用单独的测试或开发 Azure 订阅来测试预览功能。

状态显示为“已注册”需要几分钟时间。 您可以使用[az feature list][az-feature-list]命令检查注册状态:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

准备就绪后, 请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>限制

当你创建和管理支持具有*标准*SKU 的负载均衡器的 AKS 群集时, 以下限制适用:

* 为负载均衡器使用*标准*SKU 时, 必须允许公共地址, 并避免创建 ban IP 创建的任何 Azure 策略。 AKS 群集会自动在为 AKS 群集创建的同一资源组中创建*标准*SKU 公共 IP, 这通常在开头使用*MC_* 进行命名。 AKS 将公共 IP 分配给*标准*SKU 负载均衡器。 需要公共 IP 才能允许来自 AKS 群集的传出流量。 还需要此公共 IP 来维护控制平面和代理节点之间的连接, 以及保持与早期版本的 AKS 的兼容性。
* 为负载均衡器使用*标准*SKU 时, 必须使用 Kubernetes 版本1.13.5 或更高版本。

此功能处于预览阶段, 但以下附加限制适用:

* 在 AKS 中使用负载均衡器的*标准*SKU 时, 不能为负载均衡器的出口设置自己的公共 IP 地址。 必须使用 AKS 分配给负载均衡器的 IP 地址。
* 这不能用于[Node 公共 IP 功能](use-multiple-node-pools.md#assign-a-public-ip-per-node-in-a-node-pool)。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create][az-group-create] 命令创建资源组。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

以下示例输出显示已成功创建资源组：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>创建 AKS 群集
若要运行支持具有*标准*SKU 的负载均衡器的 AKS 群集, 群集需要将*负载平衡器 SKU*参数设置为 "*标准*"。 创建群集时, 此参数将使用*标准*SKU 创建负载均衡器。 当你在群集上运行*LoadBalancer*服务时,*标准*的 SK 负载均衡器的配置将使用该服务的配置进行更新。 使用[az aks create][az-aks-create]命令创建名为*myAKSCluster*的 aks 群集。

> [!NOTE]
> *负载平衡器 sku*属性只能在创建群集时使用。 创建 AKS 群集后, 无法更改负载平衡器 SKU。 此外, 在单个群集中只能使用一种类型的负载均衡器 SKU。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。

## <a name="connect-to-the-cluster"></a>连接至群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli][az-aks-install-cli] 命令：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点的列表。

```azurecli-interactive
kubectl get nodes
```

以下示例输出显示在上一步创建的单个节点。 请确保节点的状态为 *Ready*：

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>验证群集是否使用*标准*SKU

使用[az aks show][az-aks-show]显示群集的配置。

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

验证*loadBalancerSku*属性是否显示为*标准*。

## <a name="use-the-load-balancer"></a>使用负载均衡器

若要在群集上使用负载均衡器, 请使用服务类型*LoadBalancer*创建服务清单。 若要显示负载均衡器的工作情况, 请创建另一个清单, 其中包含要在群集上运行的示例应用程序。 此示例应用程序通过负载均衡器公开, 并可通过浏览器进行查看。

创建一个名`sample.yaml`为的清单, 如以下示例中所示:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

上面的清单配置了两个部署: *azure-投票*和*azure 投票*。 若要配置使用负载均衡器公开的*azure 投票前*部署, 请创建一个名`standard-lb.yaml`为的清单, 如以下示例中所示:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

服务*azure 投票*使用*LOADBALANCER*类型在 AKS 群集上配置负载均衡器, 以连接到*azure 投票*的部署。

使用[kubectl apply][kubectl-apply]部署示例应用程序和负载均衡器, 并指定 YAML 清单的名称:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*标准*SKU 负载均衡器现已配置为公开示例应用程序。 使用[kubectl get][kubectl-get]查看*azure*的服务详细信息, 查看负载均衡器的公共 IP。 负载均衡器的公共 IP 地址显示在 "*外部 ip* " 列中。 IP 地址可能需要一到两分钟的时间才能从 *\<"挂起\>* " 更改为实际的外部 IP 地址, 如以下示例中所示:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

在浏览器中导航到公共 IP, 并验证是否看到该示例应用程序。 在上面的示例中, 公共 IP 为`52.179.23.131`。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> 你还可以将负载均衡器配置为内部负载均衡器, 而不是公开公共 IP。 若要将负载均衡器配置为内部`service.beta.kubernetes.io/azure-load-balancer-internal: "true"`负载均衡器, 请将作为批注添加到*LoadBalancer*服务。 可在[此处][internal-lb-yaml]查看示例 yaml 清单以及有关内部负载均衡器的更多详细信息。

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>清理标准 SKU 负载平衡器配置

若要删除示例应用程序和负载均衡器配置, 请使用[kubectl 删除][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
