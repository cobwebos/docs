---
title: "什么是 VM 规模集？ | Microsoft 文档"
description: "了解 VM 缩放集。"
keywords: "linux 虚拟机, 虚拟机缩放集"
services: virtual-machines-linux
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba1aedb6-49cb-4546-8b8b-da97aba8e42d
ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 9ee2d1d4e50a5bf4c146c48024aae5f10cea32ef


---
# <a name="what-are-virtual-machine-scale-sets"></a>什么是虚拟机缩放集？
虚拟机缩放集可让你以集的形式管理多个 VM。 概括而言，缩放集具有以下优点和缺点：

优点：

1. 高可用性。 每个规模集将它的 VM 放入具有 5 个容错域 (FD) 和 5 个更新域 (UD) 的可用性集，确保可用性（有关 FD 和 UD 的详细信息，请参阅 [VM 可用性](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。 
2. 与 Azure 负载平衡器和应用网关轻松集成。
3. 与 Azure 自动缩放轻松集成。
4. 简化 VM 的部署、管理和清理。
5. 支持常用的 Windows 和 Linux 版本以及自定义映像。

缺点：

1. 无法将数据磁盘连接到缩放集中的 VM 实例。 你必须使用 Blob 存储、Azure 文件、Azure 表或其他存储解决方案。

## <a name="quick-create-using-azure-cli"></a>使用 Azure CLI 快速创建
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>后续步骤
有关一般信息，请参阅[规模集的主要登陆页](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

有关其他文档，请参阅[规模集的主要文档页](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

有关使用规模集的示例 Resource Manager 模板，请在 [Azure 快速入门模板 github 存储库](https://github.com/Azure/azure-quickstart-templates)中搜索“vmss”。




<!--HONumber=Nov16_HO3-->


