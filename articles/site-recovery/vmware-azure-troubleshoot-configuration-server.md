---
title: 排查使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 时的配置服务器问题 | Microsoft Docs
description: 本文提供用于部署配置服务器以便使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 的故障排除信息。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: ab72091c58420459620352c8169773111149316d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245722"
---
# <a name="troubleshoot-configuration-server-issues"></a>排查配置服务器问题

本文可帮助你排查在部署和管理 [Azure Site Recovery](site-recovery-overview.md) 配置服务器时遇到的问题。 配置服务器充当管理服务器。 使用配置服务器可以设置通过 Site Recovery 将本地 VMware VM 和物理服务器灾难恢复到 Azure。 以下部分将介绍在添加新的配置服务器和管理配置服务器时可能会遇到的最常见失败。

## <a name="registration-failures"></a>注册失败

安装移动代理时，源计算机将注册到配置服务器。 可遵循以下指导原则调试执行此步骤期间发生的任何失败：

1. 打开 C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log 文件。 （ProgramData 文件夹可能已隐藏。 如果未看到 ProgramData 文件夹，请在文件资源管理器的“查看”选项卡上的“显示/隐藏”部分，选中“隐藏的项目”复选框）。失败可能是多个问题造成的。

2. 搜索字符串 **No Valid IP Address found**。 如果找到了该字符串：
    1. 验证请求的主机 ID 是否与源计算机的主机 ID 相同。
    2. 验证是否为源计算机的物理 NIC 至少分配了一个 IP 地址。 若要将代理成功注册到配置服务器，必须为源计算机的物理 NIC 至少分配一个有效的 IPV4 地址。
    3. 在源计算机上运行以下命令以获取源计算机的所有 IP 地址：
      - 对于 Windows：`> ipconfig /all`
      - 对于 Linux：`# ifconfig -a`

3. 如果找不到字符串 **No Valid IP Address found**，请搜索字符串 **Reason=>NULL**。 如果源计算机使用空主机注册到配置服务器，则会发生此错误。 如果找到了该字符串：
    - 解决问题后，遵照[将源计算机注册到配置服务器](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中的指导原则重试手动注册。

4. 如果找不到字符串 **Reason=>NULL**，请在源计算机上打开 C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log 文件。 （ProgramData 文件夹可能已隐藏。 如果未看到 ProgramData 文件夹，请在文件资源管理器的“查看”选项卡上的“显示/隐藏”部分，选中“隐藏的项目”复选框）。失败可能是多个问题造成的。 

5. 搜索字符串 **post request:(7) - Couldn't connect to server**。 如果找到了该字符串：
    1. 解决源计算机与配置服务器之间的网络问题。 验证是否可以使用 ping、traceroute 或 Web 浏览器等网络工具从源计算机访问配置服务器。 确保源计算机可以通过端口 443 访问配置服务器。
    2. 检查源计算机上是否有任何防火墙规则正在阻止源计算机与配置服务器之间的连接。 咨询网络管理员来消除任何连接问题。
    3. 确保从防病毒软件中排除[要从防病毒程序中排除的 Site Recovery 文件夹](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)中列出的文件夹。
    4. 解决网络问题后，遵照[将源计算机注册到配置服务器](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中的指导原则重试注册。

6. 如果未找到字符串 **post request:(7) - Couldn't connect to server**，请在同一日志文件中查找字符串 **request:(60) - Peer certificate cannot be authenticated with given CA certificates**。 如果配置服务器证书已过期，或者源计算机不支持 TLS 1.0 或更高版本的 SSL 协议，则可能会发生此错误。 如果防火墙阻止了源计算机与配置服务器之间的 SSL 通信，则也可能会发生此错误。 如果找到了该字符串： 
    1. 若要解决此问题，请在源计算机上使用 Web 浏览器连接到配置服务器 IP 地址。 请使用 URI https:\/\/<配置服务器 IP 地址\>:443/。 确保源计算机可以通过端口 443 访问配置服务器。
    2. 检查是否需要在源计算机上添加或删除任何防火墙规则，使源计算机能够与配置服务器通信。 由于使用的防火墙软件多种多样，我们无法列出全部所需的防火墙配置。 咨询网络管理员来消除任何连接问题。
    3. 确保从防病毒软件中排除[要从防病毒程序中排除的 Site Recovery 文件夹](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)中列出的文件夹。  
    4. 解决问题后，遵照[将源计算机注册到配置服务器](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中的指导原则重试注册。

7. 在 Linux 上，如果 <INSTALLATION_DIR\>/etc/drscout.conf 中的平台值已损坏，则注册将会失败。 若要识别此问题，请打开 /var/log/ua_install.log 文件。 搜索字符串 **Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure**。 平台应设置为 **VmWare** 或 **Azure**。 如果 drscout.conf 文件已损坏，我们建议[卸载移动代理](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)，然后重新安装移动代理。 如果卸载失败，请完成以下步骤：
    1. 打开 Installation_Directory/uninstall.sh 文件，并注释掉对 **StopServices** 函数的调用。
    2. 打开 Installation_Directory/Vx/bin/uninstall.sh 文件，并注释掉对 **stop_services** 函数的调用。
    3. 打开 Installation_Directory/Fx/uninstall.sh 文件，并注释掉尝试停止 Fx 服务的整个节。
    4. [卸载](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)移动服务。 成功卸载后，重新启动系统，然后尝试安装移动代理。

## <a name="installation-failure-failed-to-load-accounts"></a>安装失败：无法加载帐户

如果服务在安装移动代理和注册到配置服务器时无法通过传输连接读取数据，则会发生此错误。 若要解决此问题，请确保在源计算机上启用 TLS 1.0。

## <a name="vcenter-discovery-failures"></a>vCenter 发现失败

若要解决 vCenter 发现失败问题，请向 byPass 列表代理设置添加 vCenter 服务器。 

- 从[此处](https://aka.ms/PsExec)下载 PsExec 工具来访问系统用户内容。
- 通过运行以下命令行在系统用户内容中打开 Internet Explorer：psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- 在 IE 中添加代理设置并重启 tmanssvc 服务。
- 若要配置 DRA 代理设置，请运行 cd C:\Program Files\Microsoft Azure Site Recovery Provider
- 接下来，执行 DRCONFIGURATOR.EXE /configure /AddBypassUrls [添加在[配置服务器部署](vmware-azure-deploy-configuration-server.md#configure-settings)的**配置 vCenter 服务器/vSphere ESXi 服务器**步骤中提供的 vCenter 服务器 IP 地址/FQDN]

## <a name="change-the-ip-address-of-the-configuration-server"></a>更改配置服务器的 IP 地址

我们强烈建议不要更改配置服务器的 IP 地址。 确保分配给配置服务器的所有 IP 是静态 IP 地址。 不要使用 DHCP IP 地址。

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008:SAML 令牌无效

若要避免此错误，请确保系统时钟上的时间与本地时间之间的偏差不超过 15 分钟。 重新运行安装程序完成注册。

## <a name="failed-to-create-a-certificate"></a>无法创建证书

无法创建用于在 Site Recovery 中进行身份验证的证书。 确保以本地管理员的身份运行安装程序后，重新运行安装程序。

## <a name="failure-to-activate-windows-licence-from-server-standard-evaluation-to-server-standard"></a>未能从 Server Standard EVALUATION 向 Server Standard 激活Windows 许可证

1. 作为通过 OVF 部署配置服务器的一部分，使用了评估许可证，该许可证的有效期为 180 天。 需要在此许可证过期之前进行激活。 否则，这可能导致配置服务器频繁关闭，因而妨碍复制活动。
2. 如果无法激活 Windows 许可证，请联系 [Windows 支持团队](https://aka.ms/Windows_Support)以解决此问题。

## <a name="register-source-machine-with-configuration-server"></a>将源计算机注册到配置服务器

### <a name="if-the-source-machine-runs-windows"></a>如果源计算机运行 Windows

在源计算机上运行以下命令：

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

设置 | 详细信息
--- | ---
使用情况 | UnifiedAgentConfigurator.exe  /CSEndPoint <配置服务器 IP 地址\> /PassphraseFilePath <通行短语文件路径\>
代理配置日志 | 位于 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 下。
/CSEndPoint | 必需的参数。 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
/PassphraseFilePath |  必需。 通行短语的位置。 使用任何有效的 UNC 或本地文件路径。

### <a name="if-the-source-machine-runs-linux"></a>如果源计算机运行 Linux

在源计算机上运行以下命令：

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

设置 | 详细信息
--- | ---
使用情况 | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <配置服务器 IP 地址\> -P <通行短语文件路径\>
-i | 必需的参数。 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
-P |  必需。 通行短语所保存到的文件的完整文件路径。 使用任何有效文件夹。

## <a name="unable-to-configure-the-configuration-server"></a>无法配置配置服务器

如果在虚拟机上安装配置服务器以外的应用程序，则可能会无法配置主目标。 

配置服务器必须是单一用途服务器，并且不支持将其用作共享服务器。 

有关详细信息，请参阅[部署配置服务器](vmware-azure-deploy-configuration-server.md#faq)中的配置常见问题解答。 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>从配置服务器数据库中删除受保护项的过时条目 

若要删除配置服务器上过时的受保护计算机，请使用以下步骤。 
 
1. 确定过时条目的源计算机和 IP 地址： 

    1. 在管理员模式下打开 MYSQL 命令行。 
    2. 执行以下命令。 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        这将返回已注册计算机的列表及其 IP 地址和上次检测信号。 查找具有过时复制对的主机。

2. 打开提升的命令提示符并导航到 C:\ProgramData\ASR\home\svsystems\bin。 
4. 若要从配置服务器删除已注册主机详细信息和过时条目信息，请使用过时条目的源计算机和 IP 地址运行以下命令。 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    如果源服务器条目为“OnPrem-VM01”且 ipaddress 为10.0.0.4，则改为使用以下命令。
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. 在源计算机上重启以下服务，向配置服务器重新注册。 
 
    - InMage Scout 应用程序服务
    - InMage Scout VX Agent - Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>服务无法停止时升级失败

如果特定服务无法停止，则配置服务器升级失败。 

若要确定问题，请导航到配置服务器上的 C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile。 如果发现以下错误，请使用以下步骤解决问题： 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

若要解决问题，请执行以下操作：

手动停止以下服务：

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost， 
- Microsoft Azure 恢复服务代理， 
- Microsoft Azure Site Recovery 服务， 
- tmansvc
  
若要更新配置服务器，请再次运行[统一安装程序](service-updates-how-to.md#links-to-currently-supported-update-rollups)。

## <a name="azure-active-directory-application-creation-failure"></a>Azure Active Directory 应用程序创建失败

没有在 Azure Active Directory (AAD) 中使用[开放虚拟化应用程序 (OVA)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
) 模板创建应用程序的足够权限。

若要解决问题，请登录 Azure 门户并执行以下操作之一：

- 在 AAD 中请求应用程序开发人员角色。 有关应用程序开发人员角色的详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。
- 验证并确保 AAD 中的“用户可以创建应用程序”标志设置为“true”。 有关更多信息，请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>进程服务器/主目标无法与配置服务器通信 

进程服务器 (PS) 和主目标 (MT) 模块无法与配置服务器 (CS) 通信，并且它们的状态在 Azure 门户上显示为未连接。

这通常是由于端口 443 出错。 使用以下步骤取消阻止该端口并重新启用与 CS 的通信。

**验证并确保 MARS 代理正在被主目标代理调用**

若要验证并确保主目标代理可以为配置服务器 IP 创建 TCP 会话，请在主目标代理日志中查找类似于以下内容的跟踪：

TCP <Replace IP with CS IP here>:52739 <Replace IP with CS IP here>:443 SYN_SENT 

TCP    192.168.1.40:52739     192.168.1.40:443      SYN_SENT  // 此处将 IP 替换为 CS IP

如果在 MT 代理日志中发现类似于以下内容的跟踪，则 MT 代理将报告端口 443 出错：

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
如果其他应用程序也在使用端口 443，或由于阻止端口的防火墙设置，可能会遇到此错误。

若要解决问题，请执行以下操作：

- 验证并确保防火墙未阻止端口 443。
- 如果由于其他应用程序使用该端口而导致端口无法访问，请停止并卸载该应用。
  - 如果停止应用不可行，请设置新的干净 CS。
- 重启配置服务器。
- 重启 IIS 服务。

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>配置服务器因 UUID 条目不正确而没有连接

当数据库中有多个配置服务器 (CS) 实例 UUID 条目时，可能会发生此错误。 此问题经常在克隆配置服务器 VM 时发生。

若要解决问题，请执行以下操作：

1. 从 vCenter 中删除过时/陈旧的 CS VM。 有关详细信息，请参阅[删除服务器并禁用保护](site-recovery-manage-registration-and-protection.md)。
2. 登录配置服务器 VM 并连接到 MySQL svsdb1 数据库。 
3. 执行以下查询：

    > [!IMPORTANT]
    >
    > 验证并确保输入的是克隆配置服务器的 UUID 详细信息，或不再用于保护虚拟机的配置服务器过时条目。 输入不正确的 UUID 将导致丢失所有现有受保护项的信息。
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. 刷新门户页面。

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>输入凭据时会出现无限登录循环

在配置服务器 OVF 上输入正确的用户名和密码后，Azure 登录将继续提示输入正确的凭据。

系统时间不正确时可能会发生此问题。

若要解决问题，请执行以下操作：

在计算机上设置正确的时间并重试登录。 
 