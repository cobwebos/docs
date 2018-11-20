---
title: 在为灾难恢复启用复制时，排查移动服务推送安装失败问题 | Microsoft Docs
description: 在为灾难恢复启用复制时，排查移动服务安装错误
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 10/29/2018
ms.openlocfilehash: 2051f37656b6717c879a24f6e06c31a0ade0b950
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012320"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>解决移动服务推送安装问题

安装移动服务是启用复制期间的关键步骤。 此步骤的成功完全取决于满足先决条件并使用支持的配置。 在移动服务安装期间最常遇到的失败是由于

* 凭据/权限错误
* 连接错误
* 操作系统不受支持

启用复制时，Azure Site Recovery 尝试在虚拟机上推送安装移动服务代理。 其间，配置服务器尝试连接虚拟机并复制代理。 要成功安装，请按照下面给出的分步故障排除指导进行操作。

## <a name="credentials-check-errorid-95107--95108"></a>凭据检查 (ErrorID：95107 & 95108)

* 验证所选用户帐户在启用复制期间是否有效且准确。
* Azure Site Recovery 需要管理员特权来执行推送安装。
  * 对于 Windows，验证该用户帐户是否具有源计算机上的本地或域管理访问权限。
  * 如果使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。
    * 若要禁用远程用户访问控制，请在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 注册表项下添加一个新 DWORD：LocalAccountTokenFilterPolicy。 将值设置为 1。 若要执行此步骤，从命令提示符处运行以下命令：

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * 对于 Linux，必须选择根帐户才能成功安装移动代理。

如果想要修改所选用户帐户的凭据，请按照[此处](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的说明进行操作。

## <a name="connectivity-check-errorid-95117--97118"></a>连接性检查 (ErrorID：95117 & 97118)

* 请确保你能够从配置服务器对源计算机执行 ping 操作。 如果在启用复制期间选择了横向扩展进程服务器，请确保能够从进程服务器对源计算机执行 ping 操作。
  * 在源服务器计算机命令行中，使用 Telnet 通过 https 端口（默认为 9443）对配置服务器/横向扩展进程服务器执行 ping 操作（如下所示），查看是否存在任何网络连接问题或防火墙端口阻止问题。

     `telnet <CS/ scale-out PS IP address> <port>`

  * 如果无法连接，请允许配置服务器/横向扩展进程服务器上的入站端口 9443。
  * 检查 InMage Scout VX Agent – Sentinel/Outpost 服务的状态。 如果服务未运行，请启动服务。

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

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>文件和打印机共享服务检查 (ErrorID：95105 & 95106)

进行连接性检查后，验证是否在虚拟机上启用了文件和打印机共享服务。

对于 windows 2008 R2 和以前的版本，

* 若要通过 Windows 防火墙启用文件和打印共享，
  * 打开“控制面板”->“系统和安全”->“Windows 防火墙”，单击左侧窗格的“高级设置”，再单击控制台树中的“入站规则”。
  * 找到文件和打印机共享 (NB-Session-In) 以及文件和打印机共享 (SMB-In) 规则。 请依次右键单击每个规则，然后单击“启用规则”。
* 若要使用组策略启用文件共享，
  * 转到开始，键入 type gpmc.msc 并搜索。
  * 在导航窗格中，打开以下文件夹：本地计算机策略、用户配置、管理模板、Windows 组件和网络共享。
  * 在详细信息窗格中，双击“防止用户共享其配置文件中的文件”。 若要禁用组策略设置，并启用用户共享文件的功能，请单击“禁用”。 单击“确定”保存更改。 若要了解详细信息，请单击[此处](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

对于更高版本，请按照[此处](vmware-azure-install-mobility-service.md)提供的说明来启用文件和打印机共享。

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Windows Management Instrumentation (WMI) 配置检查

进行文件和打印机服务检查后，通过防火墙启用 WMI 服务。

* 在“控制面板”中，单击“安全性”，然后单击“Windows 防火墙”。
* 单击“更改设置”，然后单击“异常”选项卡。
* 在异常窗口中，选中 Windows Management Instrumentation (WMI) 复选框，以通过防火墙启用 WMI 流量。 

还可以在命令提示符处通过防火墙启用 WMI 流量。 使用以下命令 `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
可以在以下文章中找到其他 WMI 故障排除文章。

* [基本 WMI 测试](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 故障排除](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [使用 WMI 脚本和 WMI 服务排查问题](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>操作系统不受支持

另一个最常见的失败原因可能是操作系统不受支持。 确保使用的是受支持的操作系统/内核版本，以便成功安装移动服务。

若要了解有关哪些操作系统支持 Azure Site Recovery 的信息，请参阅[支持矩阵文档](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="next-steps"></a>后续步骤

[了解如何](vmware-azure-tutorial.md)为 VMware VM 设置灾难恢复。