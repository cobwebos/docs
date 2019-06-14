---
title: 通过 rdp 连接到 Azure Kubernetes 服务 (AKS) 群集 Windows Server 节点
description: 了解如何使用 Azure Kubernetes 服务 (AKS) 群集进行故障排除和维护任务的 Windows 服务器节点创建的 RDP 连接。
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: twhitney
ms.openlocfilehash: 11f6869d4d5a2ee0ef2e986ee8268c7a001ea015
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688629"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>通过 RDP 连接到 Azure Kubernetes 服务 (AKS) 群集 Windows Server 节点以进行维护或故障排除

在 Azure Kubernetes 服务 (AKS) 群集的整个生命周期，可能需要访问 AKS Windows Server 节点。 进行这种访问的原因包括维护、日志收集或其他故障排除操作。 您可以访问使用 RDP AKS Windows 服务器节点。 此外，如果你想要使用 SSH 访问 AKS Windows 服务器节点，并且你有权创建群集期间使用同一密钥对，您可以按照中的步骤[通过 ssh 登录到 Azure Kubernetes 服务 (AKS) 群集节点][ssh-steps]. 出于安全考虑，AKS 节点不会在 Internet 中公开。

Windows Server 节点支持目前处于预览状态，在 AKS 中。

本文介绍如何创建与 AKS 节点使用其专用 IP 地址的 RDP 连接。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集与 Windows 的服务器节点。 如果需要 AKS 群集，请参阅文章[使用 Windows 容器使用 Azure CLI 创建 AKS 群集][aks-windows-cli]。 为你想要进行故障排除在 Windows Server 节点需要 Windows 管理员用户名和密码。 您还需要 RDP 客户端，如[Microsoft 远程桌面][rdp-mac]。

您还需要 Azure CLI 版本 2.0.61 或更高版本安装和配置。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>将虚拟机部署到你的群集位于同一子网

在 AKS 群集的 Windows Server 节点没有从外部访问的 IP 地址。 若要使 RDP 连接，可以将具有一个可公开访问的 IP 地址的虚拟机部署到 Windows Server 节点位于同一子网中。

下面的示例创建名为的虚拟机*myVM*中*myResourceGroup*资源组。

首先，获取你的 Windows 服务器节点池使用的子网。 若要获取的子网 id，您需要的子网的名称。 若要获取的子网的名称，您需要的 vnet 的名称。 通过查询群集的网络及其列表以获取 vnet 名称。 若要查询群集，您需要其名称。 你可以获取所有通过 Azure Cloud Shell 中运行以下这些：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

现在，已 SUBNET_ID，以创建 VM 在同一 Azure Cloud Shell 窗口中运行以下命令：

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

以下示例输出显示 VM 已成功创建，并显示虚拟机的公共 IP 地址。

```console
13.62.204.18
```

记录虚拟机的公共 IP 的地址。 在后面的步骤，将使用此地址。

## <a name="get-the-node-address"></a>获取节点地址

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli][az-aks-install-cli] 命令：
    
```azurecli-interactive
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

列出使用的 Windows Server 节点的内部 IP 地址[kubectl get] [ kubectl-get]命令：

```console
kubectl get nodes -o wide
```

以下示例输出显示在该群集，包括 Windows Server 节点中的所有节点的内部 IP 地址。

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

记录你想要进行故障排除在 Windows Server 节点的内部 IP 地址。 在后面的步骤，将使用此地址。

## <a name="connect-to-the-virtual-machine-and-node"></a>连接到虚拟机和节点

连接到虚拟机之前如使用 RDP 客户端创建的公共 IP 地址[Microsoft 远程桌面][rdp-mac]。

![使用连接到虚拟机的 RDP 客户端的图像](media/rdp/vm-rdp.png)

你已连接到你的虚拟机后，连接到*内部 IP 地址*你想要使用 RDP 客户端从虚拟机中的进行故障排除的 Windows Server 节点。

![连接到使用 RDP 客户端在 Windows Server 节点的映像](media/rdp/node-rdp.png)

您现在连接到您的 Windows 服务器节点。

![在 Windows Server 节点中的 cmd 窗口的图像](media/rdp/node-session.png)

现在可以在运行故障排除的任何命令*cmd*窗口。 Windows Server 节点都使用 Windows Server Core，因为存在不完整的 GUI 或其他 GUI 工具通过 RDP 连接到 Windows Server 节点时。

## <a name="remove-rdp-access"></a>删除 RDP 访问权限

完成后，退出 RDP 连接到的 Windows 服务器节点，然后退出虚拟机的 RDP 会话。 退出这两个 RDP 会话后，删除与该虚拟机[az vm 删除][ az-vm-delete]命令：

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>后续步骤

如果您需要其他故障排除数据，则可以[查看 Kubernetes 主节点日志][ view-master-logs]或[Azure Monitor][azure-monitor-containers]。

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
