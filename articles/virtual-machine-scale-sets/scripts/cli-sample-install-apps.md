---
title: Azure CLI 2.0 示例 - 安装应用 | Microsoft Docs
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
ms.openlocfilehash: b1a8510efccf810bd4fbdd647d58d38cd87d019f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 将应用程序安装到虚拟机规模集中
此脚本创建运行 Ubuntu 的虚拟机规模集，并使用自定义脚本扩展安装一个基本 Web 应用程序。 运行脚本后，可以通过 Web 浏览器访问该 Web 应用。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set"")]

## <a name="clean-up-deployment"></a>清理部署
运行以下命令删除资源组、规模集和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建资源组、虚拟机规模集和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | 创建用于存储所有资源的资源组。 |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | 创建虚拟机规模集并将其连接到虚拟网络、子网和网络安全组。 负载均衡器也会被创建，以将流量分配到多个 VM 实例。 此命令还指定要使用的 VM 映像和管理凭据。  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | 安装 Azure 自定义脚本扩展以运行脚本，从而在每个 VM 实例上准备数据磁盘。 |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | 创建负载均衡器规则，以将 TCP 端口 80 上的流量分摊到规模集中的各个 VM 实例上。 |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | 获取有关负载平衡器使用的已分配公共 IP 地址的信息。 |
| [az group delete](/cli/azure/ad/group#delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure CLI 2.0 的详细信息，请参阅 [Azure CLI 2.0 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 虚拟机规模集文档](../cli-samples.md)中找到其他虚拟机规模集 Azure CLI 2.0 脚本示例。