---
title: Azure CLI 2.0 示例 - 附加并使用数据磁盘 | Microsoft Docs
description: Azure CLI 2.0 示例
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 88497484071d44e083ad44c45d2eab61f17fd95f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 为虚拟机规模集附加并使用数据磁盘
此脚本创建虚拟机规模集并附加和准备数据磁盘。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.sh "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>清理部署
运行以下命令可删除资源组、规模集和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建资源组、虚拟机规模集和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | 创建用于存储所有资源的资源组。 |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | 创建虚拟机规模集并将其连接到虚拟网络、子网和网络安全组。 负载均衡器也会被创建，以将流量分配到多个 VM 实例。 此命令还指定要使用的 VM 映像和管理凭据。  |
| [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach) | 创建数据磁盘并将其附加到虚拟机规模集。 |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | 安装 Azure 自定义脚本扩展以运行脚本，从而在每个 VM 实例上准备数据磁盘。 |
| [az group delete](/cli/azure/ad/group#delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure CLI 2.0 的详细信息，请参阅 [Azure CLI 2.0 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 虚拟机规模集文档](../cli-samples.md)中找到其他虚拟机规模集 Azure CLI 2.0 脚本示例。