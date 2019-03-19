---
title: 在为灾难恢复启用复制时，排查移动服务推送安装失败问题 | Microsoft Docs
description: 在为灾难恢复启用复制时，排查移动服务安装错误
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/27/2019
ms.openlocfilehash: 65b8253a307693d00f5eaefe7660d500dce49be4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078646"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>解决移动服务推送安装问题

安装移动服务是启用复制期间的关键步骤。 此步骤的成功完全取决于满足先决条件并使用支持的配置。 在移动服务安装期间最常遇到的失败是由于：

* [凭据/权限错误](#credentials-check-errorid-95107--95108)
* [登录失败](#login-failures-errorid-95519-95520-95521-95522)
* [连接错误](#connectivity-failure-errorid-95117--97118)
* [文件和打印机共享错误](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI 故障](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [操作系统不受支持](#unsupported-operating-systems)
* [启动配置不受支持](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS 安装失败](#vss-installation-failures)
* [GRUB 配置中是设备名称而不是设备 UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM 卷](#lvm-support-from-920-version)
* [重启警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

启用复制时，Azure Site Recovery 尝试在虚拟机上推送安装移动服务代理。 其间，配置服务器尝试连接虚拟机并复制代理。 要成功安装，请按照下面给出的分步故障排除指导进行操作。

## <a name="credentials-check-errorid-95107--95108"></a>凭据检查（ErrorID：95107 和 95108）

* 验证所选用户帐户在启用复制期间是否有效且准确。
* Azure Site Recovery 需要具有**管理员特权**的 **ROOT** 帐户或用户帐户来执行推送安装。 否则，系统会在源计算机上阻止推送安装。
  * 对于 Windows（**错误 95107**），验证该用户帐户是否具有源计算机上的本地或域管理访问权限。
  * 如果使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。
    * 若要禁用远程用户访问控制，请在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 注册表项下添加一个新 DWORD：LocalAccountTokenFilterPolicy。 将值设置为 1。 若要执行此步骤，从命令提示符处运行以下命令：

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * 对于 Linux（**错误 95108**），必须选择根帐户才能成功安装移动代理。 另外，SFTP 服务应处于运行状态。 在 sshd_config 文件中启用 SFTP 子系统和密码身份验证：
    1. 以 root 身份登录。
    2. 转到 /etc/ssh/sshd_config 文件中，找到以 PasswordAuthentication 开头的行。
    3. 取消注释该行，并将值更改为 yes。
    4. 找到以 Subsystem 开头的行，并取消注释该行。
    5. 重启 sshd 服务。

如果想要修改所选用户帐户的凭据，请按照[此处](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的说明进行操作。

## <a name="insufficient-privileges-failure-errorid-95517"></a>权限不足故障（ErrorID：95517）

如果选择用来安装移动代理的用户没有管理员权限，则配置服务器/横向扩展进程服务器就不得将移动代理软件复制到源计算机。 因此，此错误是拒绝服务故障导致的。 确保用户帐户有管理员权限。

如果想要修改所选用户帐户的凭据，请按照[此处](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的说明进行操作。

## <a name="insufficient-privileges-failure-errorid-95518"></a>权限不足故障（ErrorID：95518）

主域和工作站之间建立的域信任关系失败时尝试登录到源计算机时，移动代理安装将失败并出现错误 ID 95518。 因此，请确保用于安装移动代理的用户帐户具有管理权限登录源计算机的主域。

如果想要修改所选用户帐户的凭据，请按照[此处](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的说明进行操作。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>登录失败（错误 ID：95519、95520、95521、95522）

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>用户帐户的凭据已禁用（ErrorID：95519）

在“启用复制”期间选择的用户帐户已禁用。 若要启用用户帐户，请参阅[此处](https://aka.ms/enable_login_user)的文章，或者运行以下命令，将文本 *username* 替换为实际的用户名。
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>由于多次登录尝试均失败，凭据已锁定（ErrorID：95520）

在访问计算机时多次重试失败会导致系统锁定用户帐户。 失败可能是因为：

* 在配置设置过程中提供的凭据不正确，或者
* 在“启用复制”期间选择的用户帐户错误

因此，请根据[此处](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的说明修改所选的凭据，然后过一段时间再重试。

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>登录服务器在源计算机上不可用（ErrorID：95521）

登录服务器在源计算机上不可用时，会发生此错误。 登录服务器不可用会导致登录请求失败，因此不能安装移动代理。 若要成功进行登录，请确保登录服务器在源计算机上可用，然后启动登录服务。 有关详细说明，请参阅知识库[139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available)错误信息：有当前可用的登录服务器。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>登录服务未在源计算机上运行（ErrorID：95522）

登录服务未在源计算机上运行，导致登录请求失败。 因此，无法安装移动代理。 若要解决此问题，请确保登录服务在源计算机上运行，以便成功进行登录。 若要启动登录服务，请通过命令提示符运行命令“net start Logon”，或者从任务管理器启动“NetLogon”服务。

## <a name="connectivity-failure-errorid-95117--97118"></a>**连接失败（ErrorID：95117 和 97118）**

配置服务器/横向扩展进程服务器尝试连接到源 VM 以安装移动代理。 当源计算机因网络连接问题而无法访问时，会发生此错误。 若要解决问题，

* 请确保你能够从配置服务器对源计算机执行 ping 操作。 如果在启用复制期间选择了横向扩展进程服务器，请确保能够从进程服务器对源计算机执行 ping 操作。
  * 在源服务器计算机命令行中，使用 Telnet 通过 https 端口 (135) 对配置服务器/横向扩展进程服务器执行 ping 操作（如下所示），查看是否存在任何网络连接问题或防火墙端口阻止问题。

     `telnet <CS/ scale-out PS IP address> <135>`
* 此外，对于Linux VM，
  * 检查是否已安装最新的 openssh、openssh-server 和 openssl 包。
  * 检查并确保安全外壳 (SSH) 已启用且正在端口 22 上运行。
  * SFTP 服务应运行。 在 sshd_config 文件中启用 SFTP 子系统和密码身份验证，
    * 以 root 身份登录。
    * 转到 /etc/ssh/sshd_config 文件中，找到以 PasswordAuthentication 开头的行。
    * 取消注释该行，并将值更改为 yes
    * 找到以 Subsystem 开头的行，并取消注释该行
    * 重启 sshd 服务。
* 如果一段时间后没有正确的响应，则连接尝试可能失败，或者由于连接的主机响应失败。已建立的连接会失败。
* 它可能是与连接性/网络/域相关的问题。 它也可能是由 DNS 名称解析问题或 TCP 端口耗尽问题造成的。 请检查域中是否存在任何此类已知问题。

## <a name="connectivity-failure-errorid-95523"></a>连接失败（ErrorID：95523）

在源计算机所在的网络找不到的情况下，或者可能已删除或不再可用的情况下，会发生此错误。 若要解决此错误，唯一的方法是确保网络存在。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>文件和打印机共享服务检查（ErrorID：95105 和 95106）

进行连接性检查后，验证是否在虚拟机上启用了文件和打印机共享服务。 若要将移动代理复制到源计算机，这些设置是必需的。

对于 windows 2008 R2 和以前的版本，

* 若要通过 Windows 防火墙启用文件和打印共享，
  * 打开“控制面板”->“系统和安全”->“Windows 防火墙”，单击左侧窗格的“高级设置”，再单击控制台树中的“入站规则”。
  * 找到文件和打印机共享 (NB-Session-In) 以及文件和打印机共享 (SMB-In) 规则。 请依次右键单击每个规则，然后单击“启用规则”。
* 若要使用组策略启用文件共享，
  * 转到开始，键入 type gpmc.msc 并搜索。
  * 在导航窗格中，打开以下文件夹：本地计算机策略、用户配置、管理模板、Windows 组件和网络共享。
  * 在详细信息窗格中，双击“防止用户共享其配置文件中的文件”。 若要禁用组策略设置，并启用用户共享文件的功能，请单击“禁用”。 单击“确定”保存更改。 若要了解详细信息，请参阅[启用或禁用文件共享使用组策略](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

有关**更高版本**，按照中提供的说明[安装 Mobility service 以便将 VMware Vm 和物理服务器的灾难恢复](vmware-azure-install-mobility-service.md)启用文件和打印机共享。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) 配置检查（错误代码：95103）

进行文件和打印机服务检查后，通过防火墙为专用配置文件、公共配置文件和域配置文件启用 WMI 服务。 若要在源计算机上完成远程执行操作，这些设置是必需的。 若要启用，

* 请转到“控制面板”，单击“安全性”，然后单击“Windows 防火墙”。
* 单击“更改设置”，然后单击“异常”选项卡。
* 在异常窗口中，选中 Windows Management Instrumentation (WMI) 复选框，以通过防火墙启用 WMI 流量。 

还可以在命令提示符处通过防火墙启用 WMI 流量。 使用以下命令 `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
可以在以下文章中找到其他 WMI 故障排除文章。

* [基本 WMI 测试](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 故障排除](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [使用 WMI 脚本和 WMI 服务排查问题](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>操作系统不受支持

另一个最常见的失败原因可能是操作系统不受支持。 确保使用的是受支持的操作系统/内核版本，以便成功安装移动服务。 避免使用专用修补程序。
若要查看 Azure Site Recovery 支持的操作系统和内核版本列表，请参阅我们的[支持矩阵文档](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>启动磁盘配置不受支持（ErrorID：95309、95310、95311）

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>启动和系统分区/卷不是同一磁盘（ErrorID：95309）

在版本 9.20 之前，将启动和系统分区/卷置于不同磁盘上是不受支持的配置。 [版本 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) 开始支持此配置。 请使用最新版本来获取此支持。

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>启动磁盘不可用（ErrorID：95310)

无法保护没有启动盘的虚拟机。 这是为了确保在故障转移操作期间顺利恢复虚拟机。 缺少启动盘会导致故障转移后无法启动计算机。 确保虚拟机包含启动盘并重试该操作。 另请注意，不支持同一台计算机上有多个启动盘。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>源计算机上存在多个启动磁盘（ErrorID：95311)

具有多个启动盘的虚拟机不是[支持的配置](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>在多个磁盘上进行系统分区（ErrorID：95313）

在版本 9.20 之前，将根分区或卷置于不同磁盘上是不受支持的配置。 [版本 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) 开始支持此配置。 请使用最新版本来获取此支持。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>启用保护失败，因为 GRUB 配置中提到了设备名而非 UUID（ErrorID：95320)

**可能的原因：** </br>
GRUB 配置文件（“/boot/grub/menu.lst”、“/boot/grub/grub.cfg”、“/boot/grub2/grub.cfg”或“/etc/default/grub”）可能包含参数“root”和“resume”的值作为实际设备名而非 UUID。 Site Recovery 要求 UUID 方法，因为设备名可能会在 VM 重启时发生更改，由于故障转移时 VM 可能不会出现相同的名称，从而导致问题。 例如： </br>


- 以下行来自 GRUB 文件 /boot/grub2/grub.cfg。 <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- 以下行来自 GRUB 文件 /boot/grub/menu.lst
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

如果发现上面的粗体字符串，GRUB 具有参数“root”和“resume”的实际设备名，而不是 UUID。
 
**如何修复：**<br>
设备名应替换为相应的 UUID。<br>


1. 执行命令“blkid <device name>”找到设备的 UUID。 例如：<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. 现在请将设备名替换为设备 UUID，格式类似于“root=UUID=<UUID>”。 例如，如果我们替换为设备名称 UUID 根和恢复的文件中上面提到的参数"/ boot/grub2/grub.cfg"，"/ boot/grub2/grub.cfg"或"/ 等/默认/grub: 文件中的行如下所示。 <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. 再次重启保护

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>安装移动设备的操作完成，出现重启警告（ErrorID：95265 和 95266）

Site Recovery 移动服务有多个组件，其中一个称为筛选器驱动程序。 筛选器驱动程序只有在系统重启时才会加载到系统内存中。 这意味着筛选器驱动程序修补程序只有在加载新的筛选器驱动程序的时候才能够实现，即只有在系统重启时才能够实现。

**请注意**，这是一个警告，在新代理更新后，现有复制仍会继续进行。 您可以选择重新启动任何时候您想获得新的筛选器驱动程序，但如果不重新启动使用旧的筛选器驱动程序保留的好处。 因此，如果在更新后没有重启，则除了筛选器驱动器，**移动服务中的其他增强功能和修复程序的功能均可实现**。 因此，虽然建议，但它不一定要在每次升级后重新启动。 有关重新启动时必需的信息，设置[移动代理升级后的源计算机的重新启动](https://aka.ms/v2a_asr_reboot)Azure Site Recovery 中的服务更新中的部分。

> [!TIP]
>在维护时段内计划升级的最佳实践，请参阅[的最新 OS/内核版本的支持](https://aka.ms/v2a_asr_upgrade_practice)Azure Site Recovery 中的服务更新中。

## <a name="lvm-support-from-920-version"></a>版本 9.20 提供的 LVM 支持

在版本 9.20 之前，仅数据磁盘支持 LVM。 /boot 应位于磁盘分区上，而不是位于 LVM 卷上。

从[版本 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) 开始，支持[将 OS 磁盘置于 LVM 上](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。 请使用最新版本来获取此支持。

## <a name="insufficient-space-errorid-95524"></a>空间不足（ErrorID：95524）

将移动代理复制到源计算机时，需要至少 100 MB 的可用空间。 因此，请确保源计算机有必需的可用空间，然后重试此操作。

## <a name="vss-installation-failures"></a>VSS 安装失败

VSS 安装是移动代理安装的一部分。 在生成应用程序一致恢复点的过程中将使用此服务。 VSS 安装过程可能会由于多种原因而失败。 若要查明确切的错误，请参阅 **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**。 以下部分中重点介绍了几个常见的错误和解决方法步骤。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 错误 -2147023170 [0x800706BE] - 退出代码 511

当防病毒软件正在阻止 Azure Site Recovery 服务的操作时，通常会出现此问题。 若要解决此问题，请执行以下操作：

1. 排除[此处](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)提到的所有文件夹。
2. 按照你的防病毒软件提供商发布的指南在 Windows 中取消阻止 DLL 的注册。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 错误 7 [0x7] - 退出代码 511

这是一个运行时错误，并且是由于没有足够的内存可用于安装 VSS 导致的。 请确保增大磁盘空间以成功完成此操作。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 错误 -2147023824 [0x80070430] - 退出代码 517

当 Azure Site Recovery VSS 提供程序服务[标记为待删除](https://msdn.microsoft.com/en-us/library/ms838153.aspx)时会发生此错误。 请尝试通过运行以下命令行手动在源计算机上安装 VSS

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 错误 -2147023841 [0x8007041F] - 退出代码 512

当 Azure Site Recovery VSS 提供程序服务数据库[被锁定](https://msdn.microsoft.com/en-us/library/ms833798.aspx)时会发生此错误。请尝试通过运行以下命令行手动在源计算机上安装 VSS

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS 退出代码 806

当用于安装的用户帐户无权执行 CSScript 命令时会发生此错误。 请为用户帐户提供执行此脚本所需的权限，然后重试操作。

### <a name="other-vss-errors"></a>其他 VSS 错误

请尝试通过运行以下命令行手动在源计算机上安装 VSS 提供程序服务

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS 错误-0x8004E00F

通常在由于 DCOM 和 DCOM 将处于严重状态的问题的移动代理安装过程中遇到此错误。

使用以下过程来确定错误的原因。

**检查安装日志**

1. 打开安装日志位于 c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log。
2. 存在以下错误指示此问题：

    注销现有应用程序...创建的目录对象中获取的应用程序集合 

    错误:

    - 错误代码：-2147164145 [0x8004E00F]
    - 退出代码：802

若要解决问题，请执行以下操作：

请联系[Microsoft Windows 平台团队](https://aka.ms/Windows_Support)以获取解决 DCOM 问题的帮助。

解决 DCOM 问题后，请重新安装 Azure Site Recovery VSS 提供程序使用以下命令手动：
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
如果应用程序一致性并不重要的灾难恢复要求，可以跳过安装 VSS 提供程序。 

若要绕过 Azure Site Recovery VSS 提供程序安装并手动安装 Azure Site Recovery VSS 提供程序安装后：

1. 安装移动服务。 
   > [!Note]
   > 
   > 在安装后的配置步骤，则安装将失败。 
2. 若要绕过 VSS 安装：
   1. 打开位于 Azure Site Recovery 移动服务安装目录：
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. 修改 Azure Site Recovery VSS 提供程序安装脚本**nMageVSSProvider_Install**并**InMageVSSProvider_Uninstall.cmd**总是成功通过添加以下行：
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. 手动重新运行移动代理安装。 
4. 在安装成功，且将移到下一步**配置**，删除所添加的行。
5. 若要安装 VSS 提供程序，打开命令提示符下以管理员身份，并运行以下命令：
   
    **C:\Program 文件 (x86) \Microsoft Azure 站点 Recovery\agent >.\InMageVSSProvider_Install.cmd**

9. 验证 ASR VSS 提供程序已安装为 Windows 服务中的服务并打开组件服务 MMC 来验证列出了 ASR VSS 提供程序。
10. 如果 VSS 提供程序安装仍然失败，使用 CX 若要解决 CAPI2 中的权限错误。

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>由于群集服务在非群集计算机上启用 VSS 提供程序安装失败

此问题会导致 Azure Site Recovery 移动代理安装在 ASAzure 站点恢复 VSS 提供程序安装步骤因与 COM + 禁止安装 VSS 提供程序的问题而失败。
 
### <a name="to-identify-the-issue"></a>若要识别的问题

日志位于服务器上，配置在 C:\ProgramData\ASRSetupLogs\UploadedLogs 中\<日期时间 > UA_InstallLogFile.log，您会发现以下异常：

COM + 无法与 Microsoft 分布式事务处理协调器 (异常来自 HRESULT:0x8004E00F)

若要解决问题，请执行以下操作：

1.  验证此计算机是在非群集计算机和未使用群集组件。
3.  如果未使用的组件，从计算机中删除了群集组件。

## <a name="drivers-are-missing-on-the-source-server"></a>驱动程序是在源服务器上缺少

如果移动代理安装失败，则检查下 C:\ProgramData\ASRSetupLogs 以确定是否某些控件集中缺少一些必需驱动程序的日志。
 
若要解决问题，请执行以下操作：
  
1. 使用诸如 regedit.msc 的注册表编辑器，打开注册表。
2. 打开 HKEY_LOCAL_MACHINE\SYSTEM 节点。
3. 在系统节点中找到该控件集。
4. 打开每个控件集并验证以下 Windows 驱动程序存在：

   - Atapi
   - Vmbus
   - Storflt
   - Storvsc
   - intelide
 
重新安装任何缺少的驱动程序。

## <a name="next-steps"></a>后续步骤

[了解如何](vmware-azure-tutorial.md)为 VMware VM 设置灾难恢复。
