---
title: "从 VMware 到 Azure 的 Azure Site Recovery 故障排除 | Microsoft 文档"
description: "解决复制 Azure 虚拟机时出现的错误"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1e2452ccc6d3f1859a39e1ee09cdde32f53767ba
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---
# <a name="troubleshooting-mobility-service-push-install-issues"></a>解决移动服务推送安装问题

本文详细介绍在尝试将移动服务安装到源服务器上以启用保护时面临的常见问题。

## <a name="error-code-95107-protection-could-not-be-enabled"></a>（错误代码 95107）无法启用保护。
**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95107 </br>消息 - 移动服务到源计算机的推送安装失败，错误代码为 EP0858。 <br> 提供用于安装移动服务的凭据不正确或用户帐户没有足够权限 | 提供用于在源计算机上安装移动服务的用户凭据不正确 | 请确保为配置服务器上的源计算机提供的用户凭据正确。 <br> 若要添加/编辑用户凭据：转到“配置服务器 > Cspsconfigtool 图标 > 管理帐户”。 </br> 此外，请确保检查以下先决条件以成功完成推送安装。

## <a name="error-code-95015-protection-could-not-be-enabled"></a>（错误代码 95015）无法启用保护。
**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95105 </br>消息 -*** 移动服务到源计算机的推送安装失败，错误代码为 EP0856。 <br> 源计算机上不允许启用“文件和打印机共享”，或进程服务器和源计算机之间的网络连接出现问题| 未启用文件和打印共享 | 在 Windows 防火墙中的源计算机上允许“文件和打印机共享”，请转到源计算机，在 Windows 防火墙设置下，选中“允许应用或功能通过防火墙”，然后为所有配置文件选择“文件和打印机共享”。 </br> 此外，请确保检查以下先决条件以成功完成推送安装。

## <a name="error-code-95117-protection-could-not-be-enabled"></a>（错误代码 95117）无法启用保护。
**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95117 </br>消息 - 移动服务到源计算机的推送安装失败，错误代码为 EP0865。 <br> 源计算机未运行，或进程服务器和源计算机之间的网络连接出现问题 | 进程服务器和源服务器之间的网络连接 | 检查进程服务器和源服务器之间的连接。 </br> 此外，请确保检查以下先决条件以成功完成推送安装。

## <a name="error-code-95103-protection-could-not-be-enabled"></a>（错误代码 95103）无法启用保护。
**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95103 </br>消息 - 移动服务到源计算机的推送安装失败，错误代码为 EP0854。 <br> 源计算机上不允许启用“Windows Management Instrumentation (WMI)”，或进程服务器和源计算机之间的网络连接出现问题| 在 Windows 防火墙中已阻止 Windows Management Instrumentation (WMI) | 请在 Windows 防火墙中允许 Windows Management Instrumentation (WMI)。 在 Windows 防火墙设置下 >“允许应用或功能通过防火墙”>“为所有配置文件选择 WMI”。 </br> 此外，请确保检查以下先决条件以成功完成推送安装。

## <a name="check-push-install-logs-for-errors"></a>检查推送安装日志是否有误

在配置/进程服务器上，导航到位于 <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ 的文件“PushinstallService”，了解问题的根源并使用以下故障排除步骤来解决此问题。</br>
![pushiinstalllogs](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-install-pre-requisites-for-windows"></a>Windows 的推送安装先决条件
### <a name="ensure-file-and-printer-sharing-is-enabled"></a>确保已启用“文件和打印机共享”
在 Windows 防火墙中的源计算机上允许“文件和打印机共享”和“Windows Management Instrumentation” </br>
#### <a name="if-source-machine-is-domain-joined-br"></a>如果源计算机已加入域： </br>
请使用组策略管理控制台 (GPMC) 配置防火墙设置。
1. 以管理员身份登录到 Active Directory 域计算机，并打开组策略管理控制台（GPMC.MSC，从“开始 > 运行”运行）。</br>
3. 如果未安装 GPMC，请按照此链接[安装 GPMC](https://technet.microsoft.com/library/cc725932.aspx) </br>
4. 在 GPMC 控制台树中，双击林和域中的组策略对象，并导航到“默认域策略”。 </br>
![gpmc1](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
5. 右键单击“默认域策略”>“编辑”，随后将打开一个新的“组策略管理编辑器”窗口。 </br>
![gpmc2](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
6. 在“组策略管理编辑器”中，导航到“计算机配置”>“策略”>“管理模板”>“网络”>“网络连接”>“Windows 防火墙”。 </br>
![gpmc3](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
7. 为域配置文件和标准配置文件启用以下设置 </br>
a) 双击异常“Windows 防火墙: 允许入站文件和打印机共享例外”。 选择“已启用”，然后单击“确定”。 </br>
b) 双击异常“Windows 防火墙: 允许入站远程管理例外”。 选择“已启用”，然后单击“确定”。 </br>
![gpmc4](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

###### <a name="if-source-machine-is-not-domain-joined-and-part-of-workgroup-br"></a>如果源计算机未加入域且不是工作组的一部分 </br>
在远程计算机上（为工作组）配置防火墙设置：
1. 转到源计算机，</br>
2. 在 Windows 防火墙设置下 >“允许应用或功能通过防火墙”> 为所有配置文件选择“文件和打印机共享”。 </br>
3. 在 Windows 防火墙设置下 >“允许应用或功能通过防火墙”>“为所有配置文件选择 WMI”。 </br>

#### <a name="disable-remote-user-account-control-uac"></a>禁用远程用户帐户控制 (UAC)
禁用 UAC 使用注册表项推送移动服务。
1. 单击“开始 > 运行 > 键入 regedit > ENTER”
2. 找到并单击以下注册表子项：HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. 如果 LocalAccountTokenFilterPolicy 注册表项不存在，请按照下列步骤执行操作：
4. 在“编辑”菜单 >“新建”> 单击 DWORD 值。
5. 键入 LocalAccountTokenFilterPolicy，然后按 Enter。
6. 右键单击 LocalAccountTokenFilterPolicy，然后单击“修改”。
7. 在“值”数据框中，键入 1，然后单击“确定”。
8. 启动注册表编辑器。


## <a name="push-install-pre-requisites-for-linux"></a>Linux 的推送安装先决条件：

1. 在源 Linux 服务器上创建根用户。 （只能将此帐户用于推送安装和更新）</br>
2. 确保源 Linux 服务器上的 /etc/hosts 文件包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。 </br>
3. 请确保在源 Linux 服务器上已安装最新的 openssh、openssh-server 和 openssl 包。 </br>
检查 ssh 端口 22 是否已启用并正在运行。 </br>
4. 检查源服务器上是否已存在代理的过时条目，卸载较旧的代理并重新启动服务器，重新安装代理。 </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>启用 sshd_config 文件中的 SFTP 子系统和密码身份验证
1. 在源服务器上，以根用户身份登录。 </br>
2. 在 /etc/ssh/sshd_config 文件中，</br> 找到以 PasswordAuthentication 开头的行。 </br>
3. d.单击“下一步”。   取消注释该行，并将值从“no”更改为“yes”。 </br>
![Linux1](./media/site-recovery-protection-common-errors/linux1.png)
4. 找到以“Subsystem”开头的行，并取消注释该行。 </br>
![Linux2](./media/site-recovery-protection-common-errors/linux2.png)
5. 保存所做的更改并重启 sshd 服务。 </br>

## <a name="push-installation-checks-on-configurationprocess-server"></a>配置/进程服务器上的推送安装检查。
#### <a name="validate-credentials-for-discovery-and-installation"></a>验证用于发现和安装的凭据

1. 在配置服务器上启动 Cspsconfigtool</br>
![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. 请确保用于保护的帐户在源计算机上具有管理员权限。 </br>

#### <a name="check-connectivity-between-process-server-and-source-server"></a>检查进程服务器和源服务器之间的连接
1. 确保进程服务器具有 Internet 连接。
2. 验证使用 wbemtest.exe 的 WMI 连接。 </br>
在进程服务器上，单击“开始”>“运行”>“wbemtest.exe”，随后将打开 Windows Management Instrumentation 测试器窗口，如下所示。</br>
   ![WMItestConnect1](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
单击“连接”，在命名空间输入用户名和密码中输入源服务器 IP（如果源计算机已加入域），并按“domainName\username”格式提供域名和用户名。 如果源计算机位于工作组中，仅提供用户名。）选择作为数据包隐私性的身份验证级别。 </br>
![WMItestConnect2](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
   单击“连接”。 现在 WMI 连接应成功，并应显示提供的数据和 Windows Management Instrumentation 测试器窗口，如下所示： </br>
   ![WMItestConnect3](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
</br>
   如果 WMI 连接未成功，将弹出一条错误消息。 如果 Windows 防火墙允许应用中未启用 WMI/远程管理，以下屏幕快照将显示尝试不成功。 </br>
   ![WMItestConnect4](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
</br>

3. 检查 WMI 状态和连接。</br>
在配置/进程服务器上， </br>
单击“开始”>“运行”>“wmimgmt.msc”>“操作”>“更多操作”，以连接到另一台计算机（源计算机）。 </br>
输入用于保护帐户的凭据，并检查连接是否正常。 </br>

#### <a name="verify-network-shared-folders-of-source-machine-is-accessible-from-process-server-ps-remotely-using-specified-credentials"></a>验证源计算机的网络共享文件夹是否可使用特定凭据从进程服务器 (PS) 进行远程访问。
  1. 登录到进程服务器 (PS) 计算机，打开“文件资源管理器”，在地址栏中键入“\\\source-machine-ip\C$”，然后单击 Enter。 </br>
  ![Fileshare1](./media/site-recovery-protection-common-errors/fileshare1.png) </br>
  2. 文件资源管理器将提示输入凭据。 输入用户名和密码，然后单击“确定”。</br>
   如果源计算机已加入域，则按“domainName\username”格式提供域名和用户名。</br>
   如果源计算机位于工作组中，仅提供“用户名”。 </br>
  ![Fileshare2](./media/site-recovery-protection-common-errors/fileshare2.png) </br>
  3. 如果成功连接，则可以从进程服务器 (PS) 远程查看源计算机的文件夹 </br>
  ![Fileshare3](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> 如果连接不成功，请检查是否满足所有先决条件。
>

如果不想要打开“Windows Management Instrumentation”，也可以在源计算机上手动安装移动服务。</br> [通过 GUI 手动安装移动服务](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) </br>
[通过配置管理器指南进行的安装](site-recovery-install-mobility-service-using-sccm.md) </br>

## <a name="next-steps"></a>后续步骤
- [启用 VMware 虚拟机的复制](vmware-walkthrough-enable-replication.md)

