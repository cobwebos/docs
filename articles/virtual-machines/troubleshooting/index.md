---
layout: LandingPage
description: 了解如何排查虚拟机部署问题。
title: Azure 虚拟机故障排除文档 | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: bdb459a69557269a20d36f05acc2da502064efb9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851343"
---
# <a name="troubleshooting-azure-virtual-machines"></a>排查 Azure 虚拟问题

## <a name="tools-for-troubleshooting"></a>用于故障排除的工具

- [串行控制台](serial-console-overview.md)
- [启动诊断](boot-diagnostics.md)
- [Windows VM：将 OS 磁盘附加到另一个 VM 以进行故障排除](troubleshoot-recovery-disks-portal-windows.md)
- [Linux VM：将 OS 磁盘附加到另一个 VM 以进行故障排除](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>无法连接到 VM

### <a name="windows"></a>Windows

**常见解决方案**

- [重置 RDP](reset-rdp.md)
- [RDP 故障排除](troubleshoot-rdp-connection.md)
- [详细的 RDP 故障排除步骤](detailed-troubleshoot-rdp.md)

**RDP 错误**

- [无许可证服务器](troubleshoot-rdp-no-license-server.md)
- [内部 ](Troubleshoot-rdp-internal-error.md)
- [身份验证错误](troubleshoot-authentication-error-rdp-vm.md)
- [排查特定错误](troubleshoot-specific-rdp-errors.md)

**VM 启动错误**

* [BitLocker 启动错误](troubleshoot-bitlocker-boot-error.md)
* [Windows 在启动过程中显示“正在检查文件系统”](troubleshoot-check-disk-boot-error.md)
* [蓝屏错误](troubleshoot-common-blue-screen-error.md)
* [VM 启动时停滞，显示消息“正在准备 Windows](troubleshoot-vm-boot-configure-update.md)
* [蓝色屏幕上出现“关键服务失败”错误](troubleshoot-critical-service-failed-boot-error.md)
* [重启循环问题](troubleshoot-reboot-loop.md)
* [VM 启动停滞在 Windows 更新阶段](troubleshoot-stuck-updating-boot-error.md)
* [VM 启动到安全模式](troubleshoot-rdp-safe-mode.md)

**其他**
- [以脱机方式为 Windows VM 重置 VM 密码](reset-local-password-without-agent.md)
- [在错误配置后重置 NIC](reset-network-interface.md)

### <a name="linux"></a>Linux

- [SSH 故障排除](troubleshoot-ssh-connection.md)
- [详细 SSH 故障排除步骤](detailed-troubleshoot-ssh-connection.md)
- [常见错误消息](error-messages.md)
- [以脱机方式为 Linux VM 重置 VM 密码](reset-password.md)

## <a name="vm-deployment-issues"></a>VM 部署问题

- [分配失败](allocation-failure.md)
- 重新部署 VM
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- 对部署进行故障排除
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [设备名称已更改](troubleshoot-device-names-problems.md)
- [脱机安装 Windows VM 代理](install-vm-agent-offline.md)
- [重新启动 VM 或调整其大小](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>VM 性能问题
- [VM 性能问题](performance-diagnostics.md)
- Windows
    - [如何使用 PerfInsights](how-to-use-perfinsights.md)
    - [性能诊断扩展](performance-diagnostics-vm-extension.md)
- Linux
    - [如何使用 PerfInsights](how-to-use-perfinsights-linux.md)