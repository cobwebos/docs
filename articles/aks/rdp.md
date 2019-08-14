---
title: RDP 到 Azure Kubernetes Service (AKS) 群集 Windows Server 节点
description: 了解如何创建与 Azure Kubernetes Service (AKS) 群集 Windows Server 节点的 RDP 连接, 以便进行故障排除和维护任务。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614566"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>通过 RDP 连接到 Azure Kubernetes Service (AKS) 群集 Windows Server 节点进行维护或故障排除

在 Azure Kubernetes 服务 (AKS) 群集的整个生命周期内, 可能需要访问 AKS Windows Server 节点。 进行这种访问的原因包括维护、日志收集或其他故障排除操作。 你可以使用 RDP 访问 AKS Windows Server 节点。 或者, 如果你想要使用 SSH 访问 AKS Windows Server 节点, 并且你有权访问在群集创建过程中使用的相同的密钥, 则可以按照 SSH 中的步骤连接[到 Azure Kubernetes Service (AKS) 群集节点][ssh-steps]。 出于安全考虑，AKS 节点不会在 Internet 中公开。

Windows Server 节点支持目前在 AKS 中以预览版提供。

本文介绍如何使用 AKS 节点通过其专用 IP 地址创建 RDP 连接。

## <a name="before-you-begin"></a>开始之前

本文假设你有一个包含 Windows Server 节点的现有 AKS 群集。 如果需要 AKS 群集, 请参阅[使用 Azure CLI 创建包含 Windows 容器的 AKS 群集][aks-windows-cli]一文。 你需要用于排查 Windows Server 节点的 Windows 管理员用户名和密码。 还需要一个 RDP 客户端, 例如[Microsoft 远程桌面][rdp-mac]。

还需要安装并配置 Azure CLI 版本2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>将虚拟机部署到与群集相同的子网

AKS 群集的 Windows Server 节点没有外部访问的 IP 地址。 若要进行 RDP 连接, 可以将具有可公开访问的 IP 地址的虚拟机部署到与你的 Windows Server 节点相同的子网。

以下示例在*myResourceGroup*资源组中创建名为*myVM*的虚拟机。

首先, 获取 Windows Server 节点池使用的子网。 若要获取子网 id, 需要子网名称。 若要获取子网的名称, 需要 vnet 的名称。 通过向群集查询其网络列表来获取 vnet 名称。 若要查询群集, 需要该群集的名称。 可以通过在 Azure Cloud Shell 中运行以下内容来获取所有这些内容:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

现在, 你已拥有 SUBNET_ID, 请在同一 Azure Cloud Shell 窗口中运行以下命令以创建 VM:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

以下示例输出显示 VM 已成功创建, 并显示虚拟机的公共 IP 地址。

```console
13.62.204.18
```

记录虚拟机的公共 IP 地址。 稍后的步骤中会用到此地址。

## <a name="get-the-node-address"></a>获取节点地址

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli][az-aks-install-cli] 命令：
    
```azurecli-interactive
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

使用[kubectl get][kubectl-get]命令列出 Windows Server 节点的内部 IP 地址:

```console
kubectl get nodes -o wide
```

以下示例输出显示群集中所有节点的内部 IP 地址, 包括 Windows Server 节点。

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

记录要进行故障排除的 Windows Server 节点的内部 IP 地址。 稍后的步骤中会用到此地址。

## <a name="connect-to-the-virtual-machine-and-node"></a>连接到虚拟机和节点

使用 RDP 客户端 (如[Microsoft 远程桌面][rdp-mac]) 连接到之前创建的虚拟机的公共 IP 地址。

![使用 RDP 客户端连接到虚拟机的映像](media/rdp/vm-rdp.png)

连接到虚拟机后, 请使用虚拟机中的 RDP 客户端连接到要进行故障排除的 Windows Server 节点的*内部 IP 地址*。

![使用 RDP 客户端连接到 Windows Server 节点的图像](media/rdp/node-rdp.png)

你现在已连接到 Windows Server 节点。

![Windows Server 节点中 cmd 窗口的图像](media/rdp/node-session.png)

你现在可以在*cmd*窗口中运行任何疑难解答命令。 由于 Windows Server 节点使用 Windows Server Core, 因此在通过 RDP 连接到 Windows Server 节点时, 没有完整的 GUI 或其他 GUI 工具。

## <a name="remove-rdp-access"></a>删除 RDP 访问

完成后, 退出与 Windows Server 节点的 RDP 连接, 并退出与虚拟机的 RDP 会话。 退出这两个 RDP 会话后, 请删除包含[az vm delete][az-vm-delete]命令的虚拟机:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>后续步骤

如果需要其他故障排除数据, 可以[查看 Kubernetes 主节点日志][view-master-logs]或[Azure Monitor][azure-monitor-containers]。

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
