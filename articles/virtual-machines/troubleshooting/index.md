---
layout: LandingPage
description: 了解如何排查虚拟机部署问题。
title: Azure 虚拟机故障排除文档 | Microsoft Docs
services: virtual-machines
author: genlin
manager: jeconnoc
ms.assetid: null
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
---

# <a name="troubleshooting-azure-virtual-machines"></a>排查 Azure 虚拟问题

- 分配失败
    - [分配失败](allocation-failure.md)
    - [经典部署分配失败](allocation-failure-classic.md)
- [启动诊断](boot-diagnostics.md)
- RDP
    - [重置 RDP](reset-rdp.md)
    - [RDP 故障排除](troubleshoot-rdp-connection.md)
    - [详细的 RDP 故障排除步骤](detailed-troubleshoot-rdp.md)
    - [排查特定错误](troubleshoot-specific-rdp-errors.md)
- SSH 
    - [SSH 故障排除](troubleshoot-ssh-connection.md)
    - [详细 SSH 故障排除步骤](detailed-troubleshoot-ssh-connection.md)
    - [常见错误消息](error-messages.md)
    - [Windows VM 性能问题](performance-diagnostics.md  )
    - [如何使用 PerfInsights](how-to-use-perfInsights.md)
    - [性能诊断扩展](performance-diagnostics-vm-extension.md)
- [脱机安装 Windows VM 代理](install-vm-agent-offline.md)
- 重新部署 VM
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- 重置 VM 密码
    - [Windows](reset-local-password-without-agent.md)
    - [Linux](reset-password.md)
- [重置 NIC](reset-network-interface.md)
- [重新启动 VM 或调整其大小](restart-resize-error-troubleshooting.md)
- 使用串行控制台
    - [Linux VM](serial-console-linux.md)
        - [串行控制台 GRUB/单用户模式](serial-console-grub-single-user-mode.md)
        - [串行控制台 NMI/SysRq](serial-console-nmi-sysrq.md)
    - [Windows VM](serial-console-windows.md)
        - [CMD 和 PowerShell 命令](serial-console-cmd-ps-commands.md)
- [删除存储资源时出错](storage-resource-deletion-errors.md      )
- [附加 VHD 的 VM 意外重新启动](unexpected-reboots-attached-vhds.md)
- [Windows 激活问题](troubleshoot-activation-problems.md)
- [应用程序访问问题](troubleshoot-app-connection.md)
- 对部署进行故障排除
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [设备名称已更改](troubleshoot-device-names-problems.md)
- VM 恢复方法
    - Windows
        - [PowerShell](troubleshoot-recovery-disks-windows.md)
        - [Azure 门户](troubleshoot-recovery-disks-portal-windows.md)
    - Linux
        - [CLI](troubleshoot-recovery-disks-linux.md)
    - [Azure 门户](troubleshoot-recovery-disks-portal-linux.md)
- [启动错误](boot-error-troubleshoot.md)
- [BitLocker 错误](troubleshoot-bitlocker-boot-error.md)
- [检查文件系统错误](troubleshoot-check-disk-boot-error.md)
- [蓝屏错误](troubleshoot-common-blue-screen-error.md)
- [限制错误](troubleshooting-throttling-errors.md)
- [使用嵌套虚拟化](troubleshoot-vm-by-use-nested-virtualization.md)
- [了解系统重启](understand-vm-reboot.md)

