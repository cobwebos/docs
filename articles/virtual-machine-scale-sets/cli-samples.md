---
title: Azure CLI 2.0 示例 | Microsoft Docs
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
ms.openlocfilehash: 69fc81d4d0569ee7a66fbda5ab500ef2ee15c694
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cli-20-samples-for-virtual-machine-scale-sets"></a>用于虚拟机规模集的 Azure CLI 2.0 示例

下表包含指向使用 Azure CLI 2.0 生成的 bash 脚本的链接。

| | |
|---|---|
|**创建和管理规模集**||
| [创建虚拟机规模集](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | 使用最低配置创建虚拟机规模集。 |
| [从自定义 VM 映像创建规模集](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | 创建使用自定义 VM 映像的虚拟机规模集。 |
| [将应用程序安装到规模集](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | 使用 Azure 自定义脚本扩展可将基本 Web 应用程序安装到规模集中。 |
|**管理存储**||
| [创建规模集并向其附加磁盘](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | 创建附加了数据磁盘的虚拟机规模集。 |
|**管理缩放和冗余**||
| [启用基于主机的自动缩放](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | 创建配置为基于 CPU 使用情况自动缩放的虚拟机规模集。 |
| [创建单区域规模集](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | 创建使用单个可用性区域的虚拟机规模集。 |
| [创建区域冗余规模集](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | 创建跨多个可用性区域的虚拟机规模集。 |
| | |