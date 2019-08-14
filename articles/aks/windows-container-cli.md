---
title: 预览-在 Azure Kubernetes 服务 (AKS) 群集上创建 Windows Server 容器
description: 了解如何使用 Azure CLI 快速创建 Kubernetes 群集, 在 Azure Kubernetes Service (AKS) 中的 Windows Server 容器中部署应用程序。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: 305901007180cfb197cf5c0dfb338800449560a1
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68382033"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>预览-使用 Azure CLI 在 Azure Kubernetes Service (AKS) 群集上创建 Windows Server 容器

Azure Kubernetes 服务 (AKS) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 本文介绍如何使用 Azure CLI 部署 AKS 群集。 还可将 Windows Server 容器中的 ASP.NET 示例应用程序部署到群集。

此功能目前处于预览状态。

![浏览到 ASP.NET 示例应用程序的图像](media/windows-container/asp-net-sample-app.png)

本文假定你基本了解 Kubernetes 的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI, 本文要求运行 Azure CLI 版本2.0.61 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="before-you-begin"></a>开始之前

创建可运行 Windows Server 容器的群集后, 必须添加其他节点池。 稍后的步骤中介绍了添加其他节点池, 但首先需要启用一些预览功能。

> [!IMPORTANT]
> AKS 预览功能是自助服务, 选择加入。 提供这些项目是为了从我们的社区收集反馈和 bug。 在预览版中, 这些功能并不用于生产。 公共预览版中的功能低于 "最大努力" 支持。 仅在太平洋时区 (PST) 期间, AKS 技术支持团队提供协助。 有关其他信息, 请参阅以下支持文章:
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用 Windows Server 容器, 需要*aks* CLI 扩展版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展, 然后使用[az extension update][az-extension-update]命令检查是否有任何可用更新:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>注册 Windows 预览功能

若要创建可使用多个节点池并运行 Windows Server 容器的 AKS 群集, 请先在订阅上启用*WindowsPreview*功能标志。 *WindowsPreview*功能还使用多节点池群集和虚拟机规模集来管理 Kubernetes 节点的部署和配置。 使用[az feature register][az-feature-register]命令注册*WindowsPreview*功能标志, 如以下示例中所示:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 成功注册*WindowsPreview*功能标志后创建的任何 AKS 群集都将使用此预览版群集体验。 若要继续创建常规且完全受支持的群集, 请不要对生产订阅启用预览功能。 使用单独的测试或开发 Azure 订阅来测试预览功能。

完成注册需要几分钟时间。 使用[az feature list][az-feature-list]命令检查注册状态:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

当注册状态为`Registered`时, 按 ctrl-c 停止监视状态。  然后使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时, 有以下限制:

* 成功注册*WindowsPreview*后, 多个节点池可用于创建的群集。 如果为订阅注册了*MultiAgentpoolPreview*和*VMSSPreview*功能, 则还可以使用多个节点池。 在成功注册这些功能之前, 无法添加或管理已创建的现有 AKS 群集的节点池。
* 不能删除第一个节点池。

此功能处于预览阶段, 但以下附加限制适用:

* AKS 群集最多可以有8个节点池。
* AKS 群集在这八个节点池中最多可以有400个节点。
* Windows Server 节点池名称的限制为6个字符。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create][az-group-create] 命令创建资源组。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

> [!NOTE]
> 本文使用 Bash 语法作为本教程中的命令。
> 如果使用 Azure Cloud Shell, 请确保将 Cloud Shell 窗口左上角的下拉列表设置为**Bash**。

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

若要运行支持 Windows Server 容器的节点池的 AKS 群集, 群集需要使用使用[AZURE CNI][azure-cni-about] (advanced) 网络插件的网络策略。 若要详细了解如何计划所需的子网范围和网络注意事项, 请参阅[配置 AZURE CNI 网络][use-advanced-networking]。 使用[az aks create][az-aks-create]命令创建名为*myAKSCluster*的 aks 群集。 如果这些资源不存在, 此命令将创建必要的网络资源。
  * 群集配置有一个节点
  * *Windows 管理员密码*和*windows 管理员-用户名*参数为群集上创建的任何 windows Server 容器设置管理员凭据。

提供自己的安全*PASSWORD_WIN* (请注意, 本文中的命令已输入到 BASH shell 中):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.1 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> 如果出现密码验证错误, 请尝试在另一个区域中创建资源组。
> 然后尝试创建包含新资源组的群集。

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。

## <a name="add-a-windows-server-node-pool"></a>添加 Windows Server 节点池

默认情况下, 将使用可运行 Linux 容器的节点池创建 AKS 群集。 使用`az aks nodepool add`命令添加可运行 Windows Server 容器的其他节点池。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.1
```

上述命令将创建一个名为*npwin*的新节点池, 并将其添加到*myAKSCluster*中。 创建节点池以运行 Windows Server 容器时,*节点-vm 大小*的默认值为*Standard_D2s_v3*。 如果选择设置*节点-vm 大小*的参数, 请查看[受限 vm 大小][restricted-vm-sizes]的列表。 建议的最小大小为*Standard_D2s_v3*。 上面的命令还使用运行`az aks create`时创建的默认 vnet 中的默认子网。

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
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

## <a name="run-the-application"></a>运行应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本文中, 清单用于创建在 Windows Server 容器中运行 ASP.NET 示例应用程序所需的所有对象。 此清单包括用于 ASP.NET 示例应用程序的[Kubernetes 部署][kubernetes-deployment], 以及用于从 internet 访问应用程序的外部[Kubernetes 服务][kubernetes-service]。

ASP.NET 示例应用程序作为[.NET Framework 示例][dotnet-samples]的一部分提供并在 Windows Server 容器中运行。 AKS 要求 Windows Server 容器基于*Windows server 2019*或更高版本的映像。 Kubernetes 清单文件还必须定义[节点选择器][node-selector], 以便告知 AKS 群集在可运行 Windows Server 容器的节点上运行 ASP.NET 示例应用程序的 pod。

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
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
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

使用 [kubectl apply][kubectl-apply] 命令部署应用程序，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f sample.yaml
```

以下示例输出显示已成功创建部署和服务:

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

最初,*示例*服务的*外部 IP*显示为 "*挂起*"。

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看操作中的示例应用, 请打开 web 浏览器, 以访问服务的外部 IP 地址。

![浏览到 ASP.NET 示例应用程序的图像](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>删除群集

如果不再需要群集，可以使用 [az group delete][az-group-delete] 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。

## <a name="next-steps"></a>后续步骤

本文介绍了如何部署 Kubernetes 群集, 以及如何将 Windows Server 容器中的 ASP.NET 示例应用程序部署到该群集。 访问刚刚创建的群集的 [Kubernetes Web 仪表板][kubernetes-dashboard]。

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
