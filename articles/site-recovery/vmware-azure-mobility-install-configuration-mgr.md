---
title: 自动执行移动服务，以便在 Azure Site Recovery 中进行灾难恢复安装
description: 如何通过 Azure Site Recovery 自动安装 VMware/physical server 灾难恢复的移动服务。
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 12/22/2019
ms.author: ramamill
ms.openlocfilehash: 43e6a39a52eb81573b4a4ba8ad63d48d0e51dedd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514795"
---
# <a name="automate-mobility-service-installation"></a>自动安装移动服务

本文介绍如何在[Azure Site Recovery](site-recovery-overview.md)中自动执行移动服务代理的安装和更新。

将本地 VMware Vm 和物理服务器的灾难恢复 Site Recovery 部署到 Azure 时，需要在要复制的每台计算机上安装移动服务代理。 移动服务捕获计算机上的数据写入，并将其转发到 Site Recovery 进程服务器以进行复制。 可以通过以下几种方式部署移动服务：

- **推送安装**：在为 Azure 门户中的计算机启用复制时，让 Site Recovery 安装移动服务代理。
- **手动安装**：在每台计算机上手动安装移动服务。 [了解](/vmware-physical-mobility-service-overview.md)有关推送和手动安装的详细信息。
- **自动部署**：通过软件部署工具（例如 Microsoft Endpoint Configuration Manager）或第三方工具（如 Intigua JetPatch）自动执行安装。

自动安装和更新提供了以下解决方案：

- 你的组织不允许在受保护的服务器上进行推送安装。
- 你的公司策略要求定期更改密码。 必须为推送安装指定密码。
- 你的安全策略不允许为特定计算机添加防火墙例外。
- 你作为托管服务提供商，并且不希望提供使用 Site Recovery 进行推送安装所需的客户计算机凭据。
- 需要将 g 安装同时扩展到大量服务器。
- 你希望在计划的维护时段内计划安装和升级。



## <a name="prerequisites"></a>必备组件

对于自动安装，你需要以下各项：

- 部署的软件安装解决方案，如[Configuration Manager](https://docs.microsoft.com/configmgr/)或[JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/)。 
-  部署在[Azure](tutorial-prepare-azure.md)和[本地](vmware-azure-tutorial-prepare-on-premises.md)部署的先决条件，适用于 VMware 灾难恢复，或用于[物理服务器](physical-azure-disaster-recovery.md)灾难恢复。 还应查看灾难恢复的[支持要求](vmware-physical-azure-support-matrix.md)。

## <a name="prepare-for-automated-deployment"></a>准备自动部署

下表总结了用于自动执行移动服务部署的工具和过程。

**工具** | **详细信息** | **说明**
--- | --- | ---
**配置管理器** | 1. 验证是否已准备好上面列出的[先决条件](#prerequisites)。 <br/><br/>2. 通过设置源环境部署灾难恢复，包括下载 .OVA 文件，以使用 OVF 模板将 Site Recovery 配置服务器部署为 VMware VM。<br/><br/> 2. 将配置服务器注册到 Site Recovery 服务，设置目标 Azure 环境，并配置复制策略。<br/><br/> 3. 对于自动移动服务部署，你可以创建包含配置服务器密码和移动服务安装文件的网络共享。<br/><br/> 4. 创建包含安装或更新的 Configuration Manager 包，并为移动服务部署做准备。<br/><br/> 5. 然后，你可以为安装了移动服务的计算机启用到 Azure 的复制。 | [自动执行 Configuration Manager](#automate-with-configuration-manager)。
**JetPatch** | 1. 验证是否已准备好上面列出的[先决条件](#prerequisites)。 <br/><br/> 2. 通过设置源环境部署灾难恢复，包括使用 OVF 模板在 Site Recovery 环境中下载和部署 JetPatch Azure Site Recovery 代理程序管理器。<br/><br/> 2. 将配置服务器注册到 Site Recovery，设置目标 Azure 环境，并配置复制策略。<br/><br/> 3. 对于自动部署，初始化并完成 JetPatch 代理程序管理器配置。<br/><br/> 4. 在 JetPatch 中，可以创建 Site Recovery 策略来自动部署和升级移动服务代理。 <br/><br/> 5. 然后，你可以为安装了移动服务的计算机启用到 Azure 的复制。 | [利用 JetPatch 代理程序管理器自动执行](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)。<br/><br/> 排查 JetPatch 中的[代理安装问题](https://kc.jetpatch.com/hc/articles/360035981812)。

## <a name="automate-with-configuration-manager"></a>自动执行 Configuration Manager

### <a name="prepare-the-installation-files"></a>准备安装文件

1. 请确保满足先决条件。
2. 创建可由运行配置服务器的计算机访问的安全网络文件共享（SMB 共享）。
3. 在 Configuration Manager 中，对要安装或更新移动服务的[服务器进行分类](https://docs.microsoft.com/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections)。 一个集合应包含所有 Windows 服务器，另一个是 Linux 服务器。 
5. 在网络共享上创建一个文件夹：

    - 若要在 Windows 计算机上安装，请创建**MobSvcWindows**文件夹。
    - 若要在 Linux 计算机上安装，请创建**MobSvcLinux**文件夹。

6. 登录到配置服务器计算机。
7. 在计算机上，打开管理员命令提示符。
8. 运行以下命令以生成密码文件：

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
9. 将 Mobsvc.passphrase 文件复制到 Windows 文件夹和 Linux 文件夹中。
10. 运行以下命令以浏览到包含安装文件的文件夹：

    ```
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

11. 将以下安装文件复制到网络共享：

    - 若要**MobSvcWindows**，请复制**ASR_UA_version_Windows_GA_date_Release**
    - 若要**MobSvcLinux**，请复制：
        - Microsoft ASR_UA*RHEL6-64*gz
        - Microsoft ASR_UA*RHEL7-64*gz
        - Microsoft ASR_UA*SLES11-64*gz
        - Microsoft ASR_UA*SLES11-64*gz
        - Microsoft-ASR_UA*OL6-64*release.tar.gz
        - Microsoft ASR_UA*14.04-64*gz
      
12. 将代码复制到 Windows 或 Linux 文件夹，如以下过程中所述。 我们假设：
    - 配置服务器的 IP 地址为192.168.3.121。
    - 安全网络文件共享是 **\ContosoSecureFS\MobilityServiceInstallers\\** 。

### <a name="copy-code-to-the-windows-folder"></a>将代码复制到 Windows 文件夹

复制以下代码：

- 将其保存为**MobSvcWindows**文件夹中的 **.bat** 。
- 将此脚本中的 [CSIP] 占位符替换为配置服务器的实际 IP 地址值。
- 该脚本支持移动服务代理的全新安装和已安装的代理的更新。

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```
### <a name="copy-code-to-the-linux-folder"></a>将代码复制到 Linux 文件夹

复制以下代码：

- 将其保存为**MobSvcLinux**文件夹中的**install_linux。**
- 将此脚本中的 [CSIP] 占位符替换为配置服务器的实际 IP 地址值。
- 该脚本支持移动服务代理的全新安装和已安装的代理的更新。

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```


### <a name="create-a-package"></a>创建包

1. **软件库** > **应用程序管理** > **包**登录到 Configuration Manager 控制台。
2. 右键单击 "**包**" > "**创建包**"。
3. 提供包详细信息，包括名称、描述、制造商、语言和版本。
4. 选择 "**此包包含源文件**"。
5. 单击 "**浏览**"，然后选择包含相关安装程序的网络共享和文件夹（MobSvcWindows 或 MobSvcLinux），然后单击 "**下一步**"。

   ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. 在 "**选择要创建的程序类型**" 页上，选择 "**标准程序** > "**下一步**"。

   ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. 在 "**指定有关此标准程序的信息**" 页上，指定以下值：

    **Parameter** | **Windows 值** | **Linux 值**
    --- | --- | ---
    **名称** | 安装 Microsoft Azure 移动服务 (Windows) | 安装 Microsoft Azure 移动服务（Linux）。
    **命令行** | install.bat | ./install_linux.sh
    **程序可以运行** | 用户是否已登录 | 用户是否已登录
    **其他参数** | 使用默认设置 | 使用默认设置

   ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. 在 "**指定此标准程序的要求**" 中，执行以下操作：

    - 对于 Windows 计算机，选择 "**此程序只能在指定的平台上运行"** 。 然后选择[受支持的 Windows 操作系统](vmware-physical-azure-support-matrix.md#replicated-machines)。 然后单击“下一步”。
    - 对于 Linux 计算机，选择 "**此程序可以在任何平台上运行"** 。 然后单击“下一步”。
   
10. 完成该向导。



### <a name="deploy-the-package"></a>部署包

1. 在 Configuration Manager 控制台中，右键单击包 > "**分发内容**"。
   ![Configuration Manager 控制台的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. 选择包应复制到的分发点。 [了解详细信息](https://docs.microsoft.com/sccm/core/servers/deploy/configure/install-and-configure-distribution-points)。
3. 完成向导。 包随后开始复制到指定的分发点。
4. 包分发完成后，右键单击 >**部署**的包。
   ![Configuration Manager 控制台的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. 选择之前创建的 Windows 或 Linux 设备集合。
6. 在 "**指定内容目标**" 页上，选择 "**分发点**"。
7. 在 "**指定设置以控制此软件的部署方式**" 页上，将 "**目的**" 设置为 "**必需**"。

   ![部署软件向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. 在 "**为此部署指定计划**" 中，设置计划。 [了解详细信息](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched)。

    - 移动服务按照您指定的计划安装。 
    - 为了避免不必要的重新启动，请在每月的维护时段或软件更新时段计划包安装。
9. 在 "**分发点**" 页上，配置设置并完成向导。
10. 在 Configuration Manager 控制台中监视部署进度。 转到“监视” > “部署” > “[包名称]”。





### <a name="uninstall-the-mobility-service"></a>卸载移动服务
可以创建用于卸载移动服务的 Configuration Manager 包。 使用以下脚本执行相关操作：

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>后续步骤
现在为 Vm[启用保护](vmware-azure-enable-replication.md)。
