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
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d4d6d273001642ebb8c960333a1c01977e863dbc
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>解决移动服务推送安装问题

本文介绍当尝试在源服务器上安装 Azure Site Recovery 移动服务以启用保护时可能遇到的常见问题。

## <a name="error-code-95107-protection-could-not-be-enabled"></a>（错误代码 95107）无法启用保护
**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95107 </br>消息：移动服务到源计算机的推送安装失败，错误代码为 EP0858。 <br> 提供用于安装移动服务的凭据不正确或用户帐户没有足够权限。 | 为在源计算机上安装移动服务而提供的用户凭据不正确。 | 请确保为配置服务器上的源计算机提供的用户凭据正确。 <br> 若要添加/编辑用户凭据，请转到配置服务器并选择“Cspsconfigtool” > “管理帐户”。 </br> 此外，请检查以下先决条件以成功完成推送安装。

## <a name="error-code-95015-protection-could-not-be-enabled"></a>（错误代码 95015）无法启用保护
**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95105 </br>消息：移动服务到源计算机的推送安装失败，错误代码为 EP0856。 <br> 源计算机上不允许启用“文件和打印机共享”，或进程服务器和源计算机之间的网络连接出现问题。| 未启用“文件和打印机共享”。 | 在源计算机上的 Windows 防火墙中允许“文件和打印机共享”。 在源计算机上，在“Windows 防火墙” > “允许应用或功能通过防火墙”下，为所有配置文件选择“文件和打印机共享”。 </br> 此外，请检查以下先决条件以成功完成推送安装。

## <a name="error-code-95117-protection-could-not-be-enabled"></a>（错误代码 95117）无法启用保护
**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95117 </br>消息：移动服务到源计算机的推送安装失败，错误代码为 EP0865。 <br> 源计算机未运行，或进程服务器和源计算机之间的网络连接出现问题。 | 进程服务器和源服务器之间的网络连接性问题。 | 检查进程服务器和源服务器之间的连接性。 </br> 此外，请检查以下先决条件以成功完成推送安装。

## <a name="error-code-95103-protection-could-not-be-enabled"></a>（错误代码 95103）无法启用保护
**错误代码** | **可能的原因** | **错误特定建议**
--- | --- | ---
95103 </br>消息：移动服务到源计算机的推送安装失败，错误代码为 EP0854。 <br> 源计算机上不允许启用 Windows Management Instrumentation (WMI)，或进程服务器和源计算机之间的网络连接出现问题。| Windows 防火墙中阻止了 WMI。 | 在 Windows 防火墙中允许 WMI。 在“Windows 防火墙” > “允许应用或功能通过防火墙”下，选择“为所有配置文件选择 WMI”。 </br> 此外，请检查以下先决条件以成功完成推送安装。

## <a name="check-push-installation-logs-for-errors"></a>检查推送安装日志中的错误

在配置服务器/进程服务器上，转到位于 <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ 的 PushinstallService 文件，了解问题来源。 使用以下故障排除步骤解决问题。</br>

![推送安装日志](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-installation-prerequisites-for-windows"></a>适用于 Windows 的推送安装先决条件
### <a name="ensure-that-file-and-printer-sharing-is-enabled"></a>请确保启用“文件和打印机共享”。
在 Windows 防火墙中的源计算机上运行“文件和打印机共享”和“Windows Management Instrumentation”。 </br>
#### <a name="if-the-source-machine-is-domain-joined-br"></a>如果源计算机已加入域 </br>
通过使用组策略管理控制台配置防火墙设置。

1. 以管理员身份登录到 Azure Active Directory 域计算机。 打开组策略管理控制台（“开始” > “运行”）。</br>

2. 如果没有安装组策略管理控制台，请参阅[安装组策略管理控制台](https://technet.microsoft.com/library/cc725932.aspx)。 </br>

3. 在组策略管理控制台树中，双击林和域中的“组策略对象”。 转到“默认域策略”。 </br>

    ![默认域策略](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
4. 右键单击“默认域策略” > “编辑”。 新的“组策略管理编辑器”窗口随即打开。 </br>

    ![组策略管理编辑器](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
5. 在“组策略管理编辑器”中，转到“计算机配置” > “策略” > “管理模板” > “网络” > “网络连接” > “Windows 防火墙”。 </br>

    ![Windows 防火墙](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
6. 为“域配置文件”和“标准配置文件”启用以下设置： </br>

    a. 双击“Windows 防火墙: 允许入站文件和打印机共享异常”。 依次选择“已启用”和“确定”。 </br>

    b. 双击“Windows 防火墙: 允许入站远程管理异常”。 依次选择“已启用”和“确定”。 </br>

    ![域配置文件](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

#### <a name="if-the-source-machine-isnt-domain-joined-and-part-of-a-workgroup-br"></a>如果源计算机未加入域而属于工作组 </br>
在远程计算机上（为工作组）配置防火墙设置。

1. 转到源计算机。</br>

2. 在“Windows 防火墙” > “允许应用或功能穿过防火墙”下，为所有配置文件选择“文件和打印机共享”。 </br>

3. 在“Windows 防火墙” > “允许应用或功能通过防火墙”下，选择为所有配置文件选择“WMI”。 </br>

#### <a name="disable-remote-user-account-control"></a>禁用远程用户帐户控制
使用注册表项推送移动服务，以禁用用户帐户控制。

1. 选择“开始” > “运行”，键入“regedit”，然后选择“输入”。

2. 找到并选择以下注册表子项：HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System

3. 如果 LocalAccountTokenFilterPolicy 注册表项不存在，请按照下列步骤执行操作：

    a. 在“编辑”菜单上，选择“新建”，然后选择“DWORD 值”。

    b. 键入“LocalAccountTokenFilterPolicy”，然后选择“输入”。

    c. 右键单击“LocalAccountTokenFilterPolicy”，然后选择“修改”。

    d.单击“下一步”。 在“值”数据框中，键入“1”，然后选择“确定”。

4. 关闭注册表编辑器。


## <a name="push-installation-prerequisites-for-linux"></a>适用于 Linux 的推送安装先决条件

1. 在源 Linux 服务器上创建根用户。 （只能将此帐户用于推送安装和更新。）</br>

2. 确保源 Linux 服务器上的 /etc/hosts 文件包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。 </br>

3. 请确保在源 Linux 服务器上已安装最新的 openssh、openssh-server 和 openssl 包。 检查 ssh 端口 22 是否已启用并正在运行。 </br>

4. 检查源服务器上是否已存在代理的过时条目，卸载较旧的代理并重新启动服务器。 重新安装代理。 </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>启用 sshd_config 文件中的 SFTP 子系统和密码身份验证
1. 在源服务器上，以根用户身份登录。 </br>

2. 在 /etc/ssh/sshd_config 文件中，找到以“PasswordAuthentication”开头的行。 </br>

3. 取消注释该行，并将值从“no”更改为“yes”。 </br>

    ![PasswordAuthentication](./media/site-recovery-protection-common-errors/linux1.png)

4. 找到以“Subsystem”开头的行，并取消注释该行。 </br>

    ![子系统](./media/site-recovery-protection-common-errors/linux2.png)

5. 保存所做的更改并重启 sshd 服务。 </br>


## <a name="push-installation-checks-on-the-configurationprocess-server"></a>配置服务器/进程服务器上的推送安装检查
#### <a name="validate-credentials-for-discovery-and-installation"></a>验证用于发现和安装的凭据

1. 从配置服务器，启动 Cspsconfigtool。</br>

    ![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. 请确保用于保护的帐户在源计算机上具有管理员权限。 </br>

#### <a name="check-connectivity-between-the-process-server-and-the-source-server"></a>检查进程服务器和源服务器之间的连接性
1. 请确保进程服务器有 Internet 连接。

2. 通过使用 wbemtest.exe 验证 WMI连接。 </br>

    a. 在进程服务器上，选择“开始” > “运行” > “wbemtest.exe”。 “Windows Management Instrumentation 测试器”窗口随即打开，如下所示：</br>

      ![WMI 测试器](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
    b. 选择“连接”，然后在“命名空间”中输入源服务器 IP。

    c. 输入“用户名”和“密码”。 （如果源计算机已加入域，则按“domainName\username”格式提供域名和用户名。 如果源计算机位于工作组中，仅提供用户名。）

    d.单击“下一步”。 选择作为“数据包隐私性”的“身份验证”级别。 </br>

      ![数据包隐私](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 选择“连接”。 WMI 连接现将显示提供的数据，“Windows Management Instrumentation 测试器”窗口显示如下所示的内容： </br>

      ![WMI 测试器数据](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
   </br>
      如果 WMI 连接不成功，将显示错误消息。 如果 Windows 防火墙允许的应用中未启用“WMI/远程管理”，将出现以下屏幕快照显示的失败尝试： </br>

    ![WMI 测试器错误消息](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
   </br>

3. 检查 WMI 状态和连接。</br>

    a. 在配置服务器/进程服务器上，选择“开始” > “运行” > “wmimgmt.msc” > “操作” > “更多操作” > “连接到另一台计算机(源计算机)”。 </br>

    b. 输入用于保护帐户的凭据，并检查连接是否正常。 </br>

#### <a name="verify-that-network-shared-folders-of-the-source-machine-are-accessible-from-the-process-server-remotely-by-using-specified-credentials"></a>验证可使用指定凭据从进程服务器远程访问源计算机的网络共享文件夹

  1. 登录到进程服务器计算机，然后打开“文件资源管理器”。 在地址栏中，键入“\\\source-machine-ip\C$”。 按 **Enter**。 </br>

      ![文件资源管理器](./media/site-recovery-protection-common-errors/fileshare1.png) </br>

  2. 文件资源管理器将提示输入凭据。 输入用户名和密码，然后选“确认”。</br>

      * 如果源计算机已加入域，则按“domainName\username”格式输入域名和用户名。</br>

      * 如果源计算机在工作组中，则只输入用户名。 </br>

      ![“凭据”对话框](./media/site-recovery-protection-common-errors/fileshare2.png) </br>

  3. 如果成功连接，则可以从进程服务器远程查看源计算机的文件夹。 </br>

      ![文件夹视图](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> 如果连接不成功，则检查是否满足所有先决条件。
>

如果不想要打开“Windows Management Instrumentation”，也可以在源计算机上手动安装移动服务。</br> 

有关详细信息，请参阅[通过 GUI 手动安装移动服务](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)和[通过配置管理器指南安装](site-recovery-install-mobility-service-using-sccm.md)。 </br>

## <a name="next-steps"></a>后续步骤
- [启用 VMware 虚拟机的复制](vmware-walkthrough-enable-replication.md)

