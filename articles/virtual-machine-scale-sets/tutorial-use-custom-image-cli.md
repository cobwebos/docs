---
title: 教程 - 通过 Azure CLI 2.0 使用规模集中的自定义 VM 映像 | Microsoft Docs
description: 了解如何使用 Azure CLI 2.0 来创建可用于部署虚拟机规模集的自定义 VM 映像
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8024033c8eb059fd0c7cc8d226a630f2bc47f01b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618319"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>教程：通过 Azure CLI 2.0 创建和使用虚拟机规模集的自定义映像
创建规模集时，需指定部署 VM 实例时要使用的映像。 若要在部署 VM 实例之后减少任务数目，可以使用自定义 VM 映像。 在此自定义 VM 映像中可以完成全部所需的应用程序安装或配置步骤。 在规模集中创建的任何 VM 实例使用自定义 VM 映像，并随时可为应用程序流量提供服务。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建和自定义 VM
> * 取消预配和通用化 VM
> * 创建自定义 VM 映像
> * 部署使用自定义 VM 映像的规模集

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 CLI，本教程要求运行 Azure CLI 2.0.29 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。


## <a name="create-and-configure-a-source-vm"></a>创建并配置源 VM

>[!NOTE]
> 本教程介绍创建和使用通用化 VM 映像的过程。 不支持从专用 VM 映像创建规模集。

首先使用 [az group create](/cli/azure/group#az_group_create) 创建资源组，然后使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 此 VM 可用作自定义 VM 映像的源。 以下示例在名为 *myResourceGroup* 的资源组中创建名为 *myVM* 的 VM：

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

[az vm create](/cli/azure/vm#az_vm_create) 命令的输出中会显示 VM 的公共 IP 地址。 通过 SSH 连接到 VM 的公共 IP 地址，如下所示：

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

为了自定义 VM，让我们安装一个基本的 Web 服务器。 部署规模集中的 VM 实例时，该实例会包含运行 Web 应用程序的全部所需包。 按如下所示，使用 `apt-get` 安装 *NGINX*：

```bash
sudo apt-get install -y nginx
```

准备将 VM 用作自定义映像的最后一步是取消预配 VM。 此步骤从 VM 中删除特定于计算机的信息，并实现从单个映像部署许多 VM。 取消预配 VM 后，主机名将重置为 *localhost.localdomain*。 还会删除 SSH 主机密钥、名称服务器配置、根密码和缓存的 DHCP 租约。

若要取消预配 VM，请使用 Azure VM 代理 (*waagent*)。 Azure VM 代理安装在每个 VM 上，用来与 Azure 平台通信。 `-force` 参数告知代理需要接受重置计算机特定信息的提示。

```bash
sudo waagent -deprovision+user -force
```

关闭与 VM 建立的 SSH 连接：

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>从源 VM 创建自定义 VM 映像
现已自定义源 VM，其上已安装 Nginx Web 服务器。 让我们创建与规模集配合使用的自定义 VM 映像。

若要创建映像，需要解除分配 VM。 使用 [az vm deallocate](/cli//azure/vm#az_vm_deallocate) 解除分配 VM。 然后，使用 [az vm generalize](/cli//azure/vm#az_vm_generalize) 将 VM 的状态设置为已通用化，以便 Azure 平台知道该 VM 可使用自定义映像。 只能从通用化 VM 创建映像：

```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

解除分配和通用化 VM 可能需要花费几分钟时间。

现在，使用 [az image create](/cli//azure/image#az_image_create) 创建 VM 的映像。 以下示例从 VM 创建名为 *myImage* 的映像：

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>从自定义 VM 映像创建规模集
使用 [az vmss create](/cli/az/vmss#az_vmss_create) 创建规模集。 不要使用 *UbuntuLTS* 或 *CentOS* 之类的平台映像，而应指定自定义 VM 映像的名称。 以下示例创建名为 *myScaleSet* 的规模集，该规模集使用上一步骤中创建的名为 *myImage* 的自定义映像：

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。


## <a name="test-your-scale-set"></a>测试规模集
若要允许流量抵达规模集并验证 Web 服务器是否正常工作，请使用 [az network lb rule create](/cli/azure/network/lb/rule#create) 命令创建负载均衡器规则。 以下示例创建名为 *myLoadBalancerRuleWeb* 的规则，该规则允许 *TCP* 端口 *80* 上的流量：

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

若要查看规模集的运行方式，请使用 [az network public-ip show](/cli/azure/network/public-ip#show) 获取负载均衡器的公共 IP 地址。 以下示例获取创建为规模集一部分的 *myScaleSetLBPublicIP* 的 IP 地址：

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

在 Web 浏览器中键入公共 IP 地址。 随后会显示默认的 NGINX 网页，如以下示例所示：

![从自定义 VM 映像运行的 Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>清理资源
若要删除规模集和其他资源，请使用 [az group delete](/cli/azure/group#az_group_delete) 删除资源组及其所有资源。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>后续步骤
本教程已介绍如何通过 Azure CLI 2.0 创建和使用规模集的自定义 VM 映像：

> [!div class="checklist"]
> * 创建和自定义 VM
> * 取消预配和通用化 VM
> * 创建自定义 VM 映像
> * 部署使用自定义 VM 映像的规模集

请继续学习下一教程，了解如何将应用程序部署到规模集。

> [!div class="nextstepaction"]
> [将应用程序部署到规模集](tutorial-install-apps-cli.md)