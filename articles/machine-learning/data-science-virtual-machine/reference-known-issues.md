---
title: 参考：疑难解答&已知问题
titleSuffix: Azure Data Science Virtual  Machine
description: 获取 Azure Data Science Virtual Machine 的已知问题、解决方法和故障排除的列表。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206514"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 的已知问题和故障排除方法

本文可帮助您查找和更正使用 Azure 数据科学虚拟机时可能会遇到的错误或故障。

## <a name="python-package-installation-issues"></a>Python 包安装问题

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>在 Linux 上安装具有 pip 中断依赖项的包

安装包时，请使用 `sudo pip install` 而不是 `pip install`。

## <a name="disk-encryption-issues"></a>磁盘加密问题

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM 上的磁盘加密失败

Ubuntu DSVM 当前不支持 Azure 磁盘加密 （ADE）。 作为解决方法，请考虑配置 Azure[托管磁盘的服务器端加密](../../virtual-machines/windows/disk-encryption.md)。

## <a name="tool-appears-disabled"></a>工具显示为已禁用

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V 在 Windows DSVM 上不起作用

Hyper-V 最初在 Windows 上不起作用是预期行为。 为了获得启动性能，我们禁用了某些服务。 要启用 Hyper-V：

1. 打开 Windows DSVM 上的搜索栏
1. 键入"服务"
1. 将所有 Hyper-V 服务设置为"手动"
1. 将"Hyper-V 虚拟机管理"设置为"自动"

最终屏幕应如下所示：

   ![启用 Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

