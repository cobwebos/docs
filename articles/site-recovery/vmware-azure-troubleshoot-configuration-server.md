---
title: 使用 Azure Site Recovery 排查将 VMware VM 和物理服务器灾难恢复到 Azure 时的配置服务器问题 | Microsoft Docs
description: 本文提供用于部署配置服务器以便使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 的故障排除信息。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998959"
---
# <a name="troubleshoot-configuration-server-issues"></a>排查配置服务器问题

本文可帮助你排查在部署和管理 [Azure Site Recovery](site-recovery-overview.md) 配置服务器时遇到的问题。 配置服务器充当管理服务器，为本地 VMware VM 和物理服务器设置使用 Site Recovery 灾难恢复到 Azure 时，可以使用该服务器。 以下部分将介绍在添加新的配置服务器和管理配置服务器时可能会遇到的最常见失败。

## <a name="registration-failures"></a>注册失败

在安装移动代理期间将源计算机注册到配置服务器。 可根据下面提供的指导原则，调试执行此步骤期间发生的任何失败：

1. 转到 C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log 文件。 ProgramData 可能是隐藏的文件夹。 如果找不到它，请尝试取消隐藏该文件夹。 失败可能是多个问题造成的。
2. 搜索字符串“No Valid IP Address found”。 如果找到了该字符串：
    - 请验证请求的主机 ID 是否与源计算机相同。
    - 应该为源计算机的物理 NIC 至少分配一个 IP 地址，这样，代理才能成功注册到配置服务器 (CS)。
    - 在源计算机上运行命令 `> ipconfig /all`（适用于 Windows OS）或 `# ifconfig -a`（适用于 Linux OS）获取源计算机的所有 IP 地址。
    - 请注意，必须为物理 NIC 分配有效的 IPV4 地址才能注册代理。
3. 如果找不到上述字符串，请搜索字符串 "Reason"=>"NULL"。 如果找到：
    - 当源计算机使用空主机注册到配置服务器时，会发生此错误。
    - 解决问题后，遵照[此处](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)提供的指导原则重试手动注册。
4. 如果找不到上述字符串，请转到源计算机，并检查日志 C:\ProgramData\ASRSetupLogs\UploadedLogs\*ASRUnifiedAgentInstaller.log。ProgramData 可能是隐藏的文件夹。 如果找不到它，请尝试取消隐藏该文件夹。 失败可能是多个问题造成的。 搜索字符串“post request:(7) - Couldn't connect to server”。 如果找到：
    - 解决源计算机与配置服务器之间的网络问题。 验证是否可以使用 ping、traceroute、Web 浏览器等网络工具从源计算机访问配置服务器。确保源计算机可以通过端口 443 访问配置服务器。
    - 检查源计算机上是否有任何防火墙规则正在阻止源计算机与配置服务器之间的连接。 咨询网络管理员来消除连接问题。
    - 确保从防病毒软件中排除[此处](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)所述的文件夹。
    - 解决网络问题后，遵照[此处](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)提供的指导原则重试注册。
5. 如果找不到该字符串，请在同一日志中查找字符串“request:(60) - Peer certificate cannot be authenticated with given CA certificates”。 如果找到： 
    - 此错误的可能原因是配置服务器证书已过期，或者源计算机不支持 TLS 1.0 和更高版本的 SSL 协议，或者防火墙正在阻止源计算机与配置服务器之间的 SSL 通信。
    - 若要解决此问题，请借助 URI https://<CSIPADDRESS>:443/，在源计算机上使用 Web 浏览器连接到配置服务器 IP 地址。 确保源计算机可通过端口 443 访问配置服务器。
    - 检查源计算机上是否有任何防火墙规则正在阻止源计算机与配置服务器之间的连接。 咨询网络管理员来消除连接问题。
    - 确保从防病毒软件中排除[此处](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)所述的文件夹。  
    - 解决问题后，遵照[此处](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)提供的指导原则重试注册。
6. 在 Linux 中，如果 <INSTALLATION_DIR>/etc/drscout.conf 中的平台值已损坏，则注册将会失败。 若要识别此问题，请转到日志 /var/log/ua_install.log。 查找字符串“Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure”。 平台应设置为“VmWare”或“Azure”。 由于 drscout.conf 文件已损坏，我们建议[卸载](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)移动代理并重新安装。 如果卸载失败，请遵循以下步骤：
    - 打开文件 Installation_Directory/uninstall.sh 并注释掉对函数 *StopServices* 的调用
    - 打开文件 Installation_Directory/Vx/bin/uninstall 并注释掉对函数 `stop_services` 的调用
    - 打开文件 Installation_Directory/Fx/uninstall 并注释掉尝试停止 Fx 服务的整个节。
    - 现在，请尝试[卸载](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)移动代理。 成功卸载后，重新启动系统，然后重试安装代理。

## <a name="installation-failure---failed-to-load-accounts"></a>安装失败 - 无法加载帐户

如果在安装移动代理和注册到配置服务器期间服务无法通过传输连接读取数据，则会发生此错误。 若要解决此问题，请确保在源计算机上启用 TLS 1.0。

## <a name="change-ip-address-of-configuration-server"></a>更改配置服务器的 IP 地址

强烈建议不要更改配置服务器的 IP 地址。 确保分配给配置服务器的所有 IP 是静态 IP，而不是 DHCP IP。

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008:SAML 令牌无效

若要避免此错误，请确保系统时钟上的时间与本地时间之间的偏差不超过 15 分钟。 重新运行安装程序完成注册。

## <a name="failed-to-create-certificate"></a>无法创建证书

无法创建用于在 Site Recovery 中进行身份验证的证书。 确保以本地管理员的身份运行安装程序后，重新运行安装程序。

## <a name="register-source-machine-with-configuration-server"></a>将源计算机注册到配置服务器

### <a name="if-source-machine-has-windows-os"></a>如果源计算机使用 Windows OS

在源计算机上运行以下命令

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**设置** | **详细信息**
--- | ---
使用情况 | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
代理配置日志 | 位于 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 下。
/CSEndPoint | 必需的参数。 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
/PassphraseFilePath |  必需。 通行短语的位置。 使用任何有效的 UNC 或本地文件路径。

### <a name="if-source-machine-has-linux-os"></a>如果源计算机使用 Linux OS

在源计算机上运行以下命令

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**设置** | **详细信息**
--- | ---
使用情况 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | 必需的参数。 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
-P |  必需。 通行短语所保存到的文件的完整文件路径。 使用任何有效文件夹