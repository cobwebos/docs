---
title: 连接到 Azure VM 时，远程桌面许可证服务器不可用 | Microsoft Docs
description: 了解如何排查在没有可用远程桌面许可证服务器的情况下 RDP 失败的问题 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088517"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>连接到 Azure VM 时，远程桌面许可证服务器不可用

本文帮助你解决在没有任何远程桌面许可证服务器可用于提供许可证的情况下，无法连接到 Azure 虚拟机 (VM) 的问题。

## <a name="symptoms"></a>症状

尝试连接到虚拟机 (VM) 时遇到以下情况：

- VM 屏幕截图显示操作系统已满载，并正在等待凭据。
- 尝试建立 Microsoft 远程桌面协议 (RDP) 连接时收到以下错误消息：

  - 由于没有可用于提供许可证的远程桌面授权服务器，远程会话已断开连接。

  - 没有可用的远程桌面许可证服务器。 远程桌面服务将停止工作，因为此计算机已超过其宽限期，并且尚未连接至少一个有效的 Windows Server 2008 许可证服务器。 请选择此消息打开“RD 会话主机服务器配置”，以使用许可诊断。

但是，可以使用管理会话正常连接到 VM：

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>原因

如果没有可用的远程桌面许可证服务器，因此无法提供许可证来启动远程会话，则将出现此问题。 即使在 VM 上设置了远程桌面会话主机角色，也仍可能有多种情况会导致此问题：

- 从未在环境中设置远程桌面授权角色，并且宽限期（180 天）已过。
- 在环境中安装了远程桌面许可证，但从未将它激活。
- 未在环境中的远程桌面许可证中注入客户端访问许可证 (CAL) 用于设置连接。
- 在环境中安装了远程桌面许可证。 有可用的 CAL，但未正确配置这些 ACL。
- 远程桌面许可证具有 CAL，并且已激活。 但是，远程桌面许可证服务器上的其他一些问题阻止在环境中提供许可证。

## <a name="solution"></a>解决方案

若要解决此问题，请[备份 OS 磁盘](../windows/snapshot-copy-managed-disk.md)并执行以下步骤：

1. 使用管理会话连接到 VM：

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    如果无法使用管理会话连接到 VM，可以使用 [Azure 上的虚拟机串行控制台](serial-console-windows.md)访问 VM，如下所述：

    1. 选择“支持和故障排除” > “串行控制台(预览版)”访问串行控制台。 如果在 VM 上启用了该功能，则可以成功连接 VM。

    2. 为 CMD 实例创建新通道。 输入 **CMD** 启动通道，并获取通道名称。

    3. 切换到运行 CMD 实例的通道。 在本例中，它应该是通道 1：

       ```
       ch -si 1
       ```

    4. 再次按 **Enter** 并输入 VM 的有效用户名和密码，以及本地 ID 或域 ID。

2. 检查 VM 上是否已启用远程桌面会话主机角色。 如果启用了该角色，请确保它正常运行。 打开权限提升的 CMD 实例并执行以下步骤：

    1. 使用以下命令检查远程桌面会话主机角色的状态：

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        如果此命令返回 0 值，则表示该角色已禁用，你可以转到步骤 3。

    2. 使用以下命令检查策略，并根据需要重新进行配置：

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        如果 **LicensingMode** 值设置为除 4 以外的其他任何值（按用户），请将它设置为 4：

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       如果 **SpecifiedLicenseServers** 值不存在或包含错误的许可证服务器信息，请如下所示进行更改：

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. 对注册表进行任何更改后，重启 VM。

    4. 如果没有 CAL，请删除远程桌面会话主机角色。 然后，RDP 将设置回到正常值。 它只允许与 VM 建立两个并发 RDP 连接：

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        如果 VM 具有远程桌面授权角色并且未被使用，则你也可以删除该角色。

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. 确保 VM 可以连接到远程桌面许可证服务器。 可以测试 VM 与许可证服务器之间的端口 135 连接： 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. 如果环境中没有远程桌面许可证服务器，而你想要一个此类服务器，可以[安装远程桌面许可角色服务](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11))。 然后[配置 RDS 许可](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383)。

4. 如果远程桌面许可证服务器已配置且正常运行，请确保使用 CAL 激活远程桌面许可证服务器。

## <a name="need-help-contact-support"></a>需要帮助？ 联系技术支持

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)解决问题。
