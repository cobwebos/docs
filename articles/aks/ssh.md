---
title: 通过 SSH 登录到 Azure Kubernetes 服务 (AKS) 群集节点
description: 了解如何与 Azure Kubernetes 服务 (AKS) 群集节点建立 SSH 连接，以完成故障排除和维护任务。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 6ddd1b160110e7a751f54f89b387a62d94e9308e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "67614479"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>使用 SSH 连接到 Azure Kubernetes 服务 (AKS) 群集节点以进行维护或故障排除

在 Azure Kubernetes 服务 (AKS) 群集的整个生命周期内，可能需要访问 AKS 节点。 进行这种访问的原因包括维护、日志收集或其他故障排除操作。 您可以使用 SSH 访问 AKS 节点, 包括 Windows Server 节点 (当前在 AKS 中为预览版)。 你还可以[使用远程桌面协议 (RDP) 连接连接到 Windows Server 节点][aks-windows-rdp]。 出于安全考虑，AKS 节点不会在 Internet 中公开。

本文介绍如何使用 AKS 节点的专用 IP 地址来与它们建立 SSH 连接。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需要安装并配置 Azure CLI 版本2.0.64 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="add-your-public-ssh-key"></a>添加 SSH 公钥

默认情况下, 在创建 AKS 群集时, 将获取或生成 SSH 密钥, 然后将其添加到节点。 如果需要指定与创建 AKS 群集时使用的密钥不同的 SSH 密钥, 请将公共 SSH 密钥添加到 Linux AKS 节点。 如果需要, 可以使用[macOS 或 Linux][ssh-nix]或[Windows][ssh-windows]创建 SSH 密钥。 如果使用 PuTTY Gen 创建密钥对, 请将密钥对保存为 OpenSSH 格式, 而不是使用默认的 PuTTy 私钥格式 (.ppk 文件)。

> [!NOTE]
> 当前只能使用 Azure CLI 将 SSH 密钥添加到 Linux 节点。 如果使用 Windows Server 节点, 请使用创建 AKS 群集时提供的 SSH 密钥, 并跳到[如何获取 AKS 节点地址](#get-the-aks-node-address)的步骤。 或者,[使用远程桌面协议 (RDP) 连接连接到 Windows Server 节点][aks-windows-rdp]。

获取 AKS 节点专用 IP 地址的步骤根据你运行的 AKS 群集类型而有所不同:

* 对于大多数 AKS 群集, 请按照步骤[获取常规 AKS 群集的 IP 地址](#add-ssh-keys-to-regular-aks-clusters)。
* 如果你在使用虚拟机规模集的 AKS 中使用了任何预览功能, 如多节点池或 Windows Server 容器支持, 请[按照基于虚拟机规模集的 AKS 群集的步骤进行操作](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters)。

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>将 SSH 密钥添加到常规 AKS 群集

若要将 SSH 密钥添加到 Linux AKS 节点, 请完成以下步骤:

1. 使用[az AKS show][az-aks-show]获取 AKS 群集资源的资源组名称。 群集名称将分配给名为*CLUSTER_RESOURCE_GROUP*的变量。 将*myResourceGroup*替换为 AKS 群集所在的资源组的名称:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. 使用[az vm list][az-vm-list]命令列出 AKS 群集资源组中的 vm。 这些 VM 是 AKS 节点：

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    以下示例输出显示 AKS 节点：

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. 若要向节点添加 SSH 密钥, 请使用[az vm user update][az-vm-user-update]命令。 提供资源组名称，然后提供在上一步骤中获取的 AKS 节点之一。 默认情况下，AKS 节点的用户名为 *azureuser*。 提供自己的 SSH 公钥位置（例如 *~/.ssh/id_rsa.pub*），或粘贴 SSH 公钥的内容：

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>将 SSH 密钥添加到基于虚拟机规模集的 AKS 群集

若要将 SSH 密钥添加到作为虚拟机规模集一部分的 Linux AKS 节点, 请完成以下步骤:

1. 使用[az AKS show][az-aks-show]获取 AKS 群集资源的资源组名称。 群集名称将分配给名为*CLUSTER_RESOURCE_GROUP*的变量。 将*myResourceGroup*替换为 AKS 群集所在的资源组的名称:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. 接下来, 使用[az vmss list][az-vmss-list]命令获取 AKS 群集的虚拟机规模集。 虚拟机规模集名称将分配给名为*SCALE_SET_NAME*的变量:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. 若要将 SSH 密钥添加到虚拟机规模集中的节点, 请使用[az vmss extension set][az-vmss-extension-set]命令。 群集资源组和虚拟机规模集名称是从前面的命令中提供的。 默认情况下，AKS 节点的用户名为 *azureuser*。 如果需要, 请更新你自己的 SSH 公钥位置的位置, 例如 *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. 使用[az vmss update-实例][az-vmss-update-instances]命令将 SSH 密钥应用于节点:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>获取 AKS 节点地址

AKS 节点不会在 Internet 中公开。 若要通过 SSH 连接到 AKS 节点，需使用专用 IP 地址。 下一步将在 AKS 群集中创建一个帮助器 Pod，以允许你通过 SSH 连接到节点的此专用 IP 地址。 获取 AKS 节点专用 IP 地址的步骤根据你运行的 AKS 群集类型而有所不同:

* 对于大多数 AKS 群集, 请按照步骤[获取常规 AKS 群集的 IP 地址](#ssh-to-regular-aks-clusters)。
* 如果你在使用虚拟机规模集的 AKS 中使用了任何预览功能, 如多节点池或 Windows Server 容器支持, 请[按照基于虚拟机规模集的 AKS 群集的步骤进行操作](#ssh-to-virtual-machine-scale-set-based-aks-clusters)。

### <a name="ssh-to-regular-aks-clusters"></a>SSH 到常规 AKS 群集

使用[az vm list-IP 地址][az-vm-list-ip-addresses]命令查看 AKS 群集节点的专用 IP 地址。 提供你自己的 AKS 群集资源组名称 (在前面的[az AKS][az-aks-show]步骤中获得):

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

以下示例输出显示 AKS 节点的专用 IP 地址：

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>SSH 到基于虚拟机规模集的 AKS 群集

使用[kubectl get 命令][kubectl-get]列出节点的内部 IP 地址:

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

记录要进行故障排除的节点的内部 IP 地址。 稍后的步骤中会用到此地址。

## <a name="create-the-ssh-connection"></a>建立 SSH 连接

若要与 AKS 节点建立 SSH 连接，请在 AKS 群集中运行帮助器 pod。 使用此帮助器 pod 可以通过 SSH 依次访问群集和其他 SSH 节点。 若要创建并使用此帮助器 pod，请完成以下步骤：

1. 运行 `debian` 容器映像，并在其上附加一个终端会话。 可以使用此容器来与 AKS 群集中的任何节点建立 SSH 会话：

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > 如果使用 Windows Server 节点 (当前在 AKS 中为预览版), 请将节点选择器添加到命令, 以将 Debian 容器安排在 Linux 节点上, 如下所示:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. 基础 Debian 映像不包含 SSH 组件。 将终端会话连接到该容器后，按如下所示使用 `apt-get` 安装 SSH 客户端：

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 在新的终端窗口中, 未连接到容器, 请使用 kubectl 的 "[获取][kubectl-get]pod" 命令列出 AKS 群集上的 pod。 在上一步骤中创建的 pod 以名称 *aks-ssh* 开头，如以下示例所示：

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. 本文的第一个步骤已将 SSH 公钥添加到了 AKS 节点。 现在，请将 SSH 私钥复制到 pod 中。 此私钥用来与 AKS 节点建立 SSH 连接。

    提供在上一步骤中获取的自己的 *aks-ssh* pod 名称。 如果需要，请将 *~/.ssh/id_rsa* 更改为 SSH 私钥的位置：

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. 返回到与容器建立的终端会话，更新复制的 `id_rsa` SSH 私钥中的权限，使其成为用户只读的密钥：

    ```console
    chmod 0600 id_rsa
    ```

1. 现在，与 AKS 节点建立 SSH 连接。 同样，AKS 节点的默认用户名为 *azureuser*。 遵照提示继续建立连接，因为一开始就信任 SSH 密钥。 然后，系统会提供 AKS 节点的 bash 提示：

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

如果需要其他故障排除数据, 可以[查看 kubelet 日志][view-kubelet-logs]或[查看 Kubernetes 主节点日志][view-master-logs]。

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
