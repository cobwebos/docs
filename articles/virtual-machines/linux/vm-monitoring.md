---
title: "启用或禁用 Azure VM 监视"
description: "介绍如何启用或禁用 Azure VM 监视"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9b2fe579113d6ca6bfd27d82eb9d4657657d44ff
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---
# <a name="enable-or-disable-azure-vm-monitoring"></a>启用或禁用 Azure VM 监视

本部分介绍如何在 Azure 中运行的虚拟机上启用或禁用监视。 你可以使用门户或适用于 Mac、Linux 和 Windows 的 Azure 命令行界面 (Azure CLI) 来启用或禁用监视。

> [!IMPORTANT]
> 本文档介绍了已弃用的 Linux 诊断扩展 2.3。 2018 年 6 月 30 日后将不再支持 2.3 版本。
>
> 可转而启用 Linux 诊断扩展 3.0。 有关详细信息，请参阅[该文档](./diagnostic-extension.md)。

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>通过 Azure 门户启用/禁用监视

可以启用 Azure VM 监视，以 1 分钟的时段提供实例相关数据。 （包括存储更改）。 然后，可以通过门户图形或 API 获取 VM 的详细诊断数据。 默认情况下，Azure 门户会启用对一组有限指标的基于主机的监视。 可在 VM 正在运行或处于停止状态时从 VM 内部启用指标监视。

* 打开 Azure 门户（[ https://portal.azure.com](https://portal.azure.com)）。
* 在左侧导航栏中，单击“虚拟机”。
* 在“虚拟机”列表中，选择正在运行或已停止的实例。 “虚拟机”边栏选项卡随即打开。
* 单击“所有设置”。
* 单击“诊断”。
* 将状态更改为“打开”或“关闭”。 你还可以在此边栏选项卡中选择要为虚拟机启用的监视级别详细信息。

![通过 Azure 门户启用/禁用监视。][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>使用 Azure CLI 启用/禁用监视

启用 Azure VM 监视。

* 创建文件（文件名类似于 PrivateConfig.json）：

```json
{
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
}
```

* 通过 Azure CLI 启用扩展。

```azurecli
azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.3 --private-config-path PrivateConfig.json
```

有关详细信息，请参阅[使用 Linux 诊断扩展监视 Linux VM 的性能和诊断数据](classic/diagnostic-extension-v2.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png

