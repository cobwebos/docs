---
title: Azure VM 来宾 OS 防火墙配置不正确 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: bbfa5ce0e277df3648e98be29fe91a44b15a52b7
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262279"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure VM 来宾 OS 防火墙配置不正确

本文介绍如何在 Azure VM 上修复配置不正确的来宾操作系统防火墙。

## <a name="symptoms"></a>症状

1.  虚拟机 (VM) 欢迎屏幕显示 VM 已完全加载。

2.  根据来宾操作系统的配置方式，可能有一些网络流量到达 VM，也可能没有。

## <a name="cause"></a>原因

来宾系统防火墙配置不正确可能会阻止部分或所有类型的网络流量发往 VM。

## <a name="solution"></a>解决方案

在执行这些步骤之前，请创建受影响 VM 的系统磁盘快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。

若要排查此问题，请使用串行控制台，或通过将 VM 的系统磁盘附加到恢复 VM 来[脱机修复 VM](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)。

## <a name="online-mitigations"></a>联机缓解措施

连接到[串行控制台并打开 PowerShell 实例](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)。 如果 VM 上未启用串行控制台，请转到以下 Azure 文章的“脱机修复 VM”部分：

 [尝试通过远程桌面连接到 Azure VM 时发生内部错误](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

可以编辑以下规则，以便启用对 VM 的访问（通过 RDP）或提供更轻松的故障排除体验：

*   远程桌面 (TCP-IN)：这是标准规则，提供对 VM 的主要访问，其方法是允许在 Azure 中使用 RDP。

*   Windows 远程管理 (HTTP-In)：此规则允许通过 PowerShell 连接到 VM。在 Azure 中，可以通过这种类型的访问权限使用远程脚本和故障排除的脚本方面的功能。

*   文件和打印机共享 (SMB-In)：可以通过此规则以故障排除选项的方式进行网络共享访问。

*   文件和打印机共享（回显请求 - ICMPv4-In）：可使用此规则对 VM 执行 ping 操作。

在“串行控制台访问”实例中，可以查询防火墙规则的当前状态。

*   使用“显示名称”作为参数进行查询：

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   使用应用程序所用的“本地端口”进行查询：

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   使用应用程序所用的“本地IP 地址”进行查询：

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   如果看到规则已禁用，则可启用它，方法是运行以下命令：

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   若要进行故障排除，可将防火墙配置文件设置为 OFF：

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    如果为了正确设置防火墙而这样做，请在完成故障排除操作后重新启用防火墙。

    > [!Note]
    > 无需重启 VM 即可应用该更改。

*   再次尝试通过 RDP 连接到 VM。

### <a name="offline-mitigations"></a>脱机缓解措施

1.  若要启用或禁用防火墙规则，请参阅[在 Azure VM 来宾 OS 上启用或禁用防火墙规则](enable-disable-firewall-rule-guest-os.md)。

2.  检查自己是否在实施[来宾 OS 防火墙阻止入站流量方案](guest-os-firewall-blocking-inbound-traffic.md)。

3.  如果仍怀疑防火墙是否正在阻止你的访问，请参阅[在 Azure VM 中禁用来宾 OS 防火墙](disable-guest-os-firewall-windows.md)，然后通过使用正确的规则来重新启用来宾系统防火墙。

