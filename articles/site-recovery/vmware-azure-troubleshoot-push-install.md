---
title: 在为灾难恢复启用复制时，排查移动服务推送安装失败问题 | Microsoft Docs
description: 在为灾难恢复启用复制时，排查移动服务安装错误
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 01/18/2019
ms.openlocfilehash: e397540d33df8a509e10f52fde41fc178cdba67e
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411741"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>解决移动服务推送安装问题

安装移动服务是启用复制期间的关键步骤。 此步骤的成功完全取决于满足先决条件并使用支持的配置。 在移动服务安装期间最常遇到的失败是由于：

* 凭据/权限错误
* 登录失败
* 连接错误
* 操作系统不受支持
* VSS 安装失败

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

如果在尝试登录到源计算机时无法在主域和工作站之间建立域信任关系，则移动代理安装会失败，出现的错误 ID 为 95518。 因此，请确保用于安装移动代理的用户帐户具有管理员权限，以便通过源计算机的主域进行登录。

如果想要修改所选用户帐户的凭据，请按照[此处](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的说明进行操作。

## <a name="login-failure-errorid-95519"></a>登录失败（ErrorID：95519）

在“启用复制”期间选择的用户帐户已禁用。 若要启用用户帐户，请参阅[此处](https://aka.ms/enable_login_user)的文章，或者运行以下命令，将文本 *username* 替换为实际的用户名。
`net user 'username' /active:yes`

## <a name="login-failure-errorid-95520"></a>登录失败（ErrorID：95520）

在访问计算机时多次重试失败会导致系统锁定用户帐户。 失败可能是因为：

* 在配置设置过程中提供的凭据不正确，或者
* 在“启用复制”期间选择的用户帐户错误

因此，请根据[此处](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的说明修改所选的凭据，然后过一段时间再重试。

## <a name="login-failure-errorid-95521"></a>登录失败（ErrorID：95521）

登录服务器在源计算机上不可用时，会发生此错误。 登录服务器不可用会导致登录请求失败，因此不能安装移动代理。 若要成功进行登录，请确保登录服务器在源计算机上可用，然后启动登录服务。 有关详细说明，请单击[此处](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available)。

## <a name="login-failure-errorid-95522"></a>登录失败（ErrorID：95522）

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
  * 在详细信息窗格中，双击“防止用户共享其配置文件中的文件”。 若要禁用组策略设置，并启用用户共享文件的功能，请单击“禁用”。 单击“确定”保存更改。 若要了解详细信息，请单击[此处](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

对于更高版本，请按照[此处](vmware-azure-install-mobility-service.md)提供的说明来启用文件和打印机共享。

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

## <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>启动和系统分区/卷不是同一磁盘（ErrorID：95309）

在版本 9.20 之前，将启动和系统分区/卷置于不同磁盘上是不受支持的配置。 [版本 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) 开始支持此配置。 请使用最新版本来获取此支持。

## <a name="boot-disk-not-found-errorid-95310"></a>找不到启动盘 (ErrorID:95310)

无法保护没有启动盘的虚拟机。 这是为了确保在故障转移操作期间顺利恢复虚拟机。 缺少启动盘会导致故障转移后无法启动计算机。 确保虚拟机包含启动盘并重试该操作。 另请注意，不支持同一台计算机上有多个启动盘。

## <a name="multiple-boot-disks-found-errorid-95311"></a>找到多个启动盘 (ErrorID:95311)

具有多个启动盘的虚拟机不是[支持的配置](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>在多个磁盘上进行系统分区（ErrorID：95313）

在版本 9.20 之前，将根分区或卷置于不同磁盘上是不受支持的配置。 [版本 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) 开始支持此配置。 请使用最新版本来获取此支持。

## <a name="grub-uuid-failure-errorid-95320"></a>GRUB UUID 失败 (ErrorID: 95320)

如果源计算机的 GRUB 使用设备名称而不 UUID，则移动代理安装将失败。 若要更改 GRUB 文件，请与系统管理员联系。

## <a name="lvm-support-from-920-version"></a>版本 9.20 提供的 LVM 支持

在版本 9.20 之前，仅数据磁盘支持 LVM。 /boot 应位于磁盘分区上，而不是位于 LVM 卷上。

从[版本 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) 开始，支持[将 OS 磁盘置于 LVM 上](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。 请使用最新版本来获取此支持。

## <a name="insufficient-space-errorid-95524"></a>空间不足（ErrorID：95524）

将移动代理复制到源计算机时，需要至少 100 MB 的可用空间。 因此，请确保源计算机有必需的可用空间，然后重试此操作。

## <a name="vss-installation-failures"></a>VSS 安装失败

VSS 安装是移动代理安装的一部分。 在生成应用程序一致恢复点的过程中将使用此服务。 VSS 安装过程可能会由于多种原因而失败。 若要查明确切的错误，请参阅 **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**。 以下部分中重点介绍了几个常见的错误和解决方法步骤。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 错误 -2147023170 [0x800706BE] - 退出代码 511

当防病毒软件阻止了 Azure Site Recovery 服务的操作时通常会出现此问题。 若要解决此问题，请执行以下操作：

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

## <a name="next-steps"></a>后续步骤

[了解如何](vmware-azure-tutorial.md)为 VMware VM 设置灾难恢复。
