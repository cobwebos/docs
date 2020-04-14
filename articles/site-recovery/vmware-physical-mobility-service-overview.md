---
title: 关于使用 Azure 站点恢复的 VMware VM 和物理服务器的灾难恢复移动服务 |微软文档
description: 使用 Azure 站点恢复服务了解用于将 VMware VM 和物理服务器灾难恢复的移动服务代理。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259778"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>关于适用于 VMware VM 和物理服务器的移动服务

使用[Azure 站点恢复](site-recovery-overview.md)为 VMware 虚拟机 （VM） 和物理服务器设置灾难恢复时，将站点恢复移动服务安装在每个本地 VMware VM 和物理服务器上。 移动服务可以捕获计算机上的数据写入，并将其转发到 Site Recovery 进程服务器。 移动服务由移动服务代理软件安装，您可以使用以下方法进行部署：

- [推送安装](#push-installation)：当通过 Azure 门户启用保护时，站点恢复将安装在服务器上的移动服务。
- 手动安装：您可以通过[用户界面 （UI）](#install-the-mobility-service-using-ui)或[命令提示符](#install-the-mobility-service-using-command-prompt)在每台计算机上手动安装移动服务。
- [自动部署](vmware-azure-mobility-install-configuration-mgr.md)：您可以使用配置管理器 等软件部署工具自动安装移动服务。

> [!NOTE]
> 移动服务在源计算机上使用大约 6%-10% 的内存用于 VMware VM 或物理计算机。

## <a name="antivirus-on-replicated-machines"></a>复制的计算机上防病毒

如果要复制的计算机正在运行防病毒软件，则从防病毒操作中排除移动服务的安装文件夹_C：_ProgramData_ASR\代理_。 此排除可确保复制按预期工作。

## <a name="push-installation"></a>推送安装

推送安装是从 Azure 门户运行以[启用复制](vmware-azure-enable-replication.md#enable-replication)作业的一个组成部分。 选择要保护和启用复制的 VM 集后，配置服务器会将移动服务代理推送到服务器，安装代理，并完成代理在配置服务器的注册。

### <a name="prerequisites"></a>先决条件

- 确保符合所有推送安装[先决条件](vmware-azure-install-mobility-service.md)。
- 确保所有服务器配置都符合[支持矩阵中的标准，以便 VMware VM 和物理服务器恢复到 Azure。](vmware-physical-azure-support-matrix.md)

推送安装工作流在以下部分中介绍：

### <a name="mobility-service-agent-version-923-and-higher"></a>移动服务代理版本 9.23 及更高版本

有关版本 9.23 的详细信息，请参阅[Azure 站点恢复更新汇总 35。](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)

在移动服务推送安装期间，执行以下步骤：

1. 代理被推送到源计算机。 由于多个环境错误，将代理复制到源计算机可能会失败。 若要排查推送安装故障，请访问[我们的指南](vmware-azure-troubleshoot-push-install.md)。
1. 成功将代理复制到服务器后，对服务器执行先决条件检查。
   - 如果满足所有先决条件，则开始安装。
   - 如果未满足一个或多个[先决条件](vmware-physical-azure-support-matrix.md)，安装将失败。
1. 作为代理安装的一部分，将安装用于 Azure 站点恢复的卷卷卷影复制服务 （VSS） 提供程序。 VSS 提供程序用于生成应用程序一致的恢复点。 如果 VSS 提供程序的安装失败，将跳过此步骤，并且代理安装将继续。
1. 如果代理安装成功，但 VSS 提供程序安装失败，则作业状态将标记为**警告**。 这不会影响崩溃一致的恢复点生成。

    - 要生成应用程序一致的恢复点，请参阅[我们的指南](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)，以完成站点恢复 VSS 提供程序的手动安装。
    - 如果不想生成应用程序一致的恢复点，[请修改复制策略](vmware-azure-set-up-replication.md#create-a-policy)以关闭应用程序一致的恢复点。

### <a name="mobility-service-agent-version-922-and-below"></a>移动服务代理版本 9.22 及以下版本

1. 代理被推送到源计算机。 由于多个环境错误，将代理复制到源计算机可能会失败。 请参阅[我们的指南](vmware-azure-troubleshoot-push-install.md)，以排除推送安装故障。
1. 成功将代理复制到服务器后，对服务器执行先决条件检查。
   - 如果满足所有先决条件，则开始安装。
   - 如果未满足一个或多个[先决条件](vmware-physical-azure-support-matrix.md)，安装将失败。

1. 作为代理安装的一部分，将安装用于 Azure 站点恢复的卷卷卷影复制服务 （VSS） 提供程序。 VSS 提供程序用于生成应用程序一致的恢复点。
   - 如果 VSS 提供程序安装失败，代理安装将失败。 为避免代理安装失败，请使用版本[9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)或更高版本生成崩溃一致的恢复点，并手动安装 VSS 提供程序。

## <a name="install-the-mobility-service-using-ui"></a>使用 UI 安装移动服务

### <a name="prerequisites"></a>先决条件

- 确保所有服务器配置都符合[支持矩阵中的标准，以便 VMware VM 和物理服务器恢复到 Azure。](vmware-physical-azure-support-matrix.md)
- [查找](#locate-installer-files)服务器操作系统的安装程序。

>[!IMPORTANT]
> 如果要将 Azure 基础结构作为服务 （IaaS） VM 从一个 Azure 区域复制到另一个 Azure 区域，则不要使用 UI 安装方法。 使用[命令提示](#install-the-mobility-service-using-command-prompt)安装。

1. 将安装文件复制到计算机，并运行它。
1. 在“安装选项”**** 中，选择“安装移动服务”****。
1. 选择安装位置并选择 **"安装**"。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="移动服务安装选项页。":::

1. 在“安装进度”**** 中监视安装。 安装完成后，选择“转到配置”****，将该服务注册到配置服务器。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="移动服务注册页面。":::

1. 在**配置服务器详细信息**中，指定您配置的 IP 地址和密码。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="移动服务注册页面。":::

1. 选择“注册”**** 完成注册。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="移动服务注册最终页面。":::

## <a name="install-the-mobility-service-using-command-prompt"></a>使用命令提示安装移动服务

### <a name="prerequisites"></a>先决条件

- 确保所有服务器配置都符合[支持矩阵中的标准，以便 VMware VM 和物理服务器恢复到 Azure。](vmware-physical-azure-support-matrix.md)
- [查找](#locate-installer-files)服务器操作系统的安装程序。

### <a name="windows-machine"></a>视窗机

- 从命令提示符中，运行以下命令以将安装程序复制到要保护的服务器上的本地文件夹，如_C：_Temp。_ 将安装程序的文件名替换为实际文件名。

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- 运行此命令以安装代理。

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- 运行这些命令以将代理注册到配置服务器。

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>安装设置

设置 | 详细信息
--- | ---
语法 | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
安装程序日志 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | 必需的安装参数。 指定是要安装移动服务 (MS) 还是主目标 (MT)。
`/InstallLocation`| 可选参数。 指定移动服务的安装位置（任意文件夹）。
`/Platform` | Mandatory。 指定在其中安装移动服务的平台： <br/> **VMware** VM/物理服务器。 <br/> **Azure** VM。<br/><br/> 如果要将 Azure VM 视为物理计算机，请指定 **VMware**。
`/Silent`| 可选。 指定是否以静默模式运行安装程序。

#### <a name="registration-settings"></a>注册设置

设置 | 详细信息
--- | ---
语法 | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
代理配置日志 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | 必需的参数。 `<CSIP>`指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
`/PassphraseFilePath` |  Mandatory。 通行短语的位置。 使用任何有效的 UNC 或本地文件路径。

### <a name="linux-machine"></a>Linux 机器

1. 从终端会话中，将安装程序复制到要保护的服务器上的 _/tmp_等本地文件夹。 将安装程序的文件名替换为 Linux 发行版的实际文件名，然后运行这些命令。

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. 按如下所示进行安装：

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. 安装完成后，必须将移动服务注册到配置服务器。 运行以下命令以将移动服务注册到配置服务器。

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>安装设置

设置 | 详细信息
--- | ---
语法 | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | 必需的安装参数。 指定是要安装移动服务 (MS) 还是主目标 (MT)。
`-d` | 可选参数。 指定移动服务安装位置： `/usr/local/ASR`。
`-v` | Mandatory。 指定在其中安装移动服务的平台。 <br/> **VMware** VM/物理服务器。 <br/> **Azure** VM。
`-q` | 可选。 指定是否以静默模式运行安装程序。

#### <a name="registration-settings"></a>注册设置

设置 | 详细信息
--- | ---
语法 | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | 必需的参数。 `<CSIP>`指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
`-P` |  Mandatory。 通行短语所保存到的文件的完整文件路径。 使用任何有效文件夹。

## <a name="azure-virtual-machine-agent"></a>Azure 虚拟机代理

- **Windows VM**：从移动服务版本 9.7.0.0 开始，移动服务安装程序会安装 [Azure VM 代理](/azure/virtual-machines/extensions/features-windows#azure-vm-agent)。 这可确保当计算机故障转移到 Azure 时，Azure VM 满足使用任何 VM 扩展的代理安装先决条件。
- **Linux VM**：故障转移后，必须在 Azure VM 上手动安装 [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent)。

## <a name="locate-installer-files"></a>找到安装程序文件

在配置服务器上转到文件夹 _%程序数据%\ASR_home_svsystems_pushinstallsvc_存储库_。 根据操作系统检查您需要的安装程序。 下表汇总了每个 VMware VM 和物理服务器操作系统的安装程序文件。 在开始之前，您可以查看[支持的操作系统](vmware-physical-azure-support-matrix.md#replicated-machines)。

> [!NOTE]
> 文件名使用下表中显示的语法，_其中版本_和_日期_作为实际值的占位符。 实际文件名将类似于以下示例：
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

安装程序文件 | 操作系统（仅限 64 位）
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | 红帽企业 Linux （RHEL） 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> 包括 SP2 和 SP3。
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | 甲骨文企业Linux 6.4 </br> 甲骨文企业Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS 服务器
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>后续步骤

[设置移动服务的推送安装](vmware-azure-install-mobility-service.md)。
