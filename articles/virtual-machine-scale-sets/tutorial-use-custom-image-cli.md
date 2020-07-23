---
title: 教程 - 通过 Azure CLI 使用规模集中的自定义 VM 映像
description: 了解如何使用 Azure CLI 来创建可用于部署虚拟机规模集的自定义 VM 映像
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.reviewer: akjosh
ms.openlocfilehash: 22f3fd44fbeb3d951d4add7b90a0e9aebd863ebf
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792830"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>教程：通过 Azure CLI 创建和使用虚拟机规模集的自定义映像
创建规模集时，需指定部署 VM 实例时要使用的映像。 若要在部署 VM 实例之后减少任务数目，可以使用自定义 VM 映像。 在此自定义 VM 映像中可以完成全部所需的应用程序安装或配置步骤。 在规模集中创建的任何 VM 实例使用自定义 VM 映像，并随时可为应用程序流量提供服务。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建共享映像库
> * 创建专用化映像定义
> * 创建映像版本
> * 从专用化映像创建规模集
> * 共享映像库


如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.4.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="overview"></a>概述

[共享映像库](shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 

共享映像库可让你与他人共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 

## <a name="create-and-configure-a-source-vm"></a>创建并配置源 VM

首先使用 [az group create](/cli/azure/group) 创建资源组，然后使用 [az vm create](/cli/azure/vm) 创建 VM。 然后，此 VM 将用作映像的源。 以下示例在名为 *myResourceGroup* 的资源组中创建名为 *myVM* 的 VM：

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> [az vm create](/cli/azure/vm) 命令的输出中会显示 VM 的 ID  。 请将此 ID 复制到安全的位置，以便稍后在本教程中使用。

[az vm create](/cli/azure/vm) 命令的输出中还会显示 VM 的公共 IP 地址。 通过 SSH 连接到 VM 的公共 IP 地址，如下所示：

```console
ssh azureuser@<publicIpAddress>
```

为了自定义 VM，让我们安装一个基本的 Web 服务器。 部署规模集中的 VM 实例时，该实例会包含运行 Web 应用程序的全部所需包。 按如下所示，使用 `apt-get` 安装 *NGINX*：

```bash
sudo apt-get install -y nginx
```

完成后，键入 `exit` 以断开 SSH 连接。

## <a name="create-an-image-gallery"></a>创建映像库 

映像库是用于启用映像共享的主要资源。 

允许用于库名称的字符为大写或小写字母、数字、点和句点。 库名称不能包含短划线。   库名称在你的订阅中必须唯一。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 创建一个映像库。 以下示例在“美国东部”创建名为 myGalleryRG 的资源组，以及名为 myGallery 的库    。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>创建映像定义

映像定义为映像创建逻辑分组。 映像定义用于管理在其中创建的映像版本的相关信息。 

映像定义名称可以由大写或小写字母、数字、点、短划线和句点构成。 

请确保映像定义的类型正确。 如果已通用化 VM（使用适用于 Windows 的 Sysprep，或适用于 Linux 的 waagent -deprovision），则应使用 `--os-state generalized` 创建通用化映像定义。 若要在不删除现有用户帐户的情况下使用 VM，请使用 `--os-state specialized` 创建专用化映像定义。

若要详细了解可为映像定义指定的值，请参阅[映像定义](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) 在库中创建一个映像定义。

在此示例中，映像定义名为 myImageDefinition，适用于[专用化](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) Linux OS 映像  。 若要使用 Windows OS 创建映像的定义，请使用 `--os-type Windows`。 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!IMPORTANT]
> 该命令的输出中会显示映像定义的 ID  。 请将此 ID 复制到安全的位置，以便稍后在本教程中使用。


## <a name="create-the-image-version"></a>创建映像版本

使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) 从 VM 创建映像版本。  

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：MajorVersion.MinorVersion.Patch    。

在此示例中，映像版本为 1.0.0，我们将在“美国中南部”区域创建 1 个副本，并在“美国东部 2”区域创建 1 个副本    。 复制区域必须包含源 VM 所在的区域。

请将此示例中的 `--managed-image` 值替换为上一步的 VM ID。

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 需要等待映像版本的生成和复制完全完成，然后才能使用同一个托管映像来创建另一个映像版本。
>
> 创建映像版本时，还可以通过添加 `--storage-account-type  premium_lrs` 在高级存储中存储映像，或者通过添加 `--storage-account-type  standard_zrs` 在[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中存储映像。
>




## <a name="create-a-scale-set-from-the-image"></a>从映像创建规模集
使用 [`az vmss create`](/cli/azure/vmss#az-vmss-create) 从专用化映像创建规模集。 

结合 --specialized 参数使用 [`az vmss create`](/cli/azure/vmss#az-vmss-create) 创建规模集可以指明该映像是专用化映像。 

使用 `--image` 的映像定义 ID 从可用的最新映像版本创建规模集实例。 还可以通过提供 `--image` 的映像版本 ID 从特定版本创建规模集实例。 

从前面创建的最新 myImageDefinition 映像版本创建名为“myScaleSet”的规模集   。

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。


## <a name="test-your-scale-set"></a>测试规模集
若要允许流量抵达规模集并验证 Web 服务器是否正常工作，请使用 [az network lb rule create](/cli/azure/network/lb/rule) 命令创建负载均衡器规则。 以下示例创建名为 *myLoadBalancerRuleWeb* 的规则，该规则允许 *TCP* 端口 *80* 上的流量：

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

若要查看规模集的运行方式，请使用 [az network public-ip show](/cli/azure/network/public-ip) 获取负载均衡器的公共 IP 地址。 以下示例获取创建为规模集一部分的 *myScaleSetLBPublicIP* 的 IP 地址：

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

在 Web 浏览器中键入公共 IP 地址。 随后会显示默认的 NGINX 网页，如以下示例所示：

![从自定义 VM 映像运行的 Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>共享库

可以使用基于角色的访问控制 (RBAC) 在订阅之间共享映像。 可以在库、映像定义或映像版本级别共享映像。 任何对映像版本具有读取权限的用户，即使跨订阅，也能够使用映像版本部署 VM。

建议在库级别与其他用户进行共享。 若要获取库的对象 ID，请使用 [az sig show](/cli/azure/sig#az-sig-show)。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

使用对象 ID 作为范围，并使用电子邮件地址和 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 为用户授予对共享映像库的访问权限。 请将 `<email-address>` 和 `<gallery iD>` 替换为自己的信息。

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

有关如何使用 RBAC 共享资源的详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)。


## <a name="clean-up-resources"></a>清理资源
若要删除规模集和其他资源，请使用 [az group delete](/cli/azure/group) 删除资源组及其所有资源。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>后续步骤
本教程已介绍如何通过 Azure CLI 创建和使用规模集的自定义 VM 映像：

> [!div class="checklist"]
> * 创建共享映像库
> * 创建专用化映像定义
> * 创建映像版本
> * 从专用化映像创建规模集
> * 共享映像库

请继续学习下一教程，了解如何将应用程序部署到规模集。

> [!div class="nextstepaction"]
> [将应用程序部署到规模集](tutorial-install-apps-cli.md)
