---
title: 排查使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 时的配置服务器问题 | Microsoft Docs
description: 本文提供用于部署配置服务器以便使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 的故障排除信息。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/14/2019
ms.author: ramamill
ms.openlocfilehash: 0eebfd8b75f428d3b8f6024ed6ee71c18c1309f6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435968"
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

若要解决 vCenter 发现失败，请确保将 vCenter 服务器添加到 byPass 列表代理设置。若要执行此活动，请执行以下操作：

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

