---
title: 使用 Azure 站点恢复对移动服务推送安装进行故障排除
description: 使用 Azure 站点恢复启用灾难恢复复制时，排除移动服务安装错误。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656326"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>故障排除移动服务推送安装

移动服务安装是启用复制的关键步骤。 此步骤的成功取决于满足先决条件和使用支持的配置。 在移动服务安装期间，您可能面临的最常见故障是由于：

* [凭据/权限错误](#credentials-check-errorid-95107--95108)
* [登录失败](#login-failures-errorid-95519-95520-95521-95522)
* [连接错误](#connectivity-failure-errorid-95117--97118)
* [文件和打印机共享错误](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows 管理检测 （WMI） 故障](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [不受支持的操作系统](#unsupported-operating-systems)
* [不受支持的引导配置](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [卷影复制服务 （VSS） 安装失败](#vss-installation-failures)
* [GRUB 配置中是设备名称而不是设备 UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [逻辑卷管理器 （LVM） 卷](#lvm-support-from-920-version)
* [重启警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

启用复制时，Azure 站点恢复将尝试在虚拟机 （VM） 上安装移动服务代理。 作为此过程的一部分，配置服务器尝试与虚拟机连接并复制代理。 要启用成功安装，请遵循分步故障排除指南。

## <a name="credentials-check-errorid-95107--95108"></a>凭据检查 (ErrorID：95107 & 95108)

验证启用复制期间选择的用户帐户是否有效且准确。 Azure 站点恢复需要具有**管理员权限**的**根**帐户或用户帐户执行推送安装。 否则，推送安装将在源计算机上被阻止。

对于 Windows（**错误 95107），** 验证用户帐户在源计算机上具有管理访问权限，以及本地帐户或域帐户。 如果您不使用域帐户，则需要在本地计算机上禁用远程用户访问控制。

* 要手动添加禁用远程用户访问控制的注册表项，请执行：

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * 添加新`DWORD`：`LocalAccountTokenFilterPolicy`
  * 将值设置为`1`

* 要添加注册表项，从命令提示符中，运行以下命令：

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

对于 Linux（**错误 95108），** 您必须选择成功安装移动服务代理的**根**帐户。 此外，应运行 SSH 文件传输协议 （SFTP） 服务。 要在_sshd_config_文件中启用 SFTP 子系统和密码身份验证，请执行以下操作：

1. 以 **root** 身份登录。
1. 转到 _/etc/ssh/sshd_config文件_，找到以`PasswordAuthentication`开头的行。
1. 取消注释行，并将值更改为`yes`。
1. 查找以`Subsystem`开头的行，然后取消注释行。
1. 重新启动 `sshd` 服务。

如果要修改所选用户帐户的凭据，请按照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)操作。

## <a name="insufficient-privileges-failure-errorid-95517"></a>权限失败不足（错误 ID： 95517）

当选择安装移动代理的用户没有管理员权限时，不允许配置服务器/横向扩展进程服务器将移动代理软件复制到源计算机。 此错误是访问被拒绝失败的结果。 确保用户帐户有管理员权限。

如果要修改所选用户帐户的凭据，请按照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)操作。

## <a name="insufficient-privileges-failure-errorid-95518"></a>权限失败不足（错误 ID： 95518）

当主域和工作站之间的域信任关系建立在尝试登录到源计算机时失败时，移动代理安装将失败，错误 ID 95518。 确保用于安装移动代理的用户帐户具有通过源计算机的主域登录的管理权限。

如果要修改所选用户帐户的凭据，请按照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)操作。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>登录失败（错误 ID： 95519， 95520， 95521， 95522）

本节介绍凭据和登录错误消息。

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>用户帐户的凭据已被禁用（ErrorID： 95519）

启用复制期间选择的用户帐户已禁用。 要启用用户帐户，请参阅[本文](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser)或运行以下命令，将文本_用户名_替换为实际用户名。

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>凭据因多次失败的登录尝试而锁定（错误 ID： 95520）

在访问计算机时多次重试失败会导致系统锁定用户帐户。 失败可能是因为：

* 配置设置期间提供的凭据不正确。
* 启用复制期间选择的用户帐户是错误的。

按照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)修改所选凭据，然后重试操作。

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>源计算机上不提供登录服务器（ErrorID： 95521）

当登录服务器在源计算机上不可用时，将发生此错误。 如果登录服务器不可用，登录请求将失败，无法安装移动代理。 要成功登录，请确保登录服务器在源计算机上可用，并启动 Netlogon 服务。 有关详细信息，请参阅[Windows 登录方案](/windows-server/security/windows-authentication/windows-logon-scenarios)。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>登录服务未在源计算机上运行（ErrorID： 95522）

登录服务未在源计算机上运行，并导致登录请求失败。 无法安装移动代理。 要解决错误，请使用以下方法之一在源计算机上启动`Netlogon`服务：

* 要从命令`Netlogon`提示符启动服务，运行命令`net start Netlogon`。
* 从任务管理器开始`Netlogon`服务。

## <a name="connectivity-failure-errorid-95117--97118"></a>连接故障（错误 ID： 95117 & 97118）

配置服务器/横向扩展进程服务器尝试连接到源 VM 以安装移动代理。 当源计算机由于网络连接问题而无法访问时，将发生此错误。

解决该错误：

* 确保可以从配置服务器 ping 源计算机。 如果在启用复制期间选择了横向扩展进程服务器，请确保可以从进程服务器 ping 源计算机。

* 从源服务器计算机命令行中，用于`Telnet`ping HTTPS 端口 135 上的配置服务器或横向扩展进程服务器，如下命令所示。 此命令检查是否有任何网络连接问题或防火墙端口阻塞问题。

  `telnet <CS/ scale-out PS IP address> <135>`

* 此外，对于 Linux VM：
  * 检查是否安装了最新的 OpenSSH、OpenSSH 服务器和 OpenSSL 软件包。
  * 检查并确保安全外壳 (SSH) 已启用且正在端口 22 上运行。
  * SFTP 服务应运行。 要在_sshd_config_文件中启用 SFTP 子系统和密码身份验证，请执行以下操作：

    1. 以 **root** 身份登录。
    1. 转到 _/etc/ssh/sshd_config_文件，找到以`PasswordAuthentication`开头的行。
    1. 取消注释行，并将值更改为`yes`。
    1. 查找以`Subsystem`开头的行，然后取消注释行
    1. 重新启动 `sshd` 服务。

* 如果在一段时间后没有适当的响应，或者由于连接的主机无法响应而已建立的连接失败，则连接尝试可能会失败。
* 这可能是连接/网络/域相关问题。 这也可能因为 DNS 名称解决了问题或 TCP 端口耗尽问题。 请检查域中是否存在任何此类已知问题。

## <a name="connectivity-failure-errorid-95523"></a>连接故障（错误 ID： 95523）

当源计算机所在的网络找不到、可能已删除或不再可用时，将发生此错误。 解决错误的唯一方法是确保网络存在。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>文件和打印机共享服务检查 (ErrorID：95105 & 95106)

在连接检查后，验证虚拟机上是否已启用文件和打印机共享服务。 这些设置需要将移动代理复制到源计算机。

对于**Windows 2008 R2 和以前的版本**：

* 若要通过 Windows 防火墙启用文件和打印共享，
  1. 打开**控制面板** > **系统和安全** > **窗口防火墙**。 在左侧窗格中，在控制台树中选择 **"输入规则"的"高级设置** > **Inbound Rules**"。
  1. 找到文件和打印机共享 (NB-Session-In) 以及文件和打印机共享 (SMB-In) 规则。
  1. 请依次右键单击每个规则，然后单击“启用规则”****。

* 要使用组策略启用文件共享，可以：
  1. 转到 **"开始"，** 键入`gpmc.msc`和搜索。
  1. 在导航窗格中，打开以下文件夹：**本地计算机策略** > **用户配置** > **管理模板** > **Windows 组件** > **网络共享**。
  1. 在详细信息窗格中，双击“防止用户共享其配置文件中的文件”****。

     要禁用组策略设置，并启用用户共享文件的能力，请选择"**禁用**"。

  1. 选择“确定”**** 保存更改。

  有关详细信息，请参阅[使用组策略启用或禁用文件共享](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

要为更高版本的 Windows 或 Linux 启用文件和打印机共享，请按照[安装移动服务中的说明进行 VMware VM 和物理服务器的灾难恢复](vmware-azure-install-mobility-service.md)。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows 管理检测 （WMI） 配置检查（错误代码：95103）

检查文件和打印机服务后，通过防火墙为私有、公共和域配置文件启用 WMI 服务。 若要在源计算机上完成远程执行操作，这些设置是必需的。

要启用 WMI：

1. 转到**控制面板** > **安全**并选择**Windows 防火墙**。
1. 选择 **"更改设置**"，然后选择"**例外"** 选项卡。
1. 在 **"例外"** 窗口中，选择 Windows 管理检测 （WMI） 的复选框，以启用通过防火墙的 WMI 流量。

还可以通过命令提示符通过防火墙启用 WMI 流量，并具有以下命令：

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

可以在以下文章中找到其他 WMI 故障排除文章。

* [基本 WMI 测试](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 故障排除](/windows/win32/wmisdk/wmi-troubleshooting)
* [解决 WMI 脚本和 WMI 服务的问题](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>不受支持的操作系统

失败的另一个常见原因可能是操作系统不受支持。 使用受支持的操作系统和内核版本成功安装移动服务。 避免使用专用修补程序。

要查看 Azure 站点恢复支持的操作系统和内核版本的列表，请参阅[支持矩阵文档](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>不支持的引导磁盘配置（ErrorID：95309、95310、95311）

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>启动和系统分区/卷不是同一个磁盘（ErrorID： 95309）

在 9.20 版本之前，不同磁盘上的启动和系统分区/卷是不受支持的配置。 从[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)开始，此配置受支持。

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>引导磁盘不可用（错误 ID： 95310）

无法保护没有引导磁盘的虚拟机。 引导磁盘可确保在故障转移操作期间虚拟机顺利恢复。 缺少引导磁盘会导致故障转移后无法启动计算机。 确保虚拟机包含引导磁盘并重试该操作。 此外，不支持同一台计算机上的多个引导磁盘。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>源计算机上存在多个引导磁盘（错误 ID： 95311）

具有多个引导磁盘的虚拟机不是[受支持的配置](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>多个磁盘上的系统分区（错误 ID： 95313）

在 9.20 版本之前，多个磁盘上的根分区或卷设置是不受支持的配置。 从[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)开始，此配置受支持。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>启用保护失败，因为 GRUB 配置中提及的设备名称，而不是 UUID（ErrorID：95320）

### <a name="possible-cause"></a>可能的原因

大统一引导加载程序 （GRUB） 配置文件 _（/boot/grub/menu.lst，_ _/引导/grub/grub.cfg，_ _/引导/grub2/grub.cfg，_ 或 _/etc/默认/grub）_ 可能包含参数**根**的值，并作为实际设备名称**恢复**，而不是通用唯一标识符 （UUID）。 站点恢复要求 UUID 方法，因为设备名称可能会在 VM 重新启动时更改。 例如，VM 在故障转移时可能不会以相同名称联机，从而导致问题。

例如：

- 以下行来自 GRUB 文件 _/引导/grub2/grub.cfg_：

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 以下行来自 GRUB 文件 _/引导/grub/菜单.lst_：

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB 线路包含参数**root**和**继续**的实际设备名称，而不是 UUID。

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

1. 现在，将设备名称替换为其 UUID 的格式`root=UUID=\<UUID>`，如 。 例如，如果我们将设备名称替换为 UUID，用于根和恢复参数，在文件 _/boot/grub2/grub.cfg、/boot/grub2/grub.cfg_或 _/etc/default/grub_中，文件中的行如下所示： _/boot/grub2/grub.cfg_

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 重新启动保护。

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>安装移动服务，警告重新启动（ErrorID： 95265 & 95266）

站点恢复移动服务具有许多组件，其中一个组件称为筛选器驱动程序。 筛选器驱动程序仅在系统重新启动期间加载到系统内存中。 筛选器驱动程序修复只能在系统重新启动时加载新的筛选器驱动程序时才能实现。

> [!IMPORTANT]
> 这是一个警告，即使新代理更新后，现有复制也会起作用。 您可以选择随时重新启动，您想要获得新的筛选器驱动程序的好处，但如果不重新启动，旧的筛选器驱动程序将继续工作。 因此，在更新后没有重新启动，除了筛选器驱动程序，**其他增强和修复在移动服务的好处得到实现**。 尽管建议，但每次升级后重新启动并不是强制性的。 有关何时强制重新启动的信息，请设置 Azure 站点恢复中的"服务更新"中的["移动服务升级后重新启动](service-updates-how-to.md#reboot-after-mobility-service-upgrade)"部分。

> [!TIP]
>有关在维护时段内计划升级的最佳做法，请参阅 Azure 站点恢复中服务更新中[的最新操作系统/内核支持](service-updates-how-to.md#support-for-latest-operating-systemskernels)。

## <a name="lvm-support-from-920-version"></a>版本 9.20 提供的 LVM 支持

在 9.20 版本之前，仅支持数据磁盘的逻辑卷管理器 （LVM）。 分区`/boot`应位于磁盘分区上，而不是 LVM 卷上。

从[9.20 版本](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)开始[，LVM 上的 OS 磁盘](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)受支持。

## <a name="insufficient-space-errorid-95524"></a>空间不足（错误 ID： 95524）

将移动代理复制到源计算机时，至少需要 100 MB 可用空间。 确保源计算机具有所需的可用空间量，然后重试操作。

## <a name="vss-installation-failures"></a>VSS 安装失败

卷影复制服务 （VSS） 安装是移动代理安装的一部分。 此服务用于生成应用程序一致的恢复点。 VSS 安装过程可能会由于多种原因而失败。 要识别确切的错误，请参阅_C：_程序数据\ASRSetupLogs_ASRUnifiedAgent安装程序.log_。 以下部分将突出显示一些常见错误和解决方法步骤。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 错误 -2147023170 [0x800706BE] - 退出代码 511

当防病毒软件阻止 Azure 站点恢复服务的操作时，最常看到此问题。

若要解决此问题，请执行下列操作：

1. 查看[防病毒程序中的文件夹排除](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)列表。
1. 按照防病毒提供商发布的准则取消阻止在 Windows 中注册 DLL。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 错误 7 [0x7] - 退出代码 511

此错误是由于内存不足而导致的运行时错误，无法安装 VSS。 增加磁盘空间，以便成功完成此操作。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 错误 -2147023824 [0x80070430] - 退出代码 517

当 Azure Site Recovery VSS 提供程序服务[标记为待删除](/previous-versions/ms838153(v=msdn.10))时会发生此错误。 尝试通过运行以下命令在源计算机上手动安装 VSS：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 错误 -2147023841 [0x8007041F] - 退出代码 512

当 Azure 站点恢复 VSS 提供程序服务数据库[被锁定](/previous-versions/ms833798(v=msdn.10))时，将发生此错误。尝试通过从命令提示符运行以下命令，在源计算机上手动安装 VSS：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

发生故障时，请检查是否有任何防病毒程序或其他服务处于 **"启动"** 状态。 处于 **"启动"** 状态的进程可以保留数据库服务上的锁。 这将导致安装 VSS 提供程序时出现故障。 确保没有服务处于 **"启动"** 状态，然后重试上述操作。

### <a name="vss-exit-code-806"></a>VSS 退出代码 806

当用于安装的用户帐户没有执行命令的权限时，将发生此`CSScript`错误。 请为用户帐户提供执行此脚本所需的权限，然后重试操作。

### <a name="other-vss-errors"></a>其他 VSS 错误

尝试通过从命令提示符运行以下命令，在源计算机上手动安装 VSS 提供程序服务：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS 错误 - 0x8004E00F

此错误通常在安装移动代理期间发生，因为移动代理处于`DCOM`中，并且`DCOM`处于严重状态。

使用以下过程确定错误原因。

### <a name="examine-the-installation-logs"></a>检查安装日志

1. 打开位于_C：\程序数据\ASRSetupLogs_ASRUnifiedAgent安装程序.log_的安装日志。
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

解决 DCOM 问题时，使用命令提示符中的以下命令手动重新安装 Azure 站点恢复 VSS 提供程序：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

如果应用程序一致性对于灾难恢复要求并不重要，您可以绕过 VSS 提供程序安装。

若要绕过 Azure Site Recovery VSS 提供程序的安装，并在安装后手动安装 Azure Site Recovery VSS 提供程序：

1. 安装移动服务。 安装将在步骤中失败：**安装后配置**。
1. 若要绕过 VSS 的安装：
   1. 打开位于以下位置的 Azure Site Recovery 移动服务安装目录：

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. 修改 Azure 站点恢复 VSS 提供程序安装脚本_InMageVSSProvider_Install_和_InMageVSSProvider_Uninstall.cmd，_ 以便始终通过添加以下行成功：

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. 手动安装移动代理。
1. 当安装成功并移动到下一步时，**配置**，删除您添加的行。
1. 要安装 VSS 提供程序，请以管理员身份打开命令提示符并运行以下命令：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. 验证 Azure 站点恢复 VSS 提供程序是否作为服务在 Windows 服务中安装。 打开组件服务 MMC 以确认已列出 VSS 提供程序。
1. 如果 VSS 提供程序安装继续失败，请与技术支持一起解决加密应用程序编程接口 （CAPI2） 中的权限错误。

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>VSS 提供程序安装失败，因为在非群集计算机上启用群集服务

此问题会导致 Azure 站点恢复移动代理安装在 Azure 站点恢复 VSS 提供程序安装期间失败。 失败的原因是存在阻止 VSS 提供程序安装`COM+`的问题。

### <a name="to-identify-the-issue"></a>识别问题

在位于配置服务器上的日志中 _，C：\程序数据\ASRSetupLogs_上传的\<Logs日期时间>UA_InstallLogFile.log，_ 你会发现以下异常：

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

若要解决问题，请执行以下操作：

1. 验证此计算机是否为非群集计算机，以及未使用群集组件。
1. 如果未使用组件，请从计算机中删除群集组件。

## <a name="drivers-are-missing-on-the-source-server"></a>源服务器上缺少驱动程序

如果移动代理安装失败，请检查_C：_ProgramData_ASRSetupLogs_下的日志，以确定某些控制集中是否缺少某些必需的驱动程序。

若要解决问题，请执行以下操作：

1. 使用注册表编辑器（如`regedit.msc`）打开注册表。
1. 打开`HKEY_LOCAL_MACHINE\SYSTEM`节点。
1. 在节点`SYSTEM`中，找到控制集。
1. 打开每个控制集并确认以下 Windows 驱动程序是否存在：

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * 英特尔

1. 重新安装所有缺少的驱动程序。

## <a name="next-steps"></a>后续步骤

[详细了解如何](vmware-azure-tutorial.md)为 VMware VM 设置灾难恢复。
