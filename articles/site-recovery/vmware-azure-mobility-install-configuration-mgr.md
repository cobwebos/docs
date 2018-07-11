---
title: 使用 System Center Configuration Manager 自动执行 Azure Site Recovery 的移动服务安装 | Microsoft Docs
description: 本文可帮助用户使用 System Center Configuration Manager 自动执行移动服务安装。
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: anoopkv
ms.openlocfilehash: c01fa286b0f9b9768ddc719b41c77e331aa82f8f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286556"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>使用 System Center Configuration Manager 自动执行移动服务安装

使用 [Azure Site Recovery](site-recovery-overview.md) 将移动服务安装在要复制到 Azure 的 VMware VM 和物理服务器上

本文提供如何使用 System Center Configuration Manager 在 VMware VM 上部署 Azure Site Recovery 移动服务的示例。 使用 Configuration Manager 等软件部署工具具有以下优势：

* 计划全新安装和升级，在计划内维护时段内进行软件更新
* 将部署规模扩大为同时部署数百台服务器

本文使用 System Center Configuration Manager 2012 R2 来演示部署活动。 我们假定你使用版本 **9.9.4510.1** 或更高版本的移动服务。

或者，可以使用 [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md) 自动执行移动服务安装。

## <a name="prerequisites"></a>先决条件

1. 已在环境中部署的软件部署工具，例如 Configuration Manager。
2. 应创建两个[设备集合](https://technet.microsoft.com/library/gg682169.aspx)，一个用于要使用 Site Recovery 保护的所有 **Windows 服务器**，另一个用于要保护的所有 **Linux 服务器**。
3. 已在恢复服务保管库中注册的配置服务器。
4. 可通过 Configuration Manager 计算机访问的安全网络文件共享（SMB 共享）。

## <a name="deploy-on-windows-machines"></a>在 Windows 计算机上部署
> [!NOTE]
> 本文假设配置服务器的 IP 地址为 192.168.3.121，且安全网络文件共享为 \\\ContosoSecureFS\MobilityServiceInstallers。

### <a name="prepare-for-deployment"></a>准备部署
1. 在网络共享上创建一个文件夹，并将其命名为 **MobSvcWindows**。
2. 登录配置服务器，并打开管理命令提示符。
3. 运行以下命令，生成密码文件：

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. 将 **MobSvc.passphrase** 文件复制到网络共享上的 **MobSvcWindows** 文件夹。
5. 运行以下命令，浏览到配置服务器上的安装程序存储库：

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. 将 **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** 复制到网络共享上的 **MobSvcWindows** 文件夹。
7. 复制以下代码，并将它作为 **install.bat** 保存到 **MobSvcWindows** 文件夹。

   > [!NOTE]
   > 将此脚本中的 [CSIP] 占位符替换为配置服务器的实际 IP 地址值。

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

### <a name="create-a-package"></a>创建包

1. 登录 Configuration Manager 控制台。
2. 浏览到“软件库” > “应用程序管理” > “包”。
3. 右键单击“包”，并选择“创建包”。
4. 提供“名称”、“说明”、“制造商”、“语言”和“版本”的值。
5. 选中“此包包含源文件”复选框。
6. 单击“浏览”，并选择存储安装程序的网络共享 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows)。

  ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. 在“选择要创建的程序类型”页上，选择“标准程序”，并单击“下一步”。

  ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. 在“指定此标准程序的相关信息”页上，提供以下输入，并单击“下一步”。 （其他输入可以使用其默认值。）

  | **参数名称** | **值** |
  |--|--|
  | 名称 | 安装 Microsoft Azure 移动服务 (Windows) |
  | 命令行 | install.bat |
  | 程序可以运行 | 用户是否已登录 |

  ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. 在下一页中，选择目标操作系统。 移动服务只能安装在 Windows Server 2012 R2、Windows Server 2012 和 Windows Server 2008 R2 上。

  ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. 要完成向导，请单击“下一步”两次。


> [!NOTE]
> 脚本支持移动服务代理的全新安装和已安装代理的更新。

### <a name="deploy-the-package"></a>部署包
1. 在 Configuration Manager 控制台中，右键单击包，并选择“分发内容”。
  ![Configuration Manager 控制台的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. 选择包应复制到的**[分发点](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**。
3. 完成该向导。 包随后开始复制到指定的分发点。
4. 完成包分发后，右键单击包，并选择“部署”。
  ![Configuration Manager 控制台的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. 选择在先决条件部分中创建的 Windows Server 设备集合作为部署的目标集合。

  ![部署软件向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. 在“指定内容目标”页上，选择**分发点**。
7. 在“指定设置以控制此软件的部署方式”页上，确保目的为“必需”。

  ![部署软件向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. 在“为此部署指定计划”页中，指定计划。 有关详细信息，请参阅[计划包](https://technet.microsoft.com/library/gg682178.aspx)。
9. 在“分发点”页上，根据数据中心的需求配置属性。 然后，完成向导。

> [!TIP]
> 为了避免不必要的重新启动，请在每月的维护时段或软件更新时段计划包安装。

可以使用 Configuration Manager 控制台监视部署进度。 转到“监视” > “部署” > “[包名称]”。

  ![用于监视部署的 Configuration Manager 选项的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>在 Linux 计算机上部署
> [!NOTE]
> 本文假设配置服务器的 IP 地址为 192.168.3.121，且安全网络文件共享为 \\\ContosoSecureFS\MobilityServiceInstallers。

### <a name="prepare-for-deployment"></a>准备部署
1. 在网络共享上创建一个文件夹，并将其命名为 **MobSvcLinux**。
2. 登录配置服务器，并打开管理命令提示符。
3. 运行以下命令，生成密码文件：

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. 将 **MobSvc.passphrase** 文件复制到网络共享上的 **MobSvcLinux** 文件夹。
5. 运行以下命令，浏览到配置服务器上的安装程序存储库：

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. 将以下文件复制到网络共享上的 **MobSvcLinux** 文件夹：
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. 复制以下代码，并将它作为 **install_linux.sh** 保存到 **MobSvcLinux** 文件夹。
   > [!NOTE]
   > 将此脚本中的 [CSIP] 占位符替换为配置服务器的实际 IP 地址值。

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

1. 登录 Configuration Manager 控制台。
2. 浏览到“软件库” > “应用程序管理” > “包”。
3. 右键单击“包”，并选择“创建包”。
4. 提供“名称”、“说明”、“制造商”、“语言”和“版本”的值。
5. 选中“此包包含源文件”复选框。
6. 单击“浏览”，并选择存储安装程序的网络共享 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux)。

  ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. 在“选择要创建的程序类型”页上，选择“标准程序”，并单击“下一步”。

  ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. 在“指定此标准程序的相关信息”页上，提供以下输入，并单击“下一步”。 （其他输入可以使用其默认值。）

    | **参数名称** | **值** |
  |--|--|
  | 名称 | 安装 Microsoft Azure 移动服务 (Linux) |
  | 命令行 | ./install_linux.sh |
  | 程序可以运行 | 用户是否已登录 |

  ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. 在下一页中，选择“此程序可以在任何平台上运行”。
  ![创建包和程序向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. 要完成向导，请单击“下一步”两次。

> [!NOTE]
> 脚本支持移动服务代理的全新安装和已安装代理的更新。

### <a name="deploy-the-package"></a>部署包
1. 在 Configuration Manager 控制台中，右键单击包，并选择“分发内容”。
  ![Configuration Manager 控制台的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. 选择包应复制到的**[分发点](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**。
3. 完成该向导。 包随后开始复制到指定的分发点。
4. 完成包分发后，右键单击包，并选择“部署”。
  ![Configuration Manager 控制台的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. 选择在先决条件部分中创建的 Linux 服务器设备集合作为部署的目标集合。

  ![部署软件向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. 在“指定内容目标”页上，选择**分发点**。
7. 在“指定设置以控制此软件的部署方式”页上，确保目的为“必需”。

  ![部署软件向导的屏幕截图](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. 在“为此部署指定计划”页中，指定计划。 有关详细信息，请参阅[计划包](https://technet.microsoft.com/library/gg682178.aspx)。
9. 在“分发点”页上，根据数据中心的需求配置属性。 然后，完成向导。

移动服务会根据配置的计划安装在 Linux 服务器设备集合上。


## <a name="uninstall-the-mobility-service"></a>卸载移动服务
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
现在可以为虚拟机[启用保护](vmware-azure-enable-replication.md)。
