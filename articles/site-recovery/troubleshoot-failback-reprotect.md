---
title: "排查从 Azure 故障回复到本地，然后重新保护到 Azure 期间发生的故障 | Microsoft Docs"
description: "本文介绍排查从 Azure 故障回复到本地以及重新保护过程中发生的常见错误的方法"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>排查故障回复过程中报告的错误
故障回复实质上包含两个主要步骤。 一个是将 Azure 中的虚拟机重新保护到本地，另一个是实际执行从 Azure 到本地的故障回复。

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>排查故障转移后将虚拟机重新保护回本地时出现的错误
执行将虚拟机重新保护到 Azure 时，可能收到以下错误之一。 若要解决错误，请为每个错误条件使用所述步骤。


### <a name="error-code-95226"></a>错误代码 95226

重新保护失败，因为 Azure 虚拟机无法访问本地配置服务器。

发生条件 
1. Azure 虚拟机无法访问本地配置服务器，因此无法发现，也无法向配置服务器注册。 
2. 需要在 Azure 虚拟机上运行以与本地配置服务器通信的 InMage Scout 应用程序服务，可能在故障转移之后未能运行。

解决方法
1. 需要确保已配置 Azure 虚拟机网络，以便虚拟机能与本地配置服务器通信。 要执行此操作，请将站点到站点 VPN 设置回本地数据中心，或在 Azure 虚拟机的虚拟网络上配置具有私有对等的 ExpressRoute 连接。 
2. 如果已配置网络以允许 Azure 虚拟机与本地配置服务器通信，请登录虚拟机，然后检查“InMage Scout 应用程序服务”。 如果发现 InMage Scout 应用程序服务未运行，请手动启动该服务，并确保服务启动类型设置为“自动”。

### <a name="error-code-78052"></a>错误代码 78052
重新保护失败，出现错误消息：“无法完成对虚拟机的保护”。

发生此错误的原因有两个
1. 要重新保护的虚拟机为 Windows Server 2016。 故障回复当前不支持此操作系统，但很快将给予支持。
2. 故障回复到的主目标服务器上已有同名虚拟机。

要解决此问题，可在另一台主机上选择另一个主目标服务器，以便重新保护在该主机上创建计算机，从而确保名称不发生冲突。 还可以将主目标 vMotion 到另一台主机，这样就不会发生名称冲突。 如果现有虚拟机是孤立的计算机，可将其重命名，以便在同一 ESXi 主机上创建新的虚拟机。

### <a name="error-code-78093"></a>错误代码 78093

VM 未运行，它处于挂起状态或无法访问。

要将故障转移的虚拟机重新保护回本地，需运行 Azure 虚拟机。 这样做的目的是向本地配置服务器注册移动服务，并通过与进程服务器通信来启动复制。 如果计算机位于错误的网络中或者未运行（处于挂起或关闭状态），则配置服务器无法访问虚拟机中的移动服务，无法开始重新保护。 可以重启虚拟机，使其重新与本地通信。 启动 Azure 虚拟机后，重启重新保护作业

### <a name="error-code-8061"></a>错误代码 8061

无法通过 ESXi 主机访问数据存储。

有关故障回复，请参阅[主目标先决条件](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)及[支持数据存储](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback)


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>排查执行将 Azure 虚拟机故障回复回本地时出现的错误
执行将 Azure 虚拟机故障回复回本地时，可能收到以下错误之一。 若要解决错误，请为每个错误条件使用所述步骤。

### <a name="error-code-8038"></a>错误代码 8038

由于错误导致本地虚拟机无法启动

当本地虚拟机在一个没有预配足够内存的主机上启动时，将发生这种情况。

解决方法

1. 可以在 ESXi 主机上预配更多的内存。
1. 将 VM 迁移到另一台有足够内存的 ESXi 主机上以启动虚拟机。