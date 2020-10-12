---
title: 使用 Azure Site Recovery 排查移动服务推送安装问题
description: 在为灾难恢复启用复制时，使用 Azure Site Recovery 排查移动服务安装错误。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 8ee6449f357a578b30809bb03723ac1556e4f459
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816150"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>排查移动服务推送安装问题

安装移动服务是启用复制的一个关键步骤。 此步骤的成功取决于能否满足先决条件并使用受支持的配置。 在安装移动服务期间可能会遇到的最常见故障是由以下原因导致的：

* [凭据/权限错误](#credentials-check-errorid-95107--95108)
* [登录失败](#login-failures-errorid-95519-95520-95521-95522)
* [连接错误](#connectivity-failure-errorid-95117--97118)
* [文件和打印机共享错误](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows Management Instrumentation (WMI) 故障](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [不支持的操作系统](#unsupported-operating-systems)
* [启动配置不受支持](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [卷影复制服务 (VSS) 安装失败](#vss-installation-failures)
* [GRUB 配置中是设备名称而不是设备 UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [逻辑卷管理器 (LVM) 卷](#lvm-support-from-920-version)
* [重启警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

启用复制时，Azure Site Recovery 会尝试在虚拟机 (VM) 上安装移动服务代理。 在此过程中，配置服务器会尝试连接虚拟机并复制代理。 要想成功安装，请按照分步故障排除指导进行操作。

## <a name="credentials-check-errorid-95107--95108"></a>凭据检查（ErrorID：95107 和 95108）

验证在启用复制期间选择的用户帐户是否有效且准确。 Azure Site Recovery 需要具有**管理员特权**的 **root** 帐户或用户帐户来执行推送安装。 否则，系统会在源计算机上阻止推送安装。

对于 Windows（**错误 95107**），请验证用户是否能够使用本地帐户或域帐户在源计算机上进行管理访问。 如果使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。

* 若要手动添加注册表项来禁用远程用户访问控制，请执行以下操作：

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * 添加一个新的 `DWORD`：`LocalAccountTokenFilterPolicy`
  * 将值设置为 `1`

* 若要添加注册表项，请在命令提示符下运行以下命令：

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

对于 Linux（**错误 95108**），必须选择 **root** 帐户才能成功安装移动服务代理。 此外，SSH 文件传输协议 (SFTP) 服务应当正在运行。 若要在 _sshd_config_ 文件中启用 SFTP 子系统和密码身份验证，请执行以下操作：

1. 以 **root** 身份登录。
1. 转到 /etc/ssh/sshd_config 文件，找到以 `PasswordAuthentication` 开头的行。
1. 取消注释该行，并将值更改为 `yes`。
1. 找到以 `Subsystem` 开头的行，并取消注释该行。
1. 重新启动 `sshd` 服务。

如果想要修改所选用户帐户的凭据，请按照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)进行操作。

## <a name="insufficient-privileges-failure-errorid-95517"></a>权限不足故障（ErrorID：95517）

如果选择用来安装移动代理的用户没有管理员权限，则不允许配置服务器/横向扩展进程服务器将移动代理软件复制到源计算机。 此错误是“拒绝访问”故障导致的。 确保用户帐户有管理员权限。

如果想要修改所选用户帐户的凭据，请按照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)进行操作。

## <a name="insufficient-privileges-failure-errorid-95518"></a>权限不足故障（ErrorID：95518）

如果在尝试登录到源计算机时无法在主域和工作站之间建立域信任关系，则移动代理安装会失败，出现的错误 ID 为 95518。 请确保用于安装移动代理的用户帐户具有管理员权限，能够通过源计算机的主域进行登录。

如果想要修改所选用户帐户的凭据，请按照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)进行操作。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>登录失败（错误 ID：95519、95520、95521、95522）

本部分介绍了凭据和登录错误消息。

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>用户帐户的凭据已禁用（ErrorID：95519）

在启用复制期间选择的用户帐户被禁用。 若要启用用户帐户，请参阅[此文](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser)，或者运行以下命令，将文本 username 替换为实际的用户名。

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>由于多次登录尝试均失败，凭据已被锁定（ErrorID：95520）

在访问计算机时多次重试失败会导致系统锁定用户帐户。 失败可能是因为：

* 在配置设置过程中提供的凭据不正确。
* 在启用复制期间选择的用户帐户不正确。

根据[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)修改所选的凭据，然后重试操作。

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>登录服务器在源计算机上不可用（ErrorID：95521）

登录服务器在源计算机上不可用时，会发生此错误。 如果登录服务器不可用，则登录请求会失败，并且无法安装移动代理。 若要成功进行登录，请确保登录服务器在源计算机上可用，然后启动 Netlogon 服务。 有关详细信息，请参阅 [Windows 登录方案](/windows-server/security/windows-authentication/windows-logon-scenarios)。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>登录服务未在源计算机上运行（ErrorID：95522）

登录服务未在源计算机上运行，导致登录请求失败。 无法安装移动代理。 若要解决此错误，请使用以下方法之一在源计算机上启动 `Netlogon` 服务：

* 若要从命令提示符启动 `Netlogon` 服务，请运行 `net start Netlogon` 命令。
* 从任务管理器中，启动 `Netlogon` 服务。

## <a name="connectivity-failure-errorid-95117--97118"></a>连接失败（ErrorID：95117 和 97118）

配置服务器/横向扩展进程服务器尝试连接到源 VM 以安装移动代理。 当源计算机因网络连接问题而无法访问时，会发生此错误。

若要解决该错误：

* 请确保你能够从配置服务器对源计算机执行 ping 操作。 如果在启用复制期间选择了横向扩展进程服务器，请确保能够从进程服务器对源计算机执行 ping 操作。

* 从源服务器计算机的命令行中，使用 `Telnet` 在 HTTPS 端口 135 上对配置服务器或横向扩展进程服务器执行 ping 操作，如以下命令所示。 此命令检查是否存在任何网络连接问题或防火墙端口阻止问题。

  `telnet <CS/ scale-out PS IP address> <135>`

* 此外，对于 Linux VM：
  * 请检查是否已安装最新的 OpenSSH、OpenSSH Server 和 OpenSSL 包。
  * 检查并确保安全外壳 (SSH) 已启用且正在端口 22 上运行。
  * SFTP 服务应运行。 若要在 _sshd_config_ 文件中启用 SFTP 子系统和密码身份验证，请执行以下操作：

    1. 以 **root** 身份登录。
    1. 转到 /etc/ssh/sshd_config 文件，找到以 `PasswordAuthentication` 开头的行。
    1. 取消注释该行，并将值更改为 `yes`。
    1. 找到以 `Subsystem` 开头的行，并取消注释该行。
    1. 重新启动 `sshd` 服务。

* 如果一段时间后没有正确的响应，则连接尝试可能已失败，或者由于连接的主机响应失败，已建立的连接失败。
* 这可能是由连接/网络/域相关问题导致的。 这也可能是由 DNS 名称解析问题或 TCP 端口耗尽问题导致的。 请检查域中是否存在任何此类已知问题。

## <a name="connectivity-failure-errorid-95523"></a>连接失败（ErrorID：95523）

当源计算机所在的网络无法找到、可能已被删除或不再可用时，会发生此错误。 若要解决此错误，唯一的方法是确保网络存在。

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>检查源计算机上网络共享文件夹的访问权限（ErrorID：95105,95523）

验证虚拟计算机上的网络共享文件夹是否可使用特定凭据从进程服务器 (PS) 进行远程访问。 若要确认访问，请执行以下操作： 

1. 登录到进程服务器计算机。
2. 打开文件资源管理器。 在地址栏中，键入 `\\<SOURCE-MACHINE-IP>\C$`，然后按 Enter。

    ![在 PS 中打开文件夹](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. 文件资源管理器将提示输入凭据。 输入用户名和密码，然后单击“确定”。 <br><br/>

    ![提供凭据](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > 如果源计算机已加入域，则按 `<domainName>\<username>` 格式提供域名和用户名。 如果源计算机位于工作组中，仅提供用户名。

4. 如果成功连接，则可以从进程服务器远程查看源计算机的文件夹。

    ![源计算机上的可见文件夹](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

如果连接不成功，请检查是否满足所有先决条件。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>文件和打印机共享服务检查（ErrorID：95105 和 95106）

进行连接性检查后，验证是否在虚拟机上启用了文件和打印机共享服务。 若要将移动代理复制到源计算机，这些设置是必需的。

对于 **Windows 2008 R2 和以前的版本**：

* 若要通过 Windows 防火墙启用文件和打印共享，
  1. 打开“控制面板” > “系统和安全” > “Windows 防火墙”。   在左侧窗格的控制台树中选择“高级设置” > “入站规则”。 
  1. 找到文件和打印机共享 (NB-Session-In) 以及文件和打印机共享 (SMB-In) 规则。
  1. 请依次右键单击每个规则，然后单击“启用规则”。

* 若要使用组策略来启用文件共享，请执行以下操作：
  1. 转到“开始”，键入 `gpmc.msc` 并搜索。
  1. 在导航窗格中，打开以下文件夹：“本地计算机策略” > “用户配置” > “管理模板” > “Windows 组件” > “网络共享”。    
  1. 在详细信息窗格中，双击“防止用户共享其配置文件中的文件”。

     若要禁用组策略设置，并允许用户共享文件，请选择“禁用”。

  1. 选择“确定”保存更改。

  有关详细信息，请参阅[使用组策略启用或禁用文件共享](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

若要为更高版本的 Windows 或 Linux 启用文件和打印机共享，请按照[安装移动服务以对 VMware VM 和物理服务器进行灾难恢复](vmware-azure-install-mobility-service.md)中的说明进行操作。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) 配置检查（错误代码：95103）

进行文件和打印机服务检查后，通过防火墙为专用配置文件、公共配置文件和域配置文件启用 WMI 服务。 若要在源计算机上完成远程执行操作，这些设置是必需的。

若要启用 WMI，请执行以下操作：

1. 转到“控制面板” > “安全”，然后选择“Windows 防火墙”。  
1. 选择“更改设置”，然后选择“异常”选项卡。 
1. 在“异常”窗口中，选中 Windows Management Instrumentation (WMI) 对应的复选框，以启用穿过防火墙的 WMI 流量。

还可以从命令提示符使用以下命令来启用穿过防火墙的 WMI 流量：

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

可以在以下文章中找到其他 WMI 故障排除文章。

* [基本 WMI 测试](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [WMI 故障排除](/windows/win32/wmisdk/wmi-troubleshooting)
* [使用 WMI 脚本和 WMI 服务排查问题](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>不支持的操作系统

另一个常见的失败原因可能是操作系统不受支持。 请使用受支持的操作系统和内核版本，以便成功安装移动服务。 避免使用私有修补程序。

若要查看 Azure Site Recovery 支持的操作系统和内核版本列表，请参阅[支持矩阵文档](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>启动磁盘配置不受支持（ErrorID：95309、95310、95311）

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>启动和系统分区/卷不是同一磁盘（ErrorID：95309）

在版本 9.20 之前，将启动和系统分区/卷置于不同磁盘上是不受支持的配置。 从[版本 9.20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 开始，此配置受支持。

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>启动盘不可用（ErrorID：95310)

无法保护没有启动盘的虚拟机。 启动盘可以确保在故障转移操作期间顺利恢复虚拟机。 缺少启动盘会导致故障转移后无法启动计算机。 确保虚拟机包含启动盘并重试该操作。 另外，不支持同一台计算机上有多个启动盘。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>源计算机上存在多个启动磁盘（ErrorID：95311)

具有多个启动盘的虚拟机不是[受支持的配置](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>在多个磁盘上进行系统分区（ErrorID：95313）

在版本 9.20 之前，将根分区或卷设置在多个磁盘上是不受支持的配置。 从[版本 9.20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 开始，此配置受支持。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>启用保护失败，因为 GRUB 配置中提到了设备名而非 UUID（ErrorID：95320)

### <a name="possible-cause"></a>可能的原因

Grand Unified Bootloader (GRUB) 配置文件（“/boot/grub/menu.lst”、“/boot/grub/grub.cfg”、“/boot/grub2/grub.cfg”或“/etc/default/grub”）可能包含参数 **root** 和 **resume** 的值作为实际设备名而非全局唯一标识符 (UUID)。    Site Recovery 要求使用 UUID 方法，因为设备名称在 VM 重启后可能会发生更改。 例如，VM 在故障转移时可能不会以相同的名称联机，导致出现问题。

例如：

- 以下行来自 GRUB 文件 /boot/grub2/grub.cfg：

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 以下行摘自 GRUB 文件 /boot/grub/menu.lst：

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> 对于 **root** 和 **resume** 参数，GRUB 行包含实际设备名称而非 UUID。

### <a name="how-to-fix"></a>如何修复

设备名应替换为相应的 UUID。

1. 执行 `blkid \<device name>` 命令来查找设备的 UUID。

   例如：

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 现在请将设备名替换为其 UUID，格式类似于 `root=UUID=\<UUID>`。 例如，对于在“/boot/grub2/grub.cfg”、“/boot/grub2/grub.cfg”或“/etc/default/grub”文件中提到的 root 和 resume 参数，如果将设备名称替换为 UUID，则文件中的行将类似于以下行：  

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 重启保护。

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>安装移动设备的操作完成，出现重启警告（ErrorID：95265 和 95266）

Site Recovery 移动服务有多个组件，其中一个称为筛选器驱动程序。 筛选器驱动程序只有在系统重启期间才会加载到系统内存中。 实现筛选器驱动程序修复的前提是，在系统重启时加载新的筛选器驱动程序。

> [!IMPORTANT]
> 这是一个警告，在新代理更新后，现有复制仍会继续进行。 可以选择在需要使用新筛选器驱动程序的时候重启，但如果不重启，则旧筛选器驱动程序仍可继续使用。 因此，如果在更新后没有重启，则除了筛选器驱动器之外，**移动服务中的其他增强功能和修复程序的功能均可实现**。 不需在每次升级后都重启，虽然我们建议你重启。 若要了解何时必须重启，请参阅“Azure Site Recovery 中的服务更新”中的[升级移动服务后重启](service-updates-how-to.md#reboot-after-mobility-service-upgrade)部分。

> [!TIP]
>有关在维护时段计划升级的最佳做法，请参阅“Azure Site Recovery 中的服务更新”中的[对最新操作系统/内核的支持](service-updates-how-to.md#support-for-latest-operating-systemskernels)。

## <a name="lvm-support-from-920-version"></a>版本 9.20 提供的 LVM 支持

在版本 9.20 之前，仅支持将逻辑卷管理器 (LVM) 用于数据磁盘。 `/boot` 分区应位于磁盘分区上，而不应位于 LVM 卷上。

从[版本 9.20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 开始，[LVM 上的 OS 磁盘](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)受支持。

## <a name="insufficient-space-errorid-95524"></a>空间不足（ErrorID：95524）

将移动代理复制到源计算机时，需要至少 100 MB 的可用空间。 请确保源计算机有必需的可用空间量，然后重试此操作。

## <a name="low-system-resources"></a>系统资源不足

出现此问题时，可能看到的错误 ID 有 95572 和 95573。 如果系统内存不足，且无法为移动服务安装分配内存，则会出现此问题。 确保已释放足够的内存，让安装继续进行并成功完成。

## <a name="vss-installation-failures"></a>VSS 安装失败

卷影复制服务 (VSS) 安装是移动代理安装的一部分。 在生成应用程序一致恢复点的过程中将使用此服务。 VSS 安装过程可能会由于多种原因而失败。 若要查明确切的错误，请参阅 C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log。 以下部分中重点介绍了一些常见的错误和解决方法步骤。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 错误 -2147023170 [0x800706BE] - 退出代码 511

最常出现此问题的情形是防病毒软件阻止了 Azure Site Recovery 服务的操作。

若要解决此问题，请执行下列操作：

1. 查看[防病毒程序中的文件夹排除项](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)列表。
1. 按照你的防病毒软件提供商发布的指南在 Windows 中取消阻止 DLL 的注册。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 错误 7 [0x7] - 退出代码 511

此错误是一个运行时错误，是由于内存不足而无法安装 VSS 导致的。 请增大磁盘空间以成功完成此操作。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 错误 -2147023824 [0x80070430] - 退出代码 517

当 Azure Site Recovery VSS 提供程序服务[标记为待删除](/previous-versions/ms838153(v=msdn.10))时会发生此错误。 请尝试通过运行以下命令手动在源计算机上安装 VSS：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 错误 -2147023841 [0x8007041F] - 退出代码 512

当 Azure Site Recovery VSS 提供程序服务数据库[被锁定](/previous-versions/ms833798(v=msdn.10))时会发生此错误。请尝试从命令提示符运行以下命令，以便手动在源计算机上安装 VSS：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

如果出现故障，请检查是否有任何防病毒程序或其他服务停滞在“正在启动”状态。 处于“正在启动”状态的进程可以保留对数据库服务的锁定。 这将导致安装 VSS 提供程序时出现故障。 请确保没有服务处于“正在启动”状态，然后重试上述操作。

### <a name="vss-exit-code-806"></a>VSS 退出代码 806

当用于安装的用户帐户无权执行 `CSScript` 命令时会发生此错误。 请为用户帐户提供执行此脚本所需的权限，然后重试操作。

### <a name="other-vss-errors"></a>其他 VSS 错误

请尝试从命令提示符运行以下命令，手动在源计算机上安装 VSS 提供程序服务：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS 错误 - 0x8004E00F

此错误通常出现在移动代理的安装过程中，其原因是 `DCOM` 出现问题，且 `DCOM` 处于某种严重状态。

使用以下过程确定错误原因。

### <a name="examine-the-installation-logs"></a>检查安装日志

1. 打开位于 C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log 中的安装日志。
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

解决 DCOM 问题后，从命令提示符使用以下命令手动重新安装 Azure Site Recovery VSS 提供程序：

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

如果应用程序一致性不是一项关键的灾难恢复要求，则可以绕过 VSS 提供程序的安装。

若要绕过 Azure Site Recovery VSS 提供程序的安装，并在安装后手动安装 Azure Site Recovery VSS 提供程序：

1. 安装移动服务。 安装将在此步骤失败：**安装后配置**。
1. 若要绕过 VSS 的安装：
   1. 打开位于以下位置的 Azure Site Recovery 移动服务安装目录：

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. 添加以下行，修改 Azure Site Recovery VSS 提供程序安装脚本 InMageVSSProvider_Install 和 InMageVSSProvider_Uninstall.cmd，这样就能始终成功：

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. 手动安装移动代理。
1. 当安装成功并转到下一步骤“配置”时，请删除添加的行。
1. 若要安装 VSS 提供程序，请以管理员身份打开命令提示符并运行以下命令：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. 在“Windows 服务”中验证 Azure Site Recovery VSS 提供程序是否已安装为一项服务。 打开组件服务 MMC 来确认 VSS 提供程序已列出。
1. 如果 VSS 提供程序安装仍然失败，请与技术支持人员一起解决加密应用程序编程接口 (CAPI2) 中的权限错误。

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>在非群集计算机上启用群集服务导致 VSS 提供程序安装失败

此问题导致 Azure Site Recovery 移动代理安装在 Azure Site Recovery VSS 提供程序安装过程中失败。 失败是因为 `COM+` 发生了阻止 VSS 提供程序安装的问题。

### <a name="to-identify-the-issue"></a>识别问题

在配置服务器上的 C:\ProgramData\ASRSetupLogs\UploadedLogs\<date-time>UA_InstallLogFile.log 日志中，会看到以下异常：

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

若要解决问题，请执行以下操作：

1. 确认此计算机是一台非群集计算机，并且未使用群集组件。
1. 如果未使用那些组件，请从计算机中删除群集组件。

## <a name="drivers-are-missing-on-the-source-server"></a>源服务器上缺少驱动程序

如果移动代理安装失败，请检查 C:\ProgramData\ASRSetupLogs 下的日志，以确定某些控制集中是否缺少某些必需的驱动程序。

若要解决问题，请执行以下操作：

1. 使用 `regedit.msc` 之类的注册表编辑器打开注册表。
1. 打开 `HKEY_LOCAL_MACHINE\SYSTEM` 节点。
1. 在 `SYSTEM` 节点中找到控制集。
1. 打开每个控制集并确认以下 Windows 驱动程序是否存在：

   * Atapi
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. 重新安装所有缺少的驱动程序。

## <a name="next-steps"></a>后续步骤

[详细了解](vmware-azure-tutorial.md)如何为 VMware VM 设置灾难恢复。
