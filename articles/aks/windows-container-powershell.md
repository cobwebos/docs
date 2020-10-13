---
title: 使用 PowerShell 在 AKS 群集上创建 Windows Server 容器
description: 了解如何使用 PowerShell 在 Azure Kubernetes 服务 (AKS) 中的 Windows Server 容器中快速创建 Kubernetes 群集并部署应用程序。
services: container-service
ms.topic: article
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bf446c858e40014a4085721d646f819e08542064
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497879"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>使用 PowerShell 在 Azure Kubernetes 服务 (AKS) 群集上创建 Windows Server 容器

Azure Kubernetes 服务 (AKS) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 本文介绍如何使用 PowerShell 部署 AKS 群集。 还可将 Windows Server 容器中的 `ASP.NET` 示例应用程序部署到群集。

![浏览到 ASP.NET 示例应用程序的图像](media/windows-container-powershell/asp-net-sample-app.png)

本文假定你对 Kubernetes 概念有基本的了解。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell][install-azure-powershell]。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定的订阅 ID。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时存在以下限制：

* 无法删除第一个节点池。

Windows Server 节点池存在以下额外限制：

* AKS 群集最多可以包含 10 个节点池。
* AKS 群集的每个节点池最多可以包含 100 个节点。
* Windows Server 节点池名称具有 6 个字符的限制。

## <a name="create-a-resource-group"></a>创建资源组

[Azure 资源组](../azure-resource-manager/management/overview.md)是用于部署和管理 Azure 资源的逻辑组。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [New-AzResourceGroup][new-azresourcegroup] cmdlet 创建资源组。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

> [!NOTE]
> 本文使用 PowerShell 语法作为本教程的命令。 如果使用 Azure Cloud Shell，请确保将 Cloud Shell 窗口左上角的下拉列表设置为 PowerShell。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

以下示例输出显示已成功创建资源组：

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

使用 `ssh-keygen` 命令行实用程序生成 SSH 密钥对。 有关详细信息，请参阅[快速步骤：创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对](../virtual-machines/linux/mac-create-ssh-keys.md)。

若要运行支持 Windows Server 容器的节点池的 AKS 群集，群集需要采用使用 [Azure CNI][azure-cni-about]（高级）网络插件的网络策略。 有关帮助计划所需子网范围和网络注意事项的更多详细信息，请参阅[配置 Azure CNI 网络][use-advanced-networking]。 使用下面的 [New-AzAks][new-azaks] cmdlet 创建名为 myAKSCluster 的 AKS 群集。 下面的示例将创建必要的网络资源（如果这些资源不存在）。

> [!NOTE]
> 若要确保群集能够可靠运行，应至少在默认节点池运行 2 个节点。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzAKS -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -KubernetesVersion 1.16.7 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName akswinuser -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> 如果由于此区域不支持该版本而无法创建 AKS 群集群集，则可以使用 `Get-AzAksVersion -Location eastus` 命令查找此区域的受支持版本列表。

几分钟后，该命令将完成并返回有关群集的信息。 有时，群集预配的时间可能超过几分钟。 在这种情况下，最多需要 10 分钟。

## <a name="add-a-windows-server-node-pool"></a>添加 Windows Server 节点池

默认情况下，将使用可运行 Linux 容器的节点池创建 AKS 群集。 使用 `New-AzAksNodePool` cmdlet 添加可与 Linux 节点池一同运行 Windows Server 容器的节点池。

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -OsType Windows -Name npwin -KubernetesVersion 1.16.7
```

上述命令将创建名为 npwin 的新节点池，并将其添加到 myAKSCluster 。 创建节点池以运行 Windows Server 容器时，VmSize 的默认值为 Standard_D2s_v3 。 如果选择设置 VmSize 参数，请检查[受限 VM 大小][restricted-vm-sizes]的列表。 最小推荐大小为 Standard_D2s_v3。 前一个命令还使用运行 `New-AzAks` 时创建的默认 vnet 中的默认子网。

## <a name="connect-to-the-cluster"></a>连接至群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 要本地安装 `kubectl`，请使用 `Install-AzAksKubectl` cmdlet：

```azurepowershell-interactive
Install-AzAksKubectl
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [Import-AzAksCredential][import-azakscredential] cmdlet。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点列表。

```azurepowershell-interactive
kubectl get nodes
```

以下示例输出显示了群集中的所有节点。 请确保所有节点的状态为“就绪”：

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>运行应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本文中，清单用于创建在 Windows Server 容器中运行 ASP.NET 示例应用程序所需的所有对象。 此清单包括用于 ASP.NET 示例应用程序的 [Kubernetes 部署][kubernetes-deployment]，以及用于从 Internet 访问应用程序的外部 [Kubernetes 服务][kubernetes-service]。

ASP.NET 示例应用程序作为 [.NET Framework 示例][dotnet-samples]的一部分提供并在 Windows Server 容器中运行。 AKS 要求 Windows Server 容器基于 Windows Server 2019 或更高版本的映像。 Kubernetes 清单文件还必须定义[节点选择器][node-selector]，以指示 AKS 群集在可运行 Windows Server 容器的节点上运行 ASP.NET 示例应用程序的 Pod。

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

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

以下示例输出显示已成功创建部署和服务：

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。
此过程可能需要几分钟才能完成。 有时，预配服务所需的时间可能不止几分钟。 在这种情况下，最多需要 10 分钟。

若要监视进度，请将 [kubectl get service][kubectl-get] 命令与 `--watch` 参数配合使用。

```azurepowershell-interactive
kubectl get service sample --watch
```

最初，示例服务的 EXTERNAL-IP 显示为“挂起”  。

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 **EXTERNAL-IP** 地址从 **pending** 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看示例应用的实际效果，请打开 Web 浏览器并转到服务的外部 IP 地址。

![浏览到 ASP.NET 示例应用程序的图像](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> 如果尝试加载页面时收到连接超时，则应使用以下命令 `kubectl get pods --watch` 验证示例应用是否已准备就绪。 有时，当你的外部 IP 地址可用时，Windows 容器将无法启动。

## <a name="delete-cluster"></a>删除群集

如果不再需要群集，可以使用 [Remove-AzResourceGroup][remove-azresourcegroup] cmdlet 删除资源组、容器服务及所有相关资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="next-steps"></a>后续步骤

本文介绍了如何部署 Kubernetes 群集，以及如何将 Windows Server 容器中的 `ASP.NET` 示例应用程序部署到该群集。 访问已创建的群集的 [Kubernetes Web 仪表板][kubernetes-dashboard]。

若要详细了解 AKS 并演练部署示例的完整代码，请继续阅读“Kubernetes 群集”教程。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
