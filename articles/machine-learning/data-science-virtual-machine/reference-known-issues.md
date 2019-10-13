---
title: 已知问题与故障排除
titleSuffix: Azure Data Science Virtual  Machine
description: 获取 Azure Data Science Virtual Machine 的已知问题、解决方法和疑难解答列表
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301914"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 的已知问题和故障排除

本文可帮助你查找和更正使用 Azure Data Science Virtual Machine 时遇到的错误或故障。

## <a name="python-package-installation-issues"></a>Python 包安装问题

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>在 Linux 上安装具有 pip 中断依赖项的包

安装包时，请使用 `sudo pip install` 而不是 `pip install`。

## <a name="disk-encryption-issues"></a>磁盘加密问题

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM 上的磁盘加密失败

Ubuntu DSVM 目前不支持 Azure 磁盘加密（ADE）。 作为一种解决方法，请考虑[使用客户管理的密钥配置 Azure 存储加密](../../storage/common/storage-encryption-keys-portal.md)。

## <a name="tool-appears-disabled"></a>工具显示已禁用

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-v 在 Windows DSVM 上不起作用

这是预期的行为，因为对于启动性能，我们已禁用某些服务。 若要重新启用，请打开 Windows DSVM 上的搜索栏，键入 "Services"，将所有 Hyper-v 服务设置为 "手动"，并将 "Hyper-v 虚拟机管理" 设置为 "自动"。

最终屏幕应如下所示：

   ![启用 Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

