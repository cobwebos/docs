---
title: "如何标记 Azure Linux 虚拟机 | Microsoft Docs"
description: "了解如何标记使用 Resource Manager 部署模型在 Azure 中创建的 Azure Linux 虚拟机。"
services: virtual-machines-linux
documentationcenter: 
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: da3ff0de2a5d6ac8994b7c16b758f976228a53b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>如何在 Azure 中标记 Linux 虚拟机
本文介绍在 Azure 中通过 Resource Manager 部署模型标记 Linux 虚拟机的不同方式。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 15 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 请注意，只有通过 Resource Manager 部署模型创建的资源支持标记。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>使用 Azure CLI 进行标记
首先需要安装最新的 [Azure CLI 2.0（预览版）](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

还可以使用 [Azure CLI 1.0](tag-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 执行这些步骤。

可以使用此命令查看给定虚拟机的所有属性，包括标记：

        az vm show --resource-group MyResourceGroup --name MyTestVM

若要通过 Azure CLI 添加新的 VM 标记，可以使用 `azure vm update` 命令以及标记参数 **--set**：

        az vm update --resource-group MyResourceGroup --name MyTestVM –-set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2

若要删除标记，可以在 `azure vm update` 命令中使用 **--remove** 参数。

        az vm update –-resource-group MyResourceGroup –-name MyTestVM --remove tags.myNewTagName1


既然我们已通过 Azure CLI 和门户将标记应用到资源中，那就让我们看一看使用情况详细信息，以在计费门户中查看标记。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何标记 Azure 资源，请参阅 [Azure Resource Manager 概述][Azure Resource Manager Overview]和[使用标记来组织 Azure 资源][Using Tags to organize your Azure Resources]。
* 要了解标记如何帮助你管理 Azure 资源的使用，请参阅[了解 Azure 帐单][Understanding your Azure Bill]和[深入了解 Microsoft Azure 资源消耗][Gain insights into your Microsoft Azure resource consumption]。

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
