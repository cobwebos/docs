---
title: 预览-在 Azure Kubernetes 服务 (AKS) 群集上创建一个 Windows Server 容器
description: 了解如何快速创建 Kubernetes 群集，部署在 Windows Server 容器在 Azure Kubernetes 服务 (AKS) 使用 Azure CLI 中的应用程序。
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: twhitney
ms.openlocfilehash: a9887e923358b5658a365b5cfc88759eca2501e0
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303553"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>预览-使用 Azure CLI 的 Azure Kubernetes 服务 (AKS) 群集上创建 Windows Server 容器

Azure Kubernetes 服务 (AKS) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 在本文中，你将部署 AKS 群集使用 Azure CLI。 您还将在 Windows Server 容器中的 ASP.NET 示例应用程序部署到群集。

此功能目前处于预览状态。

![浏览到 ASP.NET 示例应用程序的映像](media/windows-container/asp-net-sample-app.png)

本文假设基本了解 Kubernetes 概念。 有关详细信息，请参阅[Kubernetes 核心概念 Azure Kubernetes 服务 (AKS)][kubernetes-concepts]。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您选择本地安装并使用 CLI，本文要求运行 Azure CLI 版本 2.0.61 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="before-you-begin"></a>开始之前

在创建群集，可以运行 Windows Server 容器后，必须添加其他节点池。 在后续步骤中，介绍了添加其他节点池，但首先需要启用几个预览功能。

> [!IMPORTANT]
> AKS 预览版功能是自助服务的选择加入。 提供这些项目是为了从我们的社区收集反馈和 bug。 在预览版中，这些功能不是用于生产环境中使用。 公共预览版中的功能属于最大努力支持。 AKS 技术支持团队的协助营业时间太平洋时区 （太平洋标准时间） 仅将提供。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展
    
CLI 命令来创建和管理多个节点池均位于*aks 预览版*CLI 扩展。 安装*aks 预览版*Azure CLI 扩展使用[az 扩展添加][az-extension-add]命令，如下面的示例中所示：

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 如果您先前安装了*aks 预览版*扩展，安装任何可用更新使用`az extension update --name aks-preview`命令。

### <a name="register-windows-preview-feature"></a>注册 Windows 预览功能

若要创建的 AKS 群集，可以使用多个节点池和运行 Windows Server 容器，请先启用*WindowsPreview*功能在你的订阅上的标志。 *WindowsPreview*功能还使用多节点池群集和虚拟机规模集来管理部署和 Kubernetes 节点的配置。 注册*WindowsPreview*使用功能标志[az 功能注册][az-feature-register]命令，在下面的示例所示：

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 创建已成功注册后任何 AKS 群集*WindowsPreview*功能标志使用此预览群集体验。 若要继续创建正则、 完全受支持的群集，不要启用预览功能在生产订阅。 使用单独的测试或开发的 Azure 订阅进行测试预览功能。

需要几分钟才能完成注册。 注册状态使用检查[az 功能列表][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

注册状态时`Registered`，按 Ctrl + C 来停止监视状态。  然后刷新的注册*Microsoft.ContainerService*使用的资源提供程序[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>限制

创建和管理 AKS 群集中以支持多个节点池时，应考虑以下限制：

* 多个节点池是可用于创建已成功注册后的群集*WindowsPreview*。 注册多个节点池还有*MultiAgentpoolPreview*并*VMSSPreview*功能为你的订阅。 无法添加或管理现有之前已成功注册这些功能创建的 AKS 群集与节点池。
* 无法删除第一个节点池。

虽然此功能处于预览状态，下面的其他限制适用：

* AKS 群集可以具有最多八个节点池。
* AKS 群集可以在这些八个节点的池之间具有最多 400 节点。
* Windows Server 节点池名称的限制为 6 个字符。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create][az-group-create] 命令创建资源组。

以下示例在“eastus”  位置创建名为“myResourceGroup”  的资源组。

> [!NOTE]
> 本文在本教程中使用的所有命令的 Bash 语法。
> 如果使用 Azure Cloud Shell，请确保在 Cloud Shell 窗口的左上方的下拉列表设置为**Bash**。

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

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

若要运行支持 Windows Server 容器的节点池的 AKS 群集，群集需要使用使用的网络策略[Azure CNI][azure-cni-about] (advanced) network plugin. For more detailed information to help plan out the required subnet ranges and network considerations, see [configure Azure CNI networking][use-advanced-networking]。 使用[az aks 创建][az aks 创建]命令以创建名为 AKS 群集*myAKSCluster*。 如果它们不存在，此命令将创建必要的网络资源。
  * 使用一个节点群集进行配置
  * *Windows 管理员密码*并*windows 管理员用户名*参数设置在群集上创建任何 Windows Server 容器的管理员凭据。

提供你自己的安全*PASSWORD_WIN* （请记住 BASH shell 中，输入在本文中的命令）：

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> 如果遇到密码验证错误，请尝试在另一个区域中创建资源组。
> 然后尝试使用新的资源组创建群集。

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。

## <a name="add-a-windows-server-node-pool"></a>添加 Windows Server 节点池

默认情况下，AKS 群集被创建可运行 Linux 容器的节点池。 使用`az aks nodepool add`命令，将添加的其他节点池，可以运行 Windows Server 容器。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

上述命令将创建一个名为的新节点池*npwin*并将其添加到*myAKSCluster*。 创建节点池，以运行 Windows Server 容器时的默认值为*节点 vm 大小*是*Standard_D2s_v3*。 如果你选择设置*节点 vm 大小*参数，请检查的列表[受限制的 VM 大小][restricted-vm-sizes]。 最小建议大小是*Standard_D2s_v3*。 上述命令还在运行时创建的默认 vnet 中使用的默认子网`az aks create`。

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
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>运行应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本文中，清单用于创建在 Windows Server 容器中运行 ASP.NET 示例应用程序所需的所有对象。 此清单包括[Kubernetes 部署][kubernetes-deployment]for the ASP.NET sample application and an external [Kubernetes service][kubernetes-service]从 internet 访问应用程序。

作为的一部分提供的 ASP.NET 示例应用程序[.NET Framework 示例][dotnet-samples]和 Windows Server 容器中运行。 AKS 需要 Windows Server 容器的映像为基础*Windows Server 2019*或更高版本。 此外必须定义清单文件的 Kubernetes[节点选择器][node-selector]告诉 AKS 群集可运行 Windows Server 容器的节点上运行 ASP.NET 示例应用程序的 pod。

创建名为 `sample.yaml` 的文件，并将其复制到以下 YAML 定义中。 如果使用 Azure Cloud Shell，则可以使用 `vi` 或 `nano` 来创建此文件，就像在虚拟或物理系统中操作一样：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

部署应用程序中使用[kubectl 应用][kubectl-apply]命令并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f sample.yaml
```

以下示例输出显示了部署和服务已成功创建：

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。

若要监视进度，请将 [kubectl get service][kubectl-get] 命令与 `--watch` 参数配合使用。

```azurecli-interactive
kubectl get service sample --watch
```

最初*EXTERNAL-IP*有关*示例*服务显示为*挂起*。

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看操作中的示例应用程序，请打开 web 浏览器到你的服务的外部 IP 地址。

![浏览到 ASP.NET 示例应用程序的映像](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>删除群集

当不再需要群集时，使用[az 组删除][az-group-delete]命令删除资源组、 容器服务和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅[AKS 服务主体的注意事项和删除][sp-delete]。

## <a name="next-steps"></a>后续步骤

在本文中，将部署了 Kubernetes 群集，并向其部署在 Windows Server 容器中的 ASP.NET 示例应用程序。 [访问 Kubernetes web 仪表板][kubernetes-dashboard]为刚创建的群集。

若要详细了解 AKS 并演练部署示例的完整代码，请继续阅读“Kubernetes 群集”教程。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
