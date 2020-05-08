---
title: 从 VM 中创建映像
description: 了解如何在 Azure 中的 VM 上创建共享映像库中的映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796572"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>使用 Azure CLI 在 Azure 中创建 VM 的映像版本

如果你有一个要用于生成多个相同 Vm 的现有 VM，则可以使用该 VM 在共享映像库中使用 Azure CLI 来创建映像。 还可以使用[Azure PowerShell](image-version-vm-powershell.md)从 VM 创建映像。

使用共享映像库时，**映像版本**用于创建 VM。 可根据环境的需要创建多个映像版本。 使用映像版本创建 VM 时，映像版本用于为新 VM 创建磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>开始之前

若要完成本文，必须拥有现有的共享映像库。 

你还必须在 Azure 中有一个现有 VM，该 VM 位于你的库所在的区域中。 

如果 VM 附加了数据磁盘，则数据磁盘大小不能超过 1 TB。

完成本文后，请在需要时替换资源名称。

## <a name="get-information-about-the-vm"></a>获取有关 VM 的信息

可以使用[az vm list](/cli/azure/vm#az-vm-list)查看可用 vm 列表。 

```azurecli-interactive
az vm list --output table
```

知道 VM 名称及其所在的资源组后，请使用[az VM get-help](/cli/azure/vm#az-vm-get-instance-view)获取 VM 的 ID。 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>创建映像定义

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像版本的信息，这些版本是在其中创建的。 

映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

请确保图像定义为正确的类型。 如果已通用化 VM （使用适用于 Windows 的 Sysprep，或适用于 Linux 的 waagent 取消预配），则应使用`--os-state generalized`创建通用化映像定义。 如果要在不删除现有用户帐户的情况下使用 VM，请使用`--os-state specialized`创建专用映像定义。

若要详细了解可以为映像定义指定的值，请参阅[映像定义](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用[az sig image definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)在库中创建映像定义。

在此示例中，映像定义的名称为*myImageDefinition*，适用于[专用](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images)Linux OS 映像。 若要使用 Windows OS 创建映像的定义，请使用`--os-type Windows`。 

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


## <a name="create-the-image-version"></a>创建映像版本

使用[az image 画廊 create-image 版本](/cli/azure/sig/image-version#az-sig-image-version-create)从 VM 创建映像版本。  

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式： *MajorVersion*。*MinorVersion*。*修补程序*。

在此示例中，我们的映像的版本为*1.0.0* ，我们将在美国*西部*区域中创建2个副本，在美国*中南部*区域创建1个副本，并使用区域冗余存储在*美国东部 2*区域中创建1个副本。 复制区域必须包含源 VM 所在的区域。

将此示例`--managed-image`中的值替换为上一步中 VM 的 ID。

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 你还可以通过添加`--storage-account-type  premium_lrs`或[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)在 Premiun 存储中存储映像，方法是在`--storage-account-type  standard_zrs`创建映像版本时添加。
>

## <a name="next-steps"></a>后续步骤

使用 Azure CLI 从[一般化映像](vm-generalized-image-version-cli.md)创建 VM。
