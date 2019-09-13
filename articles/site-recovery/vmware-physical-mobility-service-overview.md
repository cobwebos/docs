---
title: 关于使用 Azure Site Recovery 对 VMware VM 和物理服务器进行灾难恢复的移动服务 | Microsoft Docs
description: 了解可使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 的移动服务代理。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: aeb00b84ac254232e0d68fd9631fb539a928e67d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931887"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>关于适用于 VMware VM 和物理服务器的移动服务

在使用 [Azure Site Recovery](site-recovery-overview.md) 设置 VMware VM 和物理服务器的灾难恢复时，可在每个本地 VMware VM 和物理服务器上安装 Site Recovery 移动服务。  移动服务可以捕获计算机上的数据写入，并将其转发到 Site Recovery 进程服务器。 可以使用下述方法部署移动服务：

- [推送安装](#push-installation)：通过 Azure 门户启用保护时，Site Recovery 会在服务器上安装移动代理。
- 手动安装：可以通过 [UI](#install-mobility-agent-through-ui) 或[命令提示符](#install-mobility-agent-through-command-prompt)在每台计算机上手动安装移动服务。
- [自动部署](vmware-azure-mobility-install-configuration-mgr.md)：可以使用 System Center Configuration Manager 等软件部署工具自动完成安装。

## <a name="anti-virus-on-replicated-machines"></a>复制计算机上的防病毒

如果要复制的计算机上正在运行活动的防病毒软件，请确保从防病毒操作中排除移动服务安装文件夹 (*C:\ProgramData\ASR\agent*)。 这可确保复制正常工作。

## <a name="push-installation"></a>推送安装

推送安装是在门户中触发的“[启用复制](vmware-azure-enable-replication.md#enable-replication)”作业不可或缺的一部分。 在选择要保护的虚拟机集并触发“启用复制”以后，配置服务器会将移动代理推送到多个服务器并安装该代理，然后你就可以完成将代理注册到配置服务器的操作。 若要成功完成该操作，请遵循以下要求：

- 确保符合所有推送安装[先决条件](vmware-azure-install-mobility-service.md)。
- 确保服务器的所有配置位于 [VMware 到 Azure DR 方案的支持矩阵](vmware-physical-azure-support-matrix.md)中。

推送安装工作流的详细信息在以下部分介绍。

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>从 [9.23 版](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)开始

在推送安装移动代理的过程中，执行以下步骤

1. 将代理推送到源计算机。 将代理复制到源计算机的操作可能因多个环境错误而失败。 若要排查推送安装故障，请访问[我们的指南](vmware-azure-troubleshoot-push-install.md)。
2. 将代理成功复制到服务器以后，将会在服务器上执行先决条件检查。 如果一项或多项[先决条件](vmware-physical-azure-support-matrix.md)没有得到满足，安装会失败。 如果满足所有先决条件，则会触发安装过程。
3. Azure Site Recovery VSS 提供程序是在安装移动代理过程中安装到服务器上的。 此提供程序用于生成应用程序一致性点。 如果 VSS 提供程序安装失败，则会跳过此步骤，继续安装代理。
4. 如果代理安装成功但 VSS 提供程序安装失败，则会将作业状态标记为“警告”。 这不影响崩溃一致性点的生成。

    a. 若要生成应用程序一致性点，请参阅[我们的指南](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)，了解如何手动完成 Site Recovery VSS 提供程序的安装。 </br>
    b.  如果不希望生成应用程序一致性点，请[修改复制策略](vmware-azure-set-up-replication.md#create-a-policy)，将应用程序一致性点关闭。

### <a name="before-922-versions"></a>9\.22 以前的版本

1. 将代理推送到源计算机。 将代理复制到源计算机的操作可能因多个环境错误而失败。 若要排查推送安装故障，请访问[我们的指南](vmware-azure-troubleshoot-push-install.md)。
2. 将代理成功复制到服务器以后，将会在服务器上执行先决条件检查。 如果一项或多项[先决条件](vmware-physical-azure-support-matrix.md)没有得到满足，安装会失败。 如果满足所有先决条件，则会触发安装过程。
3. Azure Site Recovery VSS 提供程序是在安装移动代理过程中安装到服务器上的。 此提供程序用于生成应用程序一致性点。 如果 VSS 提供程序安装失败，则代理安装会失败。 为了避免移动代理安装失败，请使用 [9.23 版](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)或更高版本来生成崩溃一致性点，然后手动安装 VSS 提供程序。

## <a name="install-mobility-agent-through-ui"></a>通过 UI 安装移动代理

### <a name="prerequisite"></a>先决条件

- 确保服务器的所有配置位于 [VMware 到 Azure DR 方案的支持矩阵](vmware-physical-azure-support-matrix.md)中。
- 根据服务器的操作系统[找到安装程序](#locate-installer-files)。

>[!IMPORTANT]
> 如果要在不同的 Azure 区域之间复制 Azure IaaS VM，请不要使用此方法。 请改用基于命令行的安装方法。

1. 将安装文件复制到计算机，并运行它。
2. 在“安装选项”中，选择“安装移动服务”。
3. 选择安装位置 >“安装”。

    ![“移动服务安装选项”页](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. 在“安装进度”中监视安装。 安装完成后，选择“转到配置”，将该服务注册到配置服务器。

    ![“移动服务注册”页](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. 在“配置服务器详细信息”中，指定已配置的 IP 地址和通行短语。  

    ![“移动服务注册”页](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. 选择“注册”完成注册。

    ![移动服务注册最后一页](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>通过命令提示符安装移动代理

### <a name="prerequisite"></a>先决条件

- 确保服务器的所有配置位于 [VMware 到 Azure DR 方案的支持矩阵](vmware-physical-azure-support-matrix.md)中。
- 根据服务器的操作系统[找到安装程序](#locate-installer-files)。

### <a name="on-a-windows-machine"></a>在 Windows 计算机上

- 将安装程序复制到要保护的服务器上的某个本地文件夹（例如 C:\Temp）。

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- 按如下所示进行安装：

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- 将代理注册到配置服务器。

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>安装设置
**设置** | **详细信息**
--- | ---
用法 | Unifiedagent.exe/Role \<MS/MT >/InstallLocation \<安装位置 >/Platform "VmWare"/Silent
安装程序日志 | 位于 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log 下。
/Role | 必需的安装参数。 指定是要安装移动服务 (MS) 还是主目标 (MT)。
/InstallLocation| 可选参数。 指定移动服务的安装位置（任意文件夹）。
/Platform | 必需。 指定一个平台，以便在其上安装移动服务。 对于 VMware VM/物理服务器，请指定 **VMware**；对于 Azure VM，请指定 **Azure**。<br/><br/> 如果要将 Azure Vm 视为物理计算机，请指定**VMware**。
/Silent| 可选。 指定是否以静默模式运行安装程序。

#### <a name="registration-settings"></a>注册设置
**设置** | **详细信息**
--- | ---
用法 | UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
代理配置日志 | 位于 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 下。
/CSEndPoint | 必需的参数。 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
/PassphraseFilePath |  必需。 通行短语的位置。 使用任何有效的 UNC 或本地文件路径。

### <a name="on-a-linux-machine"></a>在 Linux 计算机上

1. 将安装程序复制到要保护的服务器上的某个本地文件夹（例如 /tmp）。 在终端运行以下命令：

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
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
用法 | ./install -d \<Install Location> -r \<MS/MT> -v VmWare -q
-r | 必需的安装参数。 指定是要安装移动服务 (MS) 还是主目标 (MT)。
-d | 可选参数。 指定移动服务的安装位置：/usr/local/ASR。
-v | 必需。 指定一个平台，以便在其上安装移动服务。 对于 VMware VM/物理服务器，请指定 **VMware**；对于 Azure VM，请指定 **Azure**。
-q | 可选。 指定是否以静默模式运行安装程序。

#### <a name="registration-settings"></a>注册设置
**设置** | **详细信息**
--- | ---
用法 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | 必需的参数。 指定配置服务器的 IP 地址。 使用任何有效的 IP 地址。
-P |  必需。 通行短语所保存到的文件的完整文件路径。 使用任何有效文件夹。

## <a name="azure-virtual-machine-agent"></a>Azure 虚拟机代理

- **Windows VM**：从移动服务版本 9.7.0.0 开始，移动服务安装程序会安装 [Azure VM 代理](../virtual-machines/extensions/features-windows.md#azure-vm-agent)。 这可以确保当计算机故障转移到 Azure 时，Azure VM 满足使用任何 VM 扩展相关的代理安装先决条件。
- **Linux VM**：故障转移后，必须在 Azure VM 上手动安装 [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent)。

## <a name="locate-installer-files"></a>找到安装程序文件

转到配置服务器上的 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 文件夹。 根据操作系统检查需要哪个安装程序。 下表汇总了每个 VMware VM 和物理服务器操作系统的安装程序文件。 在开始之前，可以查看[支持的操作系统](vmware-physical-azure-support-matrix.md#replicated-machines)。

**安装程序文件** | **操作系统（仅限 64 位）**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016；Windows Server 2012 R2；Windows Server 2012；Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6。* </br> CentOS 6。*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7。* </br> CentOS 7。*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1、SP2、SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS 服务器
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>后续步骤

[设置移动服务的推送安装](vmware-azure-install-mobility-service.md)。
