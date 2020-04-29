---
title: 利用 Azure Site Recovery 排查移动服务推送安装问题
description: 在通过 Azure Site Recovery 为灾难恢复启用复制时排查移动服务安装错误。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656326"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>移动服务推送安装故障排除

移动服务安装是启用复制的关键步骤。 此步骤的成功与否取决于满足先决条件和使用受支持的配置。 在移动服务安装过程中可能遇到的最常见故障是由于：

* [凭据/权限错误](#credentials-check-errorid-95107--95108)
* [登录失败](#login-failures-errorid-95519-95520-95521-95522)
* [连接错误](#connectivity-failure-errorid-95117--97118)
* [文件和打印机共享错误](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows Management Instrumentation （WMI）故障](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [不受支持的操作系统](#unsupported-operating-systems)
* [不受支持的启动配置](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [卷影复制服务（VSS）安装失败](#vss-installation-failures)
* [GRUB 配置中是设备名称而不是设备 UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [逻辑卷管理器（LVM）卷](#lvm-support-from-920-version)
* [重启警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

启用复制时，Azure Site Recovery 会尝试在虚拟机（VM）上安装移动服务代理。 在此过程中，配置服务器尝试连接到虚拟机并复制代理。 若要启用成功安装，请按照分步故障排除指南操作。

## <a name="credentials-check-errorid-95107--95108"></a>凭据检查（ErrorID：95107 和 95108）

验证 "启用复制" 过程中所选的用户帐户是否有效且准确。 Azure Site Recovery 要求具有**管理员权限**的**根**帐户或用户帐户才能执行推送安装。 否则，推送安装将在源计算机上被阻止。

对于 Windows （**错误 95107**），请使用本地帐户或域帐户验证用户帐户是否具有源计算机的管理访问权限。 如果使用的不是域帐户，则需要在本地计算机上禁用远程用户访问控制。

* 手动添加禁用远程用户访问控制的注册表项：

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * 添加新`DWORD`的：`LocalAccountTokenFilterPolicy`
  * 将值设置为`1`

* 若要添加注册表项，请在命令提示符下运行以下命令：

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

对于 Linux （**错误 95108**），必须选择**根**帐户才能成功安装移动服务代理。 此外，SSH 文件传输协议（SFTP）服务应处于运行状态。 在_sshd_config_文件中启用 SFTP 子系统和密码身份验证：

1. 以 **root** 身份登录。
1. 中转到 _/etc/ssh/sshd_config 文件_中，找到以开头的行`PasswordAuthentication`。
1. 取消注释该行，并将值更改为`yes`。
1. 找到以开头的行`Subsystem`，并取消注释该行。
1. 重新启动 `sshd` 服务。

如果要修改所选用户帐户的凭据，请遵循[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)。

## <a name="insufficient-privileges-failure-errorid-95517"></a>权限不足（ErrorID：95517）

如果选择安装移动代理的用户不具有管理员权限，则不允许配置服务器/横向扩展进程服务器将移动代理软件复制到源计算机。 此错误是由于访问被拒绝失败导致的。 确保用户帐户有管理员权限。

如果要修改所选用户帐户的凭据，请遵循[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)。

## <a name="insufficient-privileges-failure-errorid-95518"></a>权限不足（ErrorID：95518）

如果在尝试登录源计算机时，主域与工作站之间的域信任关系失败，则移动代理安装将失败，并出现错误 ID 95518。 确保用于安装移动代理的用户帐户具有管理权限，以便通过源计算机的主域进行登录。

如果要修改所选用户帐户的凭据，请按照[以下说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)进行操作。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>登录失败（ErrorID：95519、95520、95521、95522）

本节介绍凭据和登录错误消息。

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>已禁用该用户帐户的凭据（ErrorID：95519）

启用复制期间选择的用户帐户被禁用。 若要启用用户帐户，请参阅[本文](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser)或通过将文本_用户名_替换为实际用户名来运行以下命令。

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>由于多次登录尝试失败（ErrorID：95520），凭据已被锁定

在访问计算机时多次重试失败会导致系统锁定用户帐户。 失败可能是因为：

* 在配置设置期间提供的凭据不正确。
* 在 "启用复制" 过程中选择的用户帐户错误。

按照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)修改所选凭据，然后重试该操作。

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>源计算机上的登录服务器不可用（ErrorID：95521）

如果源计算机上的登录服务器不可用，则会出现此错误。 如果登录服务器不可用，则登录请求将失败，并且无法安装移动代理。 若要成功登录，请确保登录服务器在源计算机上可用，并启动 Netlogon 服务。 有关详细信息，请参阅[Windows 登录方案](/windows-server/security/windows-authentication/windows-logon-scenarios)。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>登录服务未在源计算机上运行（ErrorID：95522）

登录服务未在源计算机上运行，导致登录请求失败。 无法安装移动代理。 若要解决此错误，请使用以下方法之一在源计算机`Netlogon`上启动该服务：

* 若要从`Netlogon`命令提示符启动该服务，请运行命令`net start Netlogon`。
* 从任务管理器中， `Netlogon`启动服务。

## <a name="connectivity-failure-errorid-95117--97118"></a>连接故障（ErrorID： 95117 & 97118）

配置服务器/横向扩展进程服务器尝试连接到源 VM 以安装移动代理。 如果源计算机由于网络连接问题而无法访问，则会出现此错误。

解决该错误：

* 确保你可以从配置服务器对源计算机进行 ping 操作。 如果在 "启用复制" 过程中选择了横向扩展进程服务器，请确保可以从进程服务器对源计算机进行 ping 操作。

* 在源服务器计算机命令行中，使用`Telnet`对 HTTPS 端口135上的配置服务器或横向扩展进程服务器执行 ping 操作，如以下命令中所示。 此命令检查是否存在任何网络连接问题或防火墙端口阻止问题。

  `telnet <CS/ scale-out PS IP address> <135>`

* 此外，对于 Linux VM：
  * 检查是否安装了最新的 OpenSSH、OpenSSH 服务器和 OpenSSL 包。
  * 检查并确保安全外壳 (SSH) 已启用且正在端口 22 上运行。
  * SFTP 服务应运行。 在_sshd_config_文件中启用 SFTP 子系统和密码身份验证：

    1. 以 **root** 身份登录。
    1. 中转到 _/etc/ssh/sshd_config_文件中，找到以开头的行`PasswordAuthentication`。
    1. 取消注释该行，并将值更改为`yes`。
    1. 查找以开头的行`Subsystem`，并取消注释行
    1. 重新启动 `sshd` 服务。

* 如果在一段时间后没有合适的响应，则连接尝试可能会失败，或者已建立的连接因连接的主机未能响应而失败。
* 这可能是与连接/网络/域相关的问题。 这也可能是因为 DNS 名称解决了问题或 TCP 端口耗尽问题。 请检查域中是否存在任何此类已知问题。

## <a name="connectivity-failure-errorid-95523"></a>连接故障（ErrorID：95523）

当找不到源计算机的网络、可能已被删除或不再可用时，会出现此错误。 解决此错误的唯一方法是确保网络存在。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>文件和打印机共享服务检查 (ErrorID：95105 & 95106)

进行连接性检查后，验证虚拟机上是否已启用 "文件和打印机共享" 服务。 需要这些设置才能将移动代理复制到源计算机。

对于**Windows 2008 R2 及更早版本**：

* 若要通过 Windows 防火墙启用文件和打印共享，
  1. 打开 **"控制面板** > **系统和安全** > **Windows 防火墙**"。 在左窗格中，选择 "**高级设置** > " "控制台树中的**入站规则**"。
  1. 找到文件和打印机共享 (NB-Session-In) 以及文件和打印机共享 (SMB-In) 规则。
  1. 请依次右键单击每个规则，然后单击“启用规则”****。

* 启用组策略的文件共享：
  1. 请参阅**开始**，键入`gpmc.msc`并搜索。
  1. 在导航窗格中，打开以下文件夹： "**本地计算机策略** > **用户配置** > **管理模板** > **Windows 组件** > " "**网络共享**"。
  1. 在详细信息窗格中，双击“防止用户共享其配置文件中的文件”****。

     若要禁用组策略设置并使用户能够共享文件，请选择 "**禁用**"。

  1. 选择“确定”**** 保存更改。

  有关详细信息，请参阅[使用组策略启用或禁用文件共享](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

若要为 Windows 或 Linux 的更高版本启用文件和打印机共享，请按照[安装适用于 VMware vm 和物理服务器的灾难恢复的移动服务](vmware-azure-install-mobility-service.md)中的说明进行操作。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation （WMI）配置检查（错误代码：95103）

检查文件和打印机服务后，通过防火墙为私有、公共和域配置文件启用 WMI 服务。 若要在源计算机上完成远程执行操作，这些设置是必需的。

启用 WMI：

1. 请参阅 **"控制面板** > **安全性**" 并选择 " **Windows 防火墙**"。
1. 选择 "**更改设置**"，然后选择 "**例外**" 选项卡。
1. 在 "**例外**" 窗口中，选中 WINDOWS MANAGEMENT INSTRUMENTATION （WMI）的复选框，以启用 WMI 流量通过防火墙。

还可以使用以下命令从命令提示符通过防火墙启用 WMI 流量：

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

可以在以下文章中找到其他 WMI 故障排除文章。

* [基本 WMI 测试](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 故障排除](/windows/win32/wmisdk/wmi-troubleshooting)
* [WMI 脚本和 WMI 服务问题疑难解答](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>不受支持的操作系统

失败的另一个常见原因可能是操作系统不受支持。 使用受支持的操作系统和内核版本成功安装移动服务。 避免使用私有修补程序。

若要查看 Azure Site Recovery 支持的操作系统和内核版本的列表，请参阅[支持矩阵文档](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>不支持的启动磁盘配置（ErrorID：95309、95310、95311）

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>启动分区和系统分区/卷不是同一磁盘（ErrorID：95309）

在9.20 版本之前，不同磁盘上的启动和系统分区/卷是不受支持的配置。 从[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)开始，支持此配置。

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>启动磁盘不可用（ErrorID：95310）

无法保护没有启动磁盘的虚拟机。 启动磁盘可确保在故障转移操作期间顺畅地恢复虚拟机。 缺少启动磁盘会导致在故障转移后启动计算机失败。 请确保虚拟机包含启动磁盘，然后重试该操作。 同样，同一台计算机上的多个启动磁盘不受支持。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>源计算机上存在多个启动磁盘（ErrorID：95311）

具有多个启动磁盘的虚拟机不是[受支持的配置](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>多个磁盘上的系统分区（ErrorID：95313）

在9.20 版本之前，多个磁盘上的根分区或卷设置是不受支持的配置。 从[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)开始，支持此配置。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>启用保护失败，因为在 GRUB 配置中提到了设备名称，而不是 UUID （ErrorID：95320）

### <a name="possible-cause"></a>可能的原因

如果是统一的加载程序（GRUB）配置文件（_/boot/grub/menu.lst_、 _/boot/grub/grub.cfg_、 _/boot/grub2/grub.cfg_或 _/etc/default/grub_），则可能会包含参数**root**的值和**恢复**为实际的设备名称，而不是全局唯一标识符（UUID）。 Site Recovery 规定 UUID 方法，因为设备名称可能会在 VM 重新启动后发生更改。 例如，VM 在故障转移时可能不会以相同的名称联机，从而导致出现问题。

例如：

- 以下行来自 GRUB 文件 _/boot/grub2/grub.cfg_：

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 以下行来自 GRUB 文件 _/boot/grub/menu.lst_：

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB 行包含参数**root**和**resume**的实际设备名称，而不是 UUID。

### <a name="how-to-fix"></a>如何修复

设备名应替换为相应的 UUID。

1. 通过执行命令`blkid \<device name>`查找设备的 UUID。

   例如：

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 现在，将设备名称及其 UUID 替换为类似`root=UUID=\<UUID>`的格式。 例如，如果将设备名称替换为文件 _/boot/grub2/grub.cfg_、 _/boot/grub2/grub.cfg_或 _/etc/default/grub_中提到的 root 和 resume 参数的 UUID，则文件中的行类似于以下行：

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 重新启动保护。

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>安装移动服务已完成，但有警告重新启动（ErrorID： 95265 & 95266）

Site Recovery 移动服务包含多个组件，其中一个组件称为筛选器驱动程序。 仅当系统重新启动时，筛选器驱动程序才会加载到系统内存中。 仅当系统重新启动时加载新的筛选器驱动程序时，才可以实现筛选器驱动程序修补程序。

> [!IMPORTANT]
> 这是一条警告，即使在新代理更新后，也可以使用现有复制。 你可以选择在想要获得新筛选器驱动程序的优势时重新启动，但如果不重新启动，旧筛选器驱动程序将继续工作。 因此，在不重新启动的情况下进行更新（筛选器驱动程序除外），**移动服务中的其他增强功能和修复功能也已实现**。 尽管建议，但在每次升级之后都不需要重新启动。 有关何时需要重新启动的详细信息，请在 Azure Site Recovery 中的服务更新中设置 "[移动服务升级后重新启动](service-updates-how-to.md#reboot-after-mobility-service-upgrade)" 部分。

> [!TIP]
>有关在维护时段内计划升级的最佳实践，请参阅 Azure Site Recovery 中的服务更新中的[最新操作系统/内核支持](service-updates-how-to.md#support-for-latest-operating-systemskernels)。

## <a name="lvm-support-from-920-version"></a>版本 9.20 提供的 LVM 支持

在9.20 版本之前，仅支持数据磁盘的逻辑卷管理器（LVM）。 `/boot`分区应位于磁盘分区而不是 LVM 卷上。

从[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)开始，支持[LVM 上的 OS 磁盘](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="insufficient-space-errorid-95524"></a>空间不足（ErrorID：95524）

将移动代理复制到源计算机时，需要至少 100 MB 的可用空间。 请确保源计算机具有所需的可用空间量，然后重试该操作。

## <a name="vss-installation-failures"></a>VSS 安装失败

卷影复制服务（VSS）安装是移动代理安装的一部分。 此服务用于生成应用程序一致恢复点的过程中。 VSS 安装过程可能会由于多种原因而失败。 若要确定确切的错误，请参阅_C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_。 下一节重点介绍了一些常见错误和解决步骤。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 错误 -2147023170 [0x800706BE] - 退出代码 511

当防病毒软件阻止 Azure Site Recovery 服务的操作时，通常会出现此问题。

若要解决此问题，请执行下列操作：

1. 查看[防病毒程序中的文件夹排除](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)列表。
1. 遵循防病毒提供程序发布的准则，取消阻止在 Windows 中注册 DLL。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 错误 7 [0x7] - 退出代码 511

此错误是由于内存不足而无法安装 VSS 导致的运行时错误。 增加磁盘空间以成功完成此操作。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 错误 -2147023824 [0x80070430] - 退出代码 517

当 Azure Site Recovery VSS 提供程序服务[标记为待删除](/previous-versions/ms838153(v=msdn.10))时会发生此错误。 通过运行以下命令，尝试在源计算机上手动安装 VSS：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 错误 -2147023841 [0x8007041F] - 退出代码 512

当 Azure Site Recovery VSS 提供程序服务数据库处于[锁定状态](/previous-versions/ms833798(v=msdn.10))时，将发生此错误。尝试通过从命令提示符运行以下命令，在源计算机上手动安装 VSS：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

出现故障时，请检查任何防病毒程序或其他服务是否处于 "**正在启动**" 状态。 处于**启动**状态的进程可以保留对数据库服务的锁定。 这将导致安装 VSS 提供程序时出现故障。 请确保没有服务处于**启动**状态，然后重试上述操作。

### <a name="vss-exit-code-806"></a>VSS 退出代码 806

如果用于安装的用户帐户没有执行`CSScript`命令的权限，则会发生此错误。 请为用户帐户提供执行此脚本所需的权限，然后重试操作。

### <a name="other-vss-errors"></a>其他 VSS 错误

在命令提示符下运行以下命令，尝试在源计算机上手动安装 VSS 提供程序服务：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS 错误 - 0x8004E00F

此错误通常发生在安装移动代理的过程中`DCOM` ，因为和`DCOM`中的问题处于严重状态。

使用以下过程确定错误原因。

### <a name="examine-the-installation-logs"></a>检查安装日志

1. 打开位于_C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_的安装日志。
2. 如果存在以下错误，则表示出现了此问题：

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

若要解决问题，请执行以下操作：

请联系 [Microsoft Windows 平台团队](https://aka.ms/Windows_Support)，让他们帮助解决 DCOM 问题。

解决 DCOM 问题后，请在命令提示符下使用以下命令手动重新安装 Azure Site Recovery VSS 提供程序：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

如果应用程序一致性对灾难恢复要求不重要，则可以绕过 VSS 提供程序安装。

若要绕过 Azure Site Recovery VSS 提供程序的安装，并在安装后手动安装 Azure Site Recovery VSS 提供程序：

1. 安装移动服务。 安装过程将失败，步骤如下：**安装后的配置**。
1. 若要绕过 VSS 的安装：
   1. 打开位于以下位置的 Azure Site Recovery 移动服务安装目录：

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. 通过添加以下行，将 Azure Site Recovery VSS 提供程序安装脚本_InMageVSSProvider_Install_和_InMageVSSProvider_Uninstall_更改为始终成功：

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. 手动安装移动代理。
1. 当安装成功并转到下一步 "**配置**" 时，删除添加的行。
1. 若要安装 VSS 提供程序，请以管理员身份打开命令提示符，并运行以下命令：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. 验证 Azure Site Recovery VSS 提供程序是否作为服务安装在 Windows 服务中。 打开组件服务 MMC 以确认已列出 VSS 提供程序。
1. 如果 VSS 提供程序安装仍然失败，请与技术支持人员一起解决加密应用程序编程接口（CAPI2）中的权限错误。

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>VSS 提供程序安装失败，因为在非群集计算机上启用了群集服务

此问题导致 Azure Site Recovery 移动代理安装在 Azure Site Recovery VSS 提供程序安装过程中失败。 失败是因为导致 VSS 提供程序安装`COM+`的问题。

### <a name="to-identify-the-issue"></a>识别问题

在位于配置服务器上的 " _C:\ProgramData\ASRSetupLogs\UploadedLogs\<日期-时间">UA_InstallLogFile 日志_中，你将看到以下异常：

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

若要解决问题，请执行以下操作：

1. 验证此计算机是否为非群集计算机，以及群集组件是否未被使用。
1. 如果未使用这些组件，请从计算机中删除群集组件。

## <a name="drivers-are-missing-on-the-source-server"></a>源服务器上缺少驱动程序

如果移动代理安装失败，请检查_C:\ProgramData\ASRSetupLogs_下的日志以确定某些控制集是否缺少某些必需的驱动程序。

若要解决问题，请执行以下操作：

1. 使用注册表编辑器（如`regedit.msc`）打开注册表。
1. 打开`HKEY_LOCAL_MACHINE\SYSTEM`节点。
1. 在`SYSTEM`节点中，找到控件集。
1. 打开每个控制集并确认以下 Windows 驱动程序是否存在：

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. 重新安装所有缺少的驱动程序。

## <a name="next-steps"></a>后续步骤

[了解](vmware-azure-tutorial.md)有关如何为 VMware vm 设置灾难恢复的详细信息。
