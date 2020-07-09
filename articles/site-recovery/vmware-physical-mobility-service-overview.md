---
title: 关于 Azure Site Recovery 的 VMware Vm 和物理服务器的灾难恢复的移动服务Microsoft Docs
description: 了解可使用 Azure Site Recovery 服务将 VMware VM 和物理服务器灾难恢复到 Azure 的移动服务代理。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: d73e2776d0d9c86fe0331f9804bfeade3f1de676
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131794"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>关于适用于 VMware VM 和物理服务器的移动服务

在使用 [Azure Site Recovery](site-recovery-overview.md) 设置 VMware 虚拟机 (VM) 和物理服务器的灾难恢复时，可在每个本地 VMware VM 和物理服务器上安装 Site Recovery 移动服务。 移动服务可以捕获计算机上的数据写入，并将其转发到 Site Recovery 进程服务器。 移动服务由移动服务代理软件安装，可以使用以下方法进行部署：

- [推送安装](#push-installation)：通过 Azure 门户启用保护时，Site Recovery 会在服务器上安装移动服务。
- 手动安装：可以通过[用户界面 (UI)](#install-the-mobility-service-using-ui) 或[命令提示符](#install-the-mobility-service-using-command-prompt)在每台计算机上手动安装移动服务。
- [自动部署](vmware-azure-mobility-install-configuration-mgr.md)：可以使用 Configuration Manager 等软件部署工具自动完成移动服务安装。

> [!NOTE]
> 移动服务使用 VMware VM 的源计算机上或使用物理计算机上大约 6%-10% 的内存。

## <a name="antivirus-on-replicated-machines"></a>复制计算机上的防病毒

如果要复制的计算机上正在运行防病毒软件，请从防病毒操作中排除移动服务的安装文件夹 C:\ProgramData\ASR\agent。 排除后可确保复制将按预期方式工作。

## <a name="push-installation"></a>推送安装

推送安装是从 Azure 门户运行以[启用复制](vmware-azure-enable-replication.md#enable-replication)的作业中不可或缺的部分。 在选择要保护并启用复制的 VM 集以后，配置服务器会将移动服务代理推送到多个服务器并安装该代理，然后完成将代理注册到配置服务器的操作。

### <a name="prerequisites"></a>先决条件

- 确保符合所有推送安装[先决条件](vmware-azure-install-mobility-service.md)。
- 确保所有服务器配置均符合[将 VMware VM 和物理服务器灾难恢复到 Azure 的支持矩阵](vmware-physical-azure-support-matrix.md)中的条件。

以下部分介绍了推送安装工作流：

### <a name="mobility-service-agent-version-923-and-higher"></a>移动服务代理版本 9.23 及更高版本

有关版本 9.23 的详细信息，请参阅 [Azure Site Recovery 的更新汇总 35](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)。

在推送安装移动服务的过程中，执行以下步骤：

1. 将代理推送到源计算机。 将代理复制到源计算机的操作可能因多个环境错误而失败。 若要排查推送安装故障，请访问[我们的指南](vmware-azure-troubleshoot-push-install.md)。
1. 将代理成功复制到服务器后，服务器上将执行先决条件检查。
   - 如果满足所有先决条件，则会开始安装。
   - 如果一项或多项[先决条件](vmware-physical-azure-support-matrix.md)没有得到满足，安装会失败。
1. 在代理安装的过程中，将安装适用于 Azure Site Recovery 的卷影复制服务 (VSS) 提供程序。 VSS 提供程序用于生成应用程序一致性恢复点。 如果 VSS 提供程序安装失败，则会跳过此步骤，继续安装代理。
1. 如果代理安装成功但 VSS 提供程序安装失败，则会将作业状态标记为“警告”。 这不会影响崩溃一致性恢复点生成。

    - 若要生成应用程序一致性恢复点，请参阅[我们的指南](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)，了解如何手动安装 Site Recovery VSS 提供程序。
    - 如果不希望生成应用程序一致性恢复点，请[修改复制策略](vmware-azure-set-up-replication.md#create-a-policy)，将应用程序一致性恢复点关闭。

### <a name="mobility-service-agent-version-922-and-below"></a>移动服务代理版本 9.22 及更低版本

1. 将代理推送到源计算机。 将代理复制到源计算机的操作可能因多个环境错误而失败。 若要对推送安装失败进行故障排除，请参阅[我们的指南](vmware-azure-troubleshoot-push-install.md)。
1. 将代理成功复制到服务器后，服务器上将执行先决条件检查。
   - 如果满足所有先决条件，则会开始安装。
   - 如果一项或多项[先决条件](vmware-physical-azure-support-matrix.md)没有得到满足，安装会失败。

1. 在代理安装的过程中，将安装适用于 Azure Site Recovery 的卷影复制服务 (VSS) 提供程序。 VSS 提供程序用于生成应用程序一致性恢复点。
   - 如果 VSS 提供程序安装失败，则代理安装会失败。 为了避免代理安装失败，请使用[版本 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) 或更高版本来生成崩溃一致性恢复点，然后手动安装 VSS 提供程序。

## <a name="install-the-mobility-service-using-ui"></a>使用 UI 安装移动服务

### <a name="prerequisites"></a>先决条件

- 确保所有服务器配置均符合[将 VMware VM 和物理服务器灾难恢复到 Azure 的支持矩阵](vmware-physical-azure-support-matrix.md)中的条件。
- 针对服务器的操作系统[找到安装程序](#locate-installer-files)。

>[!IMPORTANT]
> 如果要将 Azure 基础结构即服务 (IaaS) VM 从一个 Azure 区域复制到另一个区域，请不要使用 UI 安装方法。 请使用[命令提示符](#install-the-mobility-service-using-command-prompt)安装。

1. 将安装文件复制到计算机，并运行它。
1. 在“安装选项”中，选择“安装移动服务”。
1. 选择安装位置，然后选择“安装”。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="“移动服务安装选项”页。":::

1. 在“安装进度”中监视安装。 安装完成后，选择“转到配置”，将该服务注册到配置服务器。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="“移动服务注册”页。":::

1. 在“配置服务器详细信息”中，指定已配置的 IP 地址和通行短语。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="“移动服务注册”页。":::

1. 选择“注册”完成注册。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="移动服务注册最后一页。":::

## <a name="install-the-mobility-service-using-command-prompt"></a>使用命令提示符安装移动服务

### <a name="prerequisites"></a>先决条件

- 确保所有服务器配置均符合[将 VMware VM 和物理服务器灾难恢复到 Azure 的支持矩阵](vmware-physical-azure-support-matrix.md)中的条件。
- 针对服务器的操作系统[找到安装程序](#locate-installer-files)。

### <a name="windows-machine"></a>Windows 计算机

- 在命令提示符下运行以下命令，将安装程序复制到要保护的服务器上的某个本地文件夹，如 C:\Temp。 将安装程序的文件名替换为实际文件名。

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- 运行此命令来安装代理。

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- 运行这些命令，将代理注册到配置服务器。

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
`/Platform` | 必需。 指定要在其中安装移动服务的平台： <br/> VMware VM/物理服务器的 VMware。 <br/> Azure VM 的 Azure。<br/><br/> 如果要将 Azure VM 视为物理计算机，请指定 **VMware**。
`/Silent`| 可选。 指定是否以静默模式运行安装程序。

#### <a name="registration-settings"></a>注册设置

设置 | 详细信息
--- | ---
语法 | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
代理配置日志 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | 必需的参数。 `<CSIP>` 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
`/PassphraseFilePath` |  必需。 通行短语的位置。 使用任何有效的 UNC 或本地文件路径。

### <a name="linux-machine"></a>Linux 计算机

1. 在终端会话中，将安装程序复制到要保护的服务器上的某个本地文件夹，如 /tmp。 将安装程序的文件名替换为 Linux 分发的实际文件名，然后运行命令。

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. 按如下所示进行安装：

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. 安装完成后，必须将移动服务注册到配置服务器。 运行以下命令，将移动服务注册到配置服务器。

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>安装设置

设置 | 详细信息
--- | ---
语法 | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | 必需的安装参数。 指定是要安装移动服务 (MS) 还是主目标 (MT)。
`-d` | 可选参数。 指定移动服务的安装位置：`/usr/local/ASR`。
`-v` | 必需。 指定要在其中安装移动服务的平台。 <br/> VMware VM/物理服务器的 VMware。 <br/> Azure VM 的 Azure。
`-q` | 可选。 指定是否以静默模式运行安装程序。

#### <a name="registration-settings"></a>注册设置

设置 | 详细信息
--- | ---
语法 | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | 必需的参数。 `<CSIP>` 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
`-P` |  必需。 通行短语所保存到的文件的完整文件路径。 使用任何有效文件夹。

## <a name="azure-virtual-machine-agent"></a>Azure 虚拟机代理

- **Windows VM**：从移动服务版本 9.7.0.0 开始，移动服务安装程序会安装 [Azure VM 代理](../virtual-machines/extensions/features-windows.md#azure-vm-agent)。 这可以确保当计算机故障转移到 Azure 时，Azure VM 满足使用任何 VM 扩展相关的代理安装先决条件。
- **Linux VM**：故障转移后，必须在 Azure VM 上手动安装 [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md)。

## <a name="locate-installer-files"></a>找到安装程序文件

转到配置服务器上的 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 文件夹。 根据操作系统检查需要哪个安装程序。 下表汇总了每个 VMware VM 和物理服务器操作系统的安装程序文件。 在开始之前，可以查看[支持的操作系统](vmware-physical-azure-support-matrix.md#replicated-machines)。

> [!NOTE]
> 文件名使用下表中所示的语法，其中 version 和 date 是实际值的占位符 。 实际的文件名将类似于以下示例：
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

安装程序文件 | 操作系统（仅限 64 位）
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> 包括 SP2 和 SP3。
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6。4 </br> Oracle Enterprise Linux 6。5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS 服务器
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>后续步骤

[设置移动服务的推送安装](vmware-azure-install-mobility-service.md)。
