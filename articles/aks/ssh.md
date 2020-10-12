---
title: 通过 SSH 登录到 Azure Kubernetes 服务 (AKS) 群集节点
description: 了解如何与 Azure Kubernetes 服务 (AKS) 群集节点建立 SSH 连接，以完成故障排除和维护任务。
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 50a52584618e505aa2ae7bd9ed7e0a9f6bc330a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015606"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>使用 SSH 连接到 Azure Kubernetes 服务 (AKS) 群集节点以进行维护或故障排除

在 Azure Kubernetes 服务 (AKS) 群集的整个生命周期内，可能需要访问 AKS 节点。 进行这种访问的原因包括维护、日志收集或其他故障排除操作。 可以使用 SSH 访问 AKS 节点（包括 Windows Server 节点）。 还可以[使用远程桌面协议 (RDP) 连接功能连接到 Windows Server 节点][aks-windows-rdp]。 出于安全考虑，AKS 节点不会在 Internet 中公开。 若要通过 SSH 连接到 AKS 节点，需使用专用 IP 地址。

本文介绍如何使用 AKS 节点的专用 IP 地址来与它们建立 SSH 连接。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

默认情况下，在创建 AKS 群集时会获取或生成 SSH 密钥，然后将其添加到节点。 本文介绍如何指定与创建 AKS 群集时使用的 SSH 密钥不同的 SSH 密钥。 此外介绍如何确定节点的专用 IP 地址，并使用 SSH 连接到该节点。 如果不需要指定不同的 SSH 密钥，则可以跳过将 SSH 公钥添加到节点的步骤。

本文假设你已有一个 SSH 密钥。 可以使用 [macOS 或 Linux][ssh-nix] 或 [Windows][ssh-windows] 创建 SSH 密钥。 如果使用 PuTTY Gen 来创建密钥对，请在保存密钥对时使用 OpenSSH 格式而不是默认的 PuTTy 私钥格式（.ppk 文件）。

还需安装并配置 Azure CLI 2.0.64 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>配置基于虚拟机规模集的 AKS 群集以进行 SSH 访问

若要配置基于虚拟机规模集的 SSH 访问，请找到群集的虚拟机规模集名称，并将 SSH 公钥添加到该规模集。

使用 [az aks show][az-aks-show] 命令获取 AKS 群集的资源组名称，然后使用 [az vmss list][az-vmss-list] 命令获取规模集的名称。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

以上示例将 *myResourceGroup* 中 *myAKSCluster* 的群集资源组名称分配到 *CLUSTER_RESOURCE_GROUP*。 然后，该示例使用 *CLUSTER_RESOURCE_GROUP* 列出规模集名称并将其分配到 *SCALE_SET_NAME*。

> [!IMPORTANT]
> 此时，应该使用 Azure CLI 仅更新基于虚拟机规模集的 AKS 群集的 SSH 密钥。
> 
> 对于 Linux 节点，目前只能使用 Azure CLI 添加 SSH 密钥。 如果要使用 SSH 连接到 Windows Server 节点，则在创建 AKS 群集时使用提供的 SSH 密钥，并跳过下一组添加 SSH 公钥的命令。 仍需要要排除故障的节点的 IP 地址，该地址将在此部分的最后一个命令中显示。 此外，可以[使用远程桌面协议 (RDP) 连接连接到 Windows Server 节点][aks-windows-rdp]，而不需使用 SSH。

若要将 SSH 密钥添加到虚拟机规模集中的节点，请使用 [az vmss extension set][az-vmss-extension-set] 和 [az vmss update-instances][az-vmss-update-instances] 命令。

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

以上示例使用前面命令中的 *CLUSTER_RESOURCE_GROUP* 和 *SCALE_SET_NAME* 变量。 以上示例还使用 *~/.ssh/id_rsa.pub* 作为 SSH 公钥的位置。

> [!NOTE]
> 默认情况下，AKS 节点的用户名为 *azureuser*。

将 SSH 公钥添加到规模集后，可以通过 SSH 连接到该规模集中的节点虚拟机（使用其 IP 地址进行连接）。 使用 [kubectl get][kubectl-get] 命令查看 AKS 群集节点的专用 IP 地址。

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

记录要进行故障排除的节点的内部 IP 地址。

若要使用 SSH 访问节点，请遵循[创建 SSH 连接](#create-the-ssh-connection)中的步骤。

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>配置基于虚拟机可用性集的 AKS 群集以进行 SSH 访问

若要配置基于虚拟机可用性集的 AKS 群集以进行 SSH 访问，请找到群集的 Linux 节点名称，并将 SSH 公钥添加到该节点。

使用 [az aks show][az-aks-show] 命令获取 AKS 群集的资源组名称，然后使用 [az vmss list][az-vm-list] 命令列出群集 Linux 节点的虚拟机名称。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

以上示例将 *myResourceGroup* 中 *myAKSCluster* 的群集资源组名称分配到 *CLUSTER_RESOURCE_GROUP*。 然后，该示例使用 *CLUSTER_RESOURCE_GROUP* 列出虚拟机名称。 示例输出显示了虚拟机的名称：

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

若要将 SSH 密钥添加到节点，请使用 [az vm user update][az-vm-user-update] 命令。

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

以上示例使用前面命令中的 *CLUSTER_RESOURCE_GROUP* 变量和节点虚拟机名称。 以上示例还使用 *~/.ssh/id_rsa.pub* 作为 SSH 公钥的位置。 也可以使用 SSH 公钥内容，而不是指定路径。

> [!NOTE]
> 默认情况下，AKS 节点的用户名为 *azureuser*。

将 SSH 公钥添加到节点虚拟机后，可以通过 SSH 连接到该虚拟机（使用其 IP 地址进行连接）。 使用 [az vm list-ip-addresses][az-vm-list-ip-addresses] 命令查看 AKS 群集节点的专用 IP 地址。

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

以上示例使用前面命令中设置的 *CLUSTER_RESOURCE_GROUP* 变量。 以下示例输出显示 AKS 节点的专用 IP 地址：

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>建立 SSH 连接

若要与 AKS 节点建立 SSH 连接，请在 AKS 群集中运行帮助器 pod。 使用此帮助器 pod 可以通过 SSH 依次访问群集和其他 SSH 节点。 若要创建并使用此帮助器 pod，请完成以下步骤：

1. 运行 `debian` 容器映像，并在其上附加一个终端会话。 可以使用此容器来与 AKS 群集中的任何节点建立 SSH 会话：

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > 如果使用 Windows Server 节点，则将节点选择器添加到命令，以便在 Linux 节点上计划 Debian 容器：
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. 将终端会话连接到该容器后，使用 `apt-get` 安装 SSH 客户端：

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 打开一个未连接到容器的新终端窗口，将专用 SSH 密钥复制到帮助程序 Pod 中。 此私钥用来与 AKS 节点建立 SSH 连接。 

   如果需要，请将 *~/.ssh/id_rsa* 更改为 SSH 私钥的位置：

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. 返回到与容器建立的终端会话，更新复制的 `id_rsa` SSH 私钥中的权限，使其成为用户只读的密钥：

    ```console
    chmod 0600 id_rsa
    ```

1. 与 AKS 节点建立 SSH 连接。 同样，AKS 节点的默认用户名为 *azureuser*。 遵照提示继续建立连接，因为一开始就信任 SSH 密钥。 然后，系统会提供 AKS 节点的 bash 提示：

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>删除 SSH 访问

完成后，运行 `exit` 退出 SSH 会话，然后运行 `exit` 退出交互式容器会话。 此容器会话关闭后，将删除用于从 AKS 群集进行 SSH 访问的 pod。

## <a name="next-steps"></a>后续步骤

如需其他故障排除数据，可以[查看 kubelet 日志][view-kubelet-logs]或[查看 Kubernetes 主节点日志][view-master-logs]。

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
