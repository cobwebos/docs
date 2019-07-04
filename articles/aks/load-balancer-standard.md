---
title: 预览-使用 Azure Kubernetes 服务 (AKS) 中的标准 SKU 负载均衡器
description: 了解如何使用标准 sku 负载均衡器公开服务与 Azure Kubernetes 服务 (AKS)。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476804"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>预览-使用 Azure Kubernetes 服务 (AKS) 中的标准 SKU 负载均衡器

若要向你的应用程序在 Azure Kubernetes 服务 (AKS) 提供访问权限，可以创建并使用 Azure 负载均衡器。 在 AKS 上运行的负载均衡器可用作内部或外部负载均衡器。 内部负载均衡使得 Kubernetes 服务仅在与 AKS 群集位于同一虚拟网络中运行的应用程序可以访问。 外部负载均衡器接收传入的一个或多个公共 Ip，并使 Kubernetes 服务可访问的外部使用公共 Ip。

Azure 负载均衡器以两种 SKU 提供：“基本”和“标准”   。 默认情况下*基本*SKU 时的服务清单用于在 AKS 上创建负载均衡器使用。 使用*标准*SKU 负载均衡器提供了其他特性和功能，例如更大的后端池大小和可用性区域。 务必了解之间的差异*标准*并*基本*选择要使用之前的负载均衡器。 一旦创建 AKS 群集时，不能更改为该群集的负载均衡器 SKU。 有关详细信息*基本*并*标准*Sku，请参阅[Azure 负载均衡器 SKU 的比较][azure-lb-comparison]。

本文介绍如何创建和使用与 Azure 负载均衡器*标准*SKU Azure Kubernetes 服务 (AKS)。

本文假定你基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息，请参阅[Kubernetes 的 Azure Kubernetes 服务 (AKS) 核心概念][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb]。

此功能目前处于预览状态。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您选择本地安装并使用 CLI，本文要求运行 Azure CLI 版本 2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>开始之前

AKS 群集服务主体必须有权管理网络资源，如果使用现有子网或资源组。 一般情况下，分配*网络参与者*到委派资源服务主体的角色。 有关权限的详细信息，请参阅[到其他 Azure 资源访问的委托 AKS][aks-sp]。

必须创建 AKS 群集，用于设置到负载均衡器 SKU*标准*而不是默认*基本*。 创建 AKS 群集包含在后续步骤中，但首先需要启用几个预览功能。

> [!IMPORTANT]
> AKS 预览版功能是自助服务的选择加入。 提供这些项目是为了从我们的社区收集反馈和 bug。 在预览版中，这些功能不是用于生产环境中使用。 公共预览版中的功能属于最大努力支持。 AKS 技术支持团队的协助营业时间太平洋时区 （太平洋标准时间） 仅将提供。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用 Azure 负载均衡器标准 SKU，需要*aks 预览版*CLI 扩展版本 0.4.1 或更高版本。 安装*aks 预览版*Azure CLI 扩展使用[az 扩展添加][az-extension-add]command, then check for any available updates using the [az extension update][az-extension-update]命令::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>注册 AKSAzureStandardLoadBalancer 预览功能

若要创建的 AKS 群集，可以使用与负载均衡器*标准*SKU，则必须启用*AKSAzureStandardLoadBalancer*功能在你的订阅上的标志。 *AKSAzureStandardLoadBalancer*功能还会使用*VMSSPreview*创建使用虚拟机规模集的群集时。 配置群集时，此功能提供最新的一组服务增强功能。 而不是必需的建议您启用*VMSSPreview*功能标志。

> [!CAUTION]
> 注册时对某一订阅功能，目前你无法取消注册该功能。 启用某些预览功能后，可能会对所有 AKS 群集，然后在订阅中创建使用默认值。 不要启用预览功能在生产订阅。 使用单独的订阅来测试预览功能和收集反馈。

注册*VMSSPreview*并*AKSAzureStandardLoadBalancer*使用功能标志[az 功能注册][az-feature-register]命令，在下面的示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> 创建已成功注册后任何 AKS 群集*VMSSPreview*或*AKSAzureStandardLoadBalancer*功能标志使用此预览群集体验。 若要继续创建正则、 完全受支持的群集，不要启用预览功能在生产订阅。 使用单独的测试或开发的 Azure 订阅进行测试预览功能。

状态显示为“已注册”需要几分钟时间  。 您可以检查注册状态使用[az 功能列表][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

准备就绪后，刷新的注册*Microsoft.ContainerService*使用的资源提供程序[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>限制

创建和管理支持使用负载均衡器的 AKS 群集时，以下限制适用*标准*SKU:

* 使用时*标准*SKU 负载均衡器，您必须允许公共地址并避免创建 bans IP 创建任何 Azure 策略。 AKS 群集会自动创建*标准*同一资源组中的 SKU 公共 IP 创建 AKS 群集，通常命名为与*MC_* 开头。 AKS 将分配到的公共 IP*标准*SKU 负载均衡器。 需要允许从 AKS 群集的出口流量的公共 IP。 此公共 ip 地址也需要维护的控制平面和代理节点以及有关与以前版本的 AKS 保持兼容性之间的连接。
* 使用时*标准*SKU 负载均衡器，必须使用 Kubernetes 版本 1.13.5 或更高版本。

虽然此功能处于预览状态，下面的其他限制适用：

* 使用时*标准*在 AKS 中的负载均衡器 SKU，不能将出口自己公共 IP 地址设置为负载均衡器。 必须使用 AKS 将分配给负载均衡器的 IP 地址。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create][az-group-create] 命令创建资源组。

以下示例在“eastus”  位置创建名为“myResourceGroup”  的资源组。

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
若要运行的 AKS 群集，支持使用负载均衡器*标准*SKU，需要设置你的群集*负载均衡器 sku*参数*标准*。 此参数创建负载平衡器具有*标准*SKU 创建群集时。 运行时*LoadBalancer*上的群集，配置服务*标准*SK 负载均衡器更新与服务的配置。 使用[az aks 创建][az-aks-create]命令创建名为 AKS 群集*myAKSCluster*。

> [!NOTE]
> *负载均衡器 sku*属性只能在创建群集时。 创建 AKS 群集后，无法更改负载均衡器 SKU。 此外，只能使用一种类型的单个群集中的负载均衡器 SKU。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。

## <a name="connect-to-the-cluster"></a>连接至群集

若要管理 Kubernetes 群集，请使用[kubectl][kubectl]，Kubernetes 命令行客户端。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要安装`kubectl`本地，使用[az aks 安装 cli][az-aks-install-cli]命令：

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
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>验证你的群集使用*标准*SKU

使用[az aks 显示][az-aks-show]以显示群集的配置。

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

验证是否*loadBalancerSku*属性显示为*标准*。

## <a name="use-the-load-balancer"></a>使用负载均衡器

若要在群集上使用负载均衡器，可使用的服务类型创建的服务清单*负载均衡器*。 若要显示负载均衡器使用，创建另一个清单，示例应用程序，在群集上运行。 此示例应用程序通过负载均衡器公开，可以通过浏览器查看。

创建名为的清单`sample.yaml`如下面的示例中所示：

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

上面的清单将会配置两个部署： *azure 投票前端*并*azure 投票回*。 若要配置*azure 投票前端*部署要公开使用负载均衡器，创建名为的清单`standard-lb.yaml`如下面的示例中所示：

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

该服务*azure 投票前端*使用*负载均衡器*若要连接到在 AKS 群集上配置负载均衡器的类型*azure-vote-front-* 部署。

部署示例应用程序和负载均衡器使用[kubectl 应用][kubectl-apply]和指定的 YAML 清单名称：

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*标准*SKU 负载均衡器现在已配置为公开的示例应用程序。 查看服务的详细信息*azure 投票前端*使用[kubectl 获取][kubectl-get]若要查看负载均衡器的公共 IP。 负载均衡器的公共 IP 地址所示*外部 IP*列。 可能需要一分钟或两个用于从更改的 IP 地址 *\<挂起\>* 到实际外部 IP 地址，如下面的示例中所示：

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

导航到浏览器中的公共 IP 并验证能看到的示例应用程序。 在上述示例中，公共 IP 是`52.179.23.131`。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> 可以配置内部负载均衡器并不会公开一个公共 IP。 若要将负载均衡器配置为内部，添加`service.beta.kubernetes.io/azure-load-balancer-internal: "true"`作为的批注*负载均衡器*服务。 您可以看到清单以及更多详细信息的内部负载均衡器示例 yaml[此处][internal-lb-yaml]。

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>清理标准 SKU 负载均衡器配置

若要删除的示例应用程序和负载均衡器配置，请使用[kubectl 删除][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>后续步骤

了解有关在 Kubernetes 服务[Kubernetes 服务文档][kubernetes-services]。

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
