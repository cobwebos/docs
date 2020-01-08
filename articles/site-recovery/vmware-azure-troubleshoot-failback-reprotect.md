---
title: 排查 VMware VM 灾难恢复中的故障回复与 Azure Site Recovery
description: 本文介绍了在 VMware VM 灾难恢复到 Azure 期间使用 Azure Site Recovery 排查故障回复和重新保护问题的方法。
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498104"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>排查从 Azure 到本地的故障回复问题

本文介绍了如何对使用 [Azure Site Recovery](site-recovery-overview.md) 故障转移到 Azure 后将 Azure VM 故障回复到本地 VMware 基础结构时可能会遇到的问题进行故障排除。

故障回复实质上包含两个主要步骤。 第一步，在故障转移后，需要将 Azure VM 重新保护到本地，以便它们开始复制。 第二步是从 Azure 运行故障转移，以故障回复到本地站点。

## <a name="common-issues"></a>常见问题

- 如果执行只读的用户 vCenter 发现并保护虚拟机，保护会成功且故障转移可正常工作。 进行重新保护期间，操作会失败，因为无法发现数据存储。 症状是在重新保护期间数据存储没有列出。 若要解决此问题，可以使用具有适当权限的帐户更新 vCenter 凭据，然后重试该作业。
- 在故障回复 Linux 虚拟机并在本地运行它时，会看到网络管理器程序包已从该计算机卸载。 发生此卸载的原因是虚拟机在 Azure 中恢复时，网络管理器程序包遭到删除。
- 当 Linux 虚拟机配置有静态 IP 地址且故障转移到 Azure 时，将通过 DHCP 获取 IP 地址。 当故障转移回复到本地时，该虚拟机会继续使用 DHCP 获取 IP 地址。 如有需要，请手动登录到该计算机，然后将 IP 地址设置回静态地址。 Windows 虚拟机可以重新获取其静态 IP 地址。
- 如果使用 ESXi 5.5 免费版或 vSphere 6 虚拟机监控程序免费版，则故障转移会成功，但故障回复不会成功。 要启用故障回复，请升级为程序的评估许可证。
- 如果无法从进程服务器访问配置服务器，请使用 Telnet 在端口 443 上检查与配置服务器的连接。 也可以尝试从进程服务器 ping 配置服务器。 连接到配置服务器后，进程服务器也应会发出检测信号。
- 作为物理本地服务器保护的 Windows Server 2008 R2 SP1 服务器无法从 Azure 故障回复到本地站点。
- 在以下情况下无法进行故障回复：
    - 已将计算机迁移到 Azure。 [了解详细信息](migrate-overview.md#what-do-we-mean-by-migration)。
    - 已将 VM 移到另一个资源组。
    - 已删除 Azure VM。
    - 已对 VM 禁用保护。
    - 在 Azure 中手动创建了 VM。 计算机在受到重新保护之前，应该已在本地受到初始保护并故障转移到 Azure。
    - 只能故障回复到 ESXi 主机。 不能将 VMware VM 或物理服务器故障回复到 Hyper-V 主机、物理计算机或 VMware 工作站。


## <a name="troubleshoot-reprotection-errors"></a>对重新保护错误进行故障排除

本部分详细介绍了常见的重新保护错误，以及如何更正它们。

### <a name="error-code-95226"></a>错误代码 95226

重新保护失败，因为 Azure 虚拟机无法访问本地配置服务器。

在下述情况中会发生此错误：

* Azure VM 无法访问本地配置服务器。 无法发现 VM 并将其注册到配置服务器。
* 在故障转移后，InMage Scout 应用程序服务未在 Azure VM 上运行。 该服务是与本地配置服务器进行通信所必需的。

若要解决此问题，请执行下列操作：

* 检查 Azure VM 网络是否允许 Azure VM 与本地配置服务器进行通信。 可以设置本地数据中心的站点到站点 VPN，或在 Azure VM 的虚拟网络上配置具有专用对等互连的 Azure ExpressRoute 连接。
* 如果 VM 可以与本地配置服务器通信，请登录到 VM。 然后检查 InMage Scout 应用程序服务。 如果看到该服务未运行，请手动启动该服务。 检查服务启动类型是否已设置为“自动”。

### <a name="error-code-78052"></a>错误代码 78052

**无法为虚拟机完成保护。**

如果要故障回复到的主目标服务器上已有同名的 VM，则可能会发生此问题。

若要解决此问题，请执行下列操作：

* 选择另一台主机上的另一个主目标服务器，以便重新保护在该主机上创建虚拟机，从而确保名称不发生冲突。
* 还可以使用 vMotion 将主目标服务器移到另一台主机，这样就不会发生名称冲突。 如果现有 VM 是孤立的虚拟机，请将其重命名，以便在同一 ESXi 主机上创建新的 VM。


### <a name="error-code-78093"></a>错误代码 78093

VM 未运行，它处于挂起状态或无法访问。

若要解决此问题，请执行下列操作：

若要重新保护已故障转移的 VM，必须运行 Azure VM，以便移动服务向本地配置服务器注册，并通过与进程服务器通信来启动复制。 如果计算机位于错误的网络上或未运行（不是响应或关闭），则配置服务器无法访问 VM 上的移动服务，因而无法开始重新保护。

* 重新启动 VM，使其重新与本地通信。
* 启动 Azure 虚拟机后，重启重新保护作业。

### <a name="error-code-8061"></a>错误代码 8061

无法通过 ESXi 主机访问数据存储。

针对故障回复检查[主目标先决条件和支持的数据存储](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server)。


## <a name="troubleshoot-failback-errors"></a>解决故障回复错误

本部分介绍了在故障回复期间可能会遇到的常见错误。

### <a name="error-code-8038"></a>错误代码 8038

**由于错误导致本地虚拟机无法启动。**

当本地 VM 在一个没有预配足够内存的主机上启动时，会发生此问题。 

若要解决此问题，请执行下列操作：

* 可以在 ESXi 主机上预配更多内存。
* 另外，可以使用 vMotion 将 VM 迁移到另一台有足够内存的 ESXi 主机上以启动 VM。
