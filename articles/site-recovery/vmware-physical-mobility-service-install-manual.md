---
title: 手动安装移动服务以使用 Azure Site Recovery 对 VMware VM 和物理服务器进行灾难恢复 | Microsoft Docs
description: 了解如何安装移动服务代理以使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: raynew
ms.openlocfilehash: dfed0209131379843b97ff8050c2f2ba7294537d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019748"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>在 VMware VM 和物理服务器上手动安装移动服务

在使用 [Azure Site Recovery](site-recovery-overview.md) 设置 VMware VM 和物理服务器的灾难恢复时，可在每个本地 VMware VM 和物理服务器上安装 [Site Recovery 移动服务](vmware-physical-mobility-service-overview.md)。  移动服务可以捕获计算机上的数据写入，并将其转发到 Site Recovery 进程服务器。

本文介绍如何在要保护的每台计算机上手动安装移动服务。

## <a name="create-a-passphrase"></a>创建通行短语

在安装之前，请创建安装期间将要使用的通行短语。

1. 登录到配置服务器。
2. 以管理员身份打开命令提示符。
3. 将目录更改到 bin 文件夹，然后创建一个密码文件。

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 将密码文件存储在安全位置中。 


## <a name="install-the-service-from-the-ui"></a>从 UI 安装服务

>[!IMPORTANT]
> 如果要在不同的 Azure 区域之间复制 Azure IaaS VM，请不要使用此方法。 请改用基于命令行的安装方法。

1. 找到计算机操作系统所需的安装程序版本。 安装程序位于 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 文件夹中。 [检查](vmware-physical-mobility-service-overview.md#installer-files)所需的安装程序。
2. 将安装文件复制到计算机，并运行它。
3. 在“安装选项”中，选择“安装移动服务”。
4. 选择安装位置 >“安装”。

    ![“移动服务安装选项”页](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. 在“安装进度”中监视安装。 安装完成后，选择“转到配置”，将该服务注册到配置服务器。

    ![“移动服务注册”页](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  在“配置服务器详细信息”中，指定已配置的 IP 地址和通行短语。  

    ![“移动服务注册”页](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. 选择“注册”完成注册。

    ![移动服务注册最后一页](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>从命令提示符安装服务

### <a name="on-a-windows-machine"></a>在 Windows 计算机上

1. 将安装程序复制到要保护的服务器上的某个本地文件夹（例如 C:\Temp）。 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 按如下所示进行安装：

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. 将代理注册到配置服务器。

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>安装设置
**设置** | **详细信息**
--- | ---
使用情况 | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
安装日志 | 位于 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log 下。
/Role | 必需的安装参数。 指定是要安装移动服务 (MS) 还是主目标 (MT)。
/InstallLocation| 可选参数。 指定移动服务的安装位置（任意文件夹）。
/Platform | 必需。 指定一个平台，以便在其上安装移动服务。 对于 Mware VM/物理服务器，请指定 **VMware**；对于 Azure VM，请指定 **Azure**。 
/Silent| 可选。 指定是否以静默模式运行安装程序。

#### <a name="registration-settings"></a>注册设置
**设置** | **详细信息**
--- | ---
使用情况 | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
代理配置日志 | 位于 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 下。
/CSEndPoint | 必需的参数。 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
/PassphraseFilePath |  必需。 通行短语的位置。 使用任何有效的 UNC 或本地文件路径。


### <a name="on-a-linux-machine"></a>在 Linux 计算机上

1. 将安装程序复制到要保护的服务器上的某个本地文件夹（例如 /tmp）。 在终端运行以下命令：
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. 按如下所示进行安装：

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. 安装完成后，必须将移动服务注册到配置服务器。 运行以下命令，将移动服务注册到配置服务器：

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```


#### <a name="installation-settings"></a>安装设置
**设置** | **详细信息**
--- | ---
使用情况 | ./install -d <Install Location> -r <MS|MT> -v VmWare -q
-r | 必需的安装参数。 指定是要安装移动服务 (MS) 还是主目标 (MT)。
-d | 可选参数。 指定移动服务的安装位置：/usr/local/ASR。
-v | 必需。 指定一个平台，以便在其上安装移动服务。 对于 Mware VM/物理服务器，请指定 **VMware**；对于 Azure VM，请指定 **Azure**。 
-q | 可选。 指定是否以静默模式运行安装程序。

#### <a name="registration-settings"></a>注册设置
**设置** | **详细信息**
--- | ---
使用情况 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | 必需的参数。 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
-P |  必需。 通行短语所保存到的文件的完整文件路径。 使用任何有效文件夹

## <a name="next-steps"></a>后续步骤
- [为 VMware VM 设置灾难恢复](vmware-azure-tutorial.md)
- [为物理服务器设置灾难恢复](physical-azure-disaster-recovery.md)
