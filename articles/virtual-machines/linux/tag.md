---
title: 如何使用 CLI 标记 Azure 虚拟机
description: 了解如何使用 Azure CLI 为虚拟机添加标签。
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a417e7cff4c7afb601861ddfe09eec171f0cf15
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320607"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>如何在 Azure 中标记 Linux 虚拟机
本文介绍在 Azure 中通过 Resource Manager 部署模型标记 Linux 虚拟机的不同方式。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 请注意，只有通过 Resource Manager 部署模型创建的资源支持标记。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>使用 Azure CLI 进行标记

若要开始，需要安装最新的 [Azure CLI](/cli/azure/install-azure-cli) 并已使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure 帐户。

可以使用此命令查看给定虚拟机的所有属性，包括标记：

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

若要通过 Azure CLI 添加新的 VM 标记，可以使用 `azure vm update` 命令以及标记参数 **--set**：

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

若要删除标记，可以在 `azure vm update` 命令中使用 **--remove** 参数。

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

既然我们已通过 Azure CLI 和门户将标记应用到资源中，那就让我们看一看使用情况详细信息，以在计费门户中查看标记。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述][Azure Resource Manager Overview]和 [使用标记来组织 Azure 资源][Using Tags to organize your Azure Resources]。
* 要了解标记如何帮助你管理 Azure 资源的使用，请参阅 [Understanding your Azure Bill][Understanding your Azure Bill]（了解 Azure 帐单）和 [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption]（深入了解 Microsoft Azure 资源消耗）。

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]: ../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../cost-management-billing/manage/usage-rate-card-overview.md
