---
title: "解决使用 Azure Site Recovery 将 Azure VM 故障回复到本地 VMware 时出现的错误 | Microsoft Docs"
description: "本文介绍了使用 Azure Site Recovery 从 Azure 故障回复到 VMware 时常见的故障回复和重新保护错误的解决方法。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/27/2017
ms.author: rajanaki
ms.openlocfilehash: 9b1156884a78eb7d68dc9680765b3c1436c0606a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>从 Azure 故障回复到 VMware 时进行故障排除

本文介绍了如何对使用 [Azure Site Recovery](site-recovery-overview.md) 故障转移到 Azure 后将 Azure VM 故障回复到本地 VMware 基础结构时可能会遇到的问题进行故障排除。

故障回复实质上包含两个主要步骤。 在故障转移后，需要将 Azure VM 重新保护到本地，以便它们开始复制。 第二步是从 Azure 运行故障转移，以故障回复到本地站点。 

## <a name="troubleshoot-reprotection-errors"></a>对重新保护错误进行故障排除

本部分详细介绍了常见的重新保护错误，以及如何更正它们。

### <a name="error-code-95226"></a>错误代码 95226

重新保护失败，因为 Azure 虚拟机无法访问本地配置服务器。

在下述情况中会发生此错误：

1. Azure VM 无法访问本地配置服务器。 无法发现 VM 并将其注册到配置服务器。 
2. 在故障转移后，InMage Scout 应用程序服务未在 Azure VM 上运行。 该服务是与本地配置服务器进行通信所必需的。

若要解决此问题，请执行以下操作：

1. 检查 Azure VM 网络是否允许 Azure VM 与本地配置服务器进行通信。 要执行此操作，请设置本地数据中心的站点到站点 VPN，或在 Azure VM 的虚拟网络上配置具有专用对等互连的 ExpressRoute 连接。 
2. 如果 VM 可以与本地配置服务器进行通信，则登录到 VM，并检查 InMage Scout 应用程序服务。 如果发现它未运行，请手动启动该服务，并检查服务启动类型是否已设置为“自动”。

### <a name="error-code-78052"></a>错误代码 78052

***无法为虚拟机完成保护。**

如果要故障回复到的主目标服务器上已有同名的 VM，则可能会发生此问题。

若要解决此问题，请执行以下操作：
1. 选择另一台主机上的另一个主目标服务器，以便重新保护在该主机上创建计算机，从而确保名称不发生冲突。 
2. 还可以将主目标 vMotion 到另一台主机，这样就不会发生名称冲突。 如果现有 VM 是孤立的计算机，请将其重命名，以便在同一 ESXi 主机上创建新的 VM。

### <a name="error-code-78093"></a>错误代码 78093

VM 未运行，它处于挂起状态或无法访问。

若要重新保护故障转移后的 VM，Azure VM 必须正在运行。 这样做的目的是向本地配置服务器注册移动服务，并通过与进程服务器通信来启动复制。 如果计算机位于错误的网络中或者未在运行（处于挂起或关闭状态），则配置服务器无法访问 VM 中的移动服务，无法开始重新保护。 

1. 重新启动 VM，使其重新与本地通信。
2. 启动 Azure 虚拟机后，重启重新保护作业

### <a name="error-code-8061"></a>错误代码 8061

无法通过 ESXi 主机访问数据存储。

针对故障回复检查[主目标先决条件](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)和[支持的数据存储](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback)。


## <a name="troubleshoot-failback-errors"></a>解决故障回复错误

本部分介绍了在故障回复期间可能会遇到的常见错误。

### <a name="error-code-8038"></a>错误代码 8038

由于错误导致本地虚拟机无法启动

当本地 VM 在一个没有预配足够内存的主机上启动时，将发生这种情况。 若要解决此问题，请执行以下操作：

1. 可以在 ESXi 主机上预配更多内存。
2. 另外，可以将 VM 迁移到另一台有足够内存的 ESXi 主机上以引导 VM。
