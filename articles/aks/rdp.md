---
title: RDP 进入 Azure 库伯奈斯服务 （AKS） 群集 Windows 服务器节点
description: 了解如何使用 Azure Kubernetes 服务 （AKS） 群集 Windows 服务器节点创建 RDP 连接，以便执行故障排除和维护任务。
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 897504aa9902d0feaf4245c719d3a4a3c6fd2241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594475"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>使用 RDP 连接到 Azure 库伯奈斯服务 （AKS） 群集 Windows 服务器节点，以便进行维护或故障排除

在 Azure 库伯内斯服务 （AKS） 群集的整个生命周期中，您可能需要访问 AKS Windows 服务器节点。 进行这种访问的原因包括维护、日志收集或其他故障排除操作。 您可以使用 RDP 访问 AKS Windows 服务器节点。 或者，如果要使用 SSH 访问 AKS Windows 服务器节点，并且可以访问群集创建期间使用的相同密钥对，则可以按照 SSH 中的步骤[进入 Azure Kubernetes 服务 （AKS） 群集节点][ssh-steps]。 出于安全考虑，AKS 节点不会在 Internet 中公开。

Windows 服务器节点支持当前处于 AKS 中的预览版。

本文介绍如何使用 AKS 节点的专用 IP 地址创建 RDP 连接。

## <a name="before-you-begin"></a>开始之前

本文假定您具有具有 Windows 服务器节点的现有 AKS 群集。 如果需要 AKS 群集，请参阅有关[使用 Azure CLI 使用 Windows 容器创建 AKS 群集][aks-windows-cli]的文章。 您需要要排除故障的 Windows 服务器节点的 Windows 管理员用户名和密码。 您还需要一个RDP客户端，如[微软远程桌面][rdp-mac]。

您还需要 Azure CLI 版本 2.0.61 或更高版本安装和配置。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>将虚拟机部署到与群集相同的子网

AKS 群集的 Windows 服务器节点没有外部可访问的 IP 地址。 要建立 RDP 连接，可以将具有可公开访问的 IP 地址的虚拟机部署到与 Windows Server 节点相同的子网。

下面的示例在*myResourceGroup 资源*组中创建名为*myVM*的虚拟机。

首先，获取 Windows Server 节点池使用的子网。 要获取子网 ID，您需要子网的名称。 要获取子网的名称，您需要 vnet 的名称。 通过查询群集的网络列表来获取 vnet 名称。 要查询群集，您需要其名称。 通过在 Azure 云外壳中运行以下内容，可以获取所有这些功能：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

现在，您已经SUBNET_ID了，请在同一 Azure 云外壳窗口中运行以下命令以创建 VM：

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

以下示例输出显示 VM 已成功创建并显示虚拟机的公共 IP 地址。

```console
13.62.204.18
```

记录虚拟机的公共 IP 地址。 您将在后面的步骤中使用此地址。

## <a name="allow-access-to-the-virtual-machine"></a>允许访问虚拟机

默认情况下，AKS 节点池子网受 NSG（网络安全组）保护。 要访问虚拟机，您必须在 NSG 中启用访问权限。

> [!NOTE]
> NSG 由 AKS 服务控制。 您对 NSG 的任何更改将随时被控制平面覆盖。
>

首先，获取 nsg 的资源组和 nsg 名称，以将规则添加到：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

然后，创建 NSG 规则：

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>获取节点地址

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli][az-aks-install-cli] 命令：
    
```azurecli-interactive
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

使用[kubectl get][kubectl-get]命令列出 Windows 服务器节点的内部 IP 地址：

```console
kubectl get nodes -o wide
```

以下示例输出显示群集中所有节点（包括 Windows Server 节点）的内部 IP 地址。

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

记录要排除故障的 Windows 服务器节点的内部 IP 地址。 您将在后面的步骤中使用此地址。

## <a name="connect-to-the-virtual-machine-and-node"></a>连接到虚拟机和节点

连接到您之前使用 RDP 客户端（如[Microsoft 远程桌面][rdp-mac]）创建的虚拟机的公共 IP 地址。

![使用 RDP 客户端连接到虚拟机的图像](media/rdp/vm-rdp.png)

连接到虚拟机后，请连接到要使用虚拟机内 RDP 客户端进行故障排除的 Windows Server 节点*的内部 IP 地址*。

![使用 RDP 客户端连接到 Windows 服务器节点的图像](media/rdp/node-rdp.png)

您现在已连接到 Windows 服务器节点。

![Windows 服务器节点中的 cmd 窗口图像](media/rdp/node-session.png)

您现在可以在*cmd*窗口中运行任何故障排除命令。 由于 Windows 服务器节点使用 Windows 服务器核心，因此当您通过 RDP 连接到 Windows Server 节点时，没有完整的 GUI 或其他 GUI 工具。

## <a name="remove-rdp-access"></a>删除 RDP 访问权限

完成后，退出 RDP 连接到 Windows 服务器节点，然后将 RDP 会话退出虚拟机。 退出两个 RDP 会话后，使用 az [vm 删除][az-vm-delete]命令删除虚拟机：

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

NSG 规则：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>后续步骤

如果需要其他故障排除数据，可以[查看库伯奈斯主节点日志][view-master-logs]或[Azure 监视器][azure-monitor-containers]。

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
