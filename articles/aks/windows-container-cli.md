---
title: 在 Azure Kubernetes 服务（AKS）群集上创建 Windows Server 容器
description: 了解如何使用 Azure CLI 快速创建 Kubernetes 群集，在 Azure Kubernetes Service （AKS）中的 Windows Server 容器中部署应用程序。
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 28925961ea3b99f939ac650d54b5dcece2551f59
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926589"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>使用 Azure CLI 在 Azure Kubernetes Service （AKS）群集上创建 Windows Server 容器

Azure Kubernetes 服务 (AKS) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 本文介绍如何使用 Azure CLI 部署 AKS 群集。 还可将 Windows Server 容器中的 ASP.NET 示例应用程序部署到群集。

![浏览到 ASP.NET 示例应用程序的图像](media/windows-container/asp-net-sample-app.png)

本文假定你基本了解 Kubernetes 的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时存在以下限制：

* 不能删除第一个节点池。

以下其他限制适用于 Windows Server 节点池：

* AKS 群集最多可以有10个节点池。
* AKS 群集的每个节点池中最多可以有100个节点。
* Windows Server 节点池名称的限制为6个字符。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create][az-group-create] 命令创建资源组。

以下示例在“eastus”  位置创建名为“myResourceGroup”  的资源组。

> [!NOTE]
> 本文使用 Bash 语法作为本教程中的命令。
> 如果使用 Azure Cloud Shell，请确保将 Cloud Shell 窗口左上角的下拉列表设置为**Bash**。

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

若要运行支持 Windows Server 容器的节点池的 AKS 群集，群集需要使用使用[AZURE CNI][azure-cni-about] （advanced）网络插件的网络策略。 若要详细了解如何计划所需的子网范围和网络注意事项，请参阅[配置 AZURE CNI 网络][use-advanced-networking]。 使用下面的[az aks create][az-aks-create]命令创建名为*myAKSCluster*的 aks 群集。 如果这些资源不存在，此命令将创建必要的网络资源。

> [!NOTE]
> 为确保群集可靠运行，应在默认节点池中至少运行 2（两）个节点。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.16.7 \
    --generate-ssh-keys \
    --network-plugin azure
```

> [!Note]
> 如果无法创建 AKS 群集，因为在此区域中不支持该版本，则可使用 [az AKS get-help--location eastus] 命令查找此区域的受支持版本列表。

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。 有时，可能需要花费超过几分钟的时间才能设置群集。 在这种情况下，最多允许10分钟。

## <a name="add-a-windows-server-node-pool"></a>添加 Windows Server 节点池

默认情况下，将使用可运行 Linux 容器的节点池创建 AKS 群集。 使用`az aks nodepool add`命令添加可在 Linux 节点池旁运行 Windows Server 容器的其他节点池。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.16.7
```

上述命令将创建一个名为*npwin*的新节点池，并将其添加到*myAKSCluster*中。 创建节点池以运行 Windows Server 容器时，*节点-vm 大小*的默认值为*Standard_D2s_v3*。 如果选择设置*节点-vm 大小*的参数，请查看[受限 vm 大小][restricted-vm-sizes]的列表。 建议的最小大小为*Standard_D2s_v3*。 上面的命令还使用运行`az aks create`时创建的默认 vnet 中的默认子网。

## <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli][az-aks-install-cli] 命令：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点列表。

```console
kubectl get nodes
```

以下示例输出显示了群集中的所有节点。 请确保所有节点的状态均为 "*就绪*"：

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>运行应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本文中，清单用于创建在 Windows Server 容器中运行 ASP.NET 示例应用程序所需的所有对象。 此清单包括用于 ASP.NET 示例应用程序的[Kubernetes 部署][kubernetes-deployment]，以及用于从 internet 访问应用程序的外部[Kubernetes 服务][kubernetes-service]。

ASP.NET 示例应用程序作为[.NET Framework 示例][dotnet-samples]的一部分提供并在 Windows Server 容器中运行。 AKS 要求 Windows Server 容器基于*Windows server 2019*或更高版本的映像。 Kubernetes 清单文件还必须定义[节点选择器][node-selector]，以便告知 AKS 群集在可运行 Windows Server 容器的节点上运行 ASP.NET 示例应用程序的 pod。

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

```console
kubectl apply -f sample.yaml
```

以下示例输出显示已成功创建部署和服务：

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。 有时，可能需要花费超过几分钟的时间来预配服务。 在这种情况下，最多允许10分钟。

若要监视进度，请将 [kubectl get service][kubectl-get] 命令与 `--watch` 参数配合使用。

```console
kubectl get service sample --watch
```

最初，*示例*服务的*外部 IP*显示为 "*挂起*"。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看操作中的示例应用，请打开 web 浏览器，以访问服务的外部 IP 地址。

![浏览到 ASP.NET 示例应用程序的图像](media/windows-container/asp-net-sample-app.png)

> [!Note]
> 如果尝试加载页面时收到连接超时，则应使用以下命令验证示例应用是否已准备好 [kubectl get pod--watch]。 有时，在您的外部 IP 地址可用时，windows 容器将不会启动。

## <a name="delete-cluster"></a>删除群集

如果不再需要群集，可以使用 [az group delete][az-group-delete] 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="next-steps"></a>后续步骤

本文介绍了如何部署 Kubernetes 群集，以及如何将 Windows Server 容器中的 ASP.NET 示例应用程序部署到该群集。 为你刚才创建的群集[访问 Kubernetes Web 仪表板][kubernetes-dashboard]。

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
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
