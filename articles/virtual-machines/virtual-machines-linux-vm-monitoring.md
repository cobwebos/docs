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
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1f15a77304ccf4fdef995d216817363fbf3f6e73
ms.lasthandoff: 03/27/2017


---
# <a name="enable-or-disable-azure-vm-monitoring"></a>启用或禁用 Azure VM 监视
本部分介绍如何在 Azure 中运行的虚拟机上启用或禁用监视。 如果 Azure 虚拟机是从 [Azure 门户](https://portal.azure.com)部署的，则默认情况下会在该虚拟机上启用监视，并且默认以 1 分钟的时段提供监视图形。 你可以使用门户或适用于 Mac、Linux 和 Windows 的 Azure 命令行界面 (Azure CLI) 来启用或禁用监视。 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>通过 Azure 门户启用/禁用监视
可以启用 Azure VM 监视，以 1 分钟的时段提供实例相关数据。 （包括存储更改）。 然后，可以通过门户图形或 API 获取 VM 的详细诊断数据。 默认情况下，Azure 门户将启用监视，但你可以按如下所述将它关闭。 可以在 VM 正在运行或处于停止状态时启用监视。

* 打开 Azure 门户（ **[https://portal.azure.com](https://portal.azure.com)**）
* 在左侧导航栏中，单击“虚拟机”。
* 在“虚拟机”列表中，选择正在运行或已停止的实例。 虚拟机边栏选项卡将会打开。
* 单击“所有设置”。
* 单击“诊断”。
* 将状态更改为“打开”或“关闭”。 你还可以在此边栏选项卡中选择要为虚拟机启用的监视级别详细信息。

[Azure.Note] 当你创建新的虚拟机时，默认情况下已启用“诊断打开”开关

![通过 Azure 门户启用/禁用监视。][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>使用 Azure CLI 启用/禁用监视
启用 Azure VM 监视。

* 使用以下内容创建名为（例如）PrivateConfig.json 的文件。
        {"storageAccountName":"用于接收数据的存储帐户", "storageAccountKey":"帐户的密钥"}
* 运行以下 Azure CLI 命令。
  
        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] 可以从 2.0 版本更改为更高的版本（如果有）。 

有关配置监视指标和示例的详细信息，请访问文档 - [Using Linux Diagnostic Extension to Monitor Linux VM’s performance and diagnostic data](linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)（使用 Linux 诊断扩展监视 Linux VM 的性能和诊断数据）。

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png



