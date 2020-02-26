---
title: 通过 SSH 登录到 Azure Kubernetes 服务 (AKS) 群集节点
description: 了解如何与 Azure Kubernetes 服务 (AKS) 群集节点建立 SSH 连接，以完成故障排除和维护任务。
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593625"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>使用 SSH 连接到 Azure Kubernetes 服务 (AKS) 群集节点以进行维护或故障排除

在 Azure Kubernetes 服务 (AKS) 群集的整个生命周期内，可能需要访问 AKS 节点。 进行这种访问的原因包括维护、日志收集或其他故障排除操作。 您可以使用 SSH 访问 AKS 节点，包括 Windows Server 节点（当前在 AKS 中为预览版）。 你还可以[使用远程桌面协议（RDP）连接连接到 Windows Server 节点][aks-windows-rdp]。 出于安全目的，AKS 节点不会向 internet 公开。 若要通过 SSH 连接到 AKS 节点，需使用专用 IP 地址。

本文介绍如何使用 AKS 节点的专用 IP 地址来与它们建立 SSH 连接。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门，并[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 门户][aks-quickstart-portal]。

默认情况下，在创建 AKS 群集时，将获取或生成 SSH 密钥，然后将其添加到节点。 本文说明如何指定与创建 AKS 群集时使用的 SSH 密钥不同的 SSH 密钥。 本文还介绍如何确定节点的专用 IP 地址，并使用 SSH 连接到该地址。 如果不需要指定其他 SSH 密钥，则可以跳过将 SSH 公钥添加到节点的步骤。

本文还假定你具有 SSH 密钥。 可以使用[macOS 或 Linux][ssh-nix]或[Windows][ssh-windows]创建 SSH 密钥。 如果使用 PuTTY Gen 创建密钥对，请将密钥对保存为 OpenSSH 格式，而不是使用默认的 PuTTy 私钥格式（.ppk 文件）。

还需要安装并配置 Azure CLI 版本2.0.64 或更高版本。 运行  `az --version` 即可查找版本。 如果需要安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>配置基于虚拟机规模集的 AKS 群集以进行 SSH 访问

若要配置基于虚拟机规模集的 SSH 访问权限，请找到群集的虚拟机规模集的名称，并将 SSH 公钥添加到该规模集。

使用[az aks show][az-aks-show]命令获取 aks 群集的资源组名称，然后使用[az vmss list][az-vmss-list]命令获取规模集的名称。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

上面的示例将*myResourceGroup*中*myAKSCluster*的群集资源组的名称分配给*CLUSTER_RESOURCE_GROUP*。 然后，该示例使用*CLUSTER_RESOURCE_GROUP*列出规模集名称并将其分配给*SCALE_SET_NAME*。

> [!IMPORTANT]
> 此时，只应使用 Azure CLI 更新基于虚拟机规模集的 AKS 群集的 SSH 密钥。
> 
> 对于 Linux 节点，当前只能使用 Azure CLI 添加 SSH 密钥。 如果要使用 SSH 连接到 Windows Server 节点，请使用创建 AKS 群集时提供的 SSH 密钥，并跳过下一组用于添加 SSH 公钥的命令。 你仍需要进行故障排除的节点的 IP 地址，该地址将显示在此部分的最后一个命令中。 或者，你可以[使用远程桌面协议（RDP）连接而不是使用 SSH 连接到 Windows Server 节点][aks-windows-rdp]。

若要将 SSH 密钥添加到虚拟机规模集中的节点，请使用[az vmss extension set][az-vmss-extension-set]和[az vmss update-实例][az-vmss-update-instances]命令。

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

上面的示例使用前面命令中的*CLUSTER_RESOURCE_GROUP*和*SCALE_SET_NAME*变量。 上面的示例还使用 *~/.ssh/id_rsa*作为 ssh 公钥的位置。

> [!NOTE]
> 默认情况下，AKS 节点的用户名为 *azureuser*。

将 SSH 公钥添加到规模集之后，可以使用 SSH 将其 IP 地址连接到该规模集内的节点虚拟机。 使用[kubectl get 命令][kubectl-get]查看 AKS 群集节点的专用 IP 地址。

```console
kubectl get nodes -o wide
```

以下示例输出显示群集中所有节点的内部 IP 地址，包括 Windows Server 节点。

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

记录要进行故障排除的节点的内部 IP 地址。

若要使用 SSH 访问节点，请按照[创建 ssh 连接](#create-the-ssh-connection)中的步骤操作。

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>配置基于虚拟机可用性集的 AKS 群集以进行 SSH 访问

若要配置用于 SSH 访问的基于虚拟机可用性集的 AKS 群集，请找到群集的 Linux 节点的名称，并将 SSH 公钥添加到该节点。

使用[az aks show][az-aks-show]命令获取 aks 群集的资源组名称，然后使用[az vm list][az-vm-list]命令列出群集的 Linux 节点的虚拟机名称。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

上面的示例将*myResourceGroup*中*myAKSCluster*的群集资源组的名称分配给*CLUSTER_RESOURCE_GROUP*。 然后，该示例使用*CLUSTER_RESOURCE_GROUP*列出虚拟机名称。 示例输出显示了虚拟机的名称：

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

若要向节点添加 SSH 密钥，请使用[az vm user update][az-vm-user-update]命令。

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

上面的示例使用前面命令中的*CLUSTER_RESOURCE_GROUP*变量和节点虚拟机名称。 上面的示例还使用 *~/.ssh/id_rsa*作为 ssh 公钥的位置。 你还可以使用 SSH 公钥的内容，而不是指定路径。

> [!NOTE]
> 默认情况下，AKS 节点的用户名为 *azureuser*。

将 SSH 公钥添加到节点虚拟机后，可以使用 SSH 连接到该虚拟机的 IP 地址。 使用[az vm list-IP 地址][az-vm-list-ip-addresses]命令查看 AKS 群集节点的专用 IP 地址。

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

上面的示例使用在前面的命令中设置的*CLUSTER_RESOURCE_GROUP*变量。 以下示例输出显示 AKS 节点的专用 IP 地址：

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>建立 SSH 连接

若要与 AKS 节点建立 SSH 连接，请在 AKS 群集中运行帮助器 pod。 使用此帮助器 pod 可以通过 SSH 依次访问群集和其他 SSH 节点。 若要创建并使用此帮助器 pod，请完成以下步骤：

1. 运行 `debian` 容器映像，并在其上附加一个终端会话。 可以使用此容器来与 AKS 群集中的任何节点建立 SSH 会话：

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > 如果使用 Windows Server 节点（当前在 AKS 中为预览版），请将节点选择器添加到命令，以在 Linux 节点上计划 Debian 容器：
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. 终端会话连接到容器后，请使用 `apt-get`安装 SSH 客户端：

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 打开新的终端窗口，而不是连接到容器，将专用 SSH 密钥复制到帮助器中。 此私钥用于在 AKS 节点中创建 SSH。 

   如果需要，请将 *~/.ssh/id_rsa* 更改为 SSH 私钥的位置：

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. 返回到容器的终端会话，更新已复制 `id_rsa` 专用 SSH 密钥的权限，使其成为只读用户：

    ```console
    chmod 0600 id_rsa
    ```

1. 创建与 AKS 节点的 SSH 连接。 同样，AKS 节点的默认用户名为 *azureuser*。 遵照提示继续建立连接，因为一开始就信任 SSH 密钥。 然后，系统会提供 AKS 节点的 bash 提示：

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

如果需要其他故障排除数据，可以[查看 kubelet 日志][view-kubelet-logs]或[查看 Kubernetes 主节点日志][view-master-logs]。

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
