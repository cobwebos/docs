---
title: "使用软件部署工具自动执行 Azure Site Recovery 的移动服务安装 | Microsoft 文档。"
description: "本文可帮助你使用软件部署工具（如 System Center Configuration Manager）自动执行移动服务安装"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 310f2a2fe793601d22952bf516a812bf4867bbec
ms.lasthandoff: 03/03/2017

---
# <a name="automate-mobility-service-installation-using-software-deployment-tools"></a>使用软件部署工具自动执行移动服务安装

本文提供了如何使用 System Center Configuration Manager (SCCM) 在数据中心内部署 Azure Site Recovery 移动服务的示例。 使用 SCCM 等软件部署工具可提供以下优势
* 计划部署 - 全新安装和升级，在计划内维护时段内进行软件更新。
* 同时大规模部署数百个服务器


> [!NOTE]
> 本文使用 System Center Configuration Manager 2012 R2 来演示部署活动。 用户还可以使用 [Azure 自动化和 Desired State Configuration](site-recovery-automate-mobility-service-install.md) 自动执行移动服务安装。

## <a name="prerequisites"></a>先决条件
1. 已在环境中部署的软件部署工具，如 System Center Configuration Manager(SCCM)。
  * 创建两个[设备集合](https://technet.microsoft.com/library/gg682169.aspx)，一个用于要使用 Azure Site Recovery 保护的所有 **Windows 服务器**，另一个用于要保护的所有 **Linux 服务器**。
3. 已向 Azure Site Recovery 注册的配置服务器。
4. 可通过 SCCM 服务器访问的安全网络文件共享（SMB 共享）。

## <a name="deploy-mobility-service-on-computers-running-microsoft-windows-operating-systems"></a>在运行 Microsoft Windows 操作系统的计算机上部署移动服务
> [!NOTE]
> 本文假定以下设置
> 1. 配置服务器的 IP 地址为 192.168.3.121
> 2. 安全网络文件共享为 \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>步骤 1：准备部署
1. 在网络共享上创建一个文件夹并将其命名为 **MobSvcWindows**
2. 登录到配置服务器，并打开管理命令提示符
3. 运行以下命令以生成密码文件。

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. 将 MobSvc.passphrase 文件复制到网络共享上的 MobSvcWindows 文件夹。
5. 下一步，通过运行以下命令浏览到配置服务器上的安装程序存储库。

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. 将 **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** 复制到网络共享上的 **MobSvcWindows** 文件夹。
7. 复制下面列出的代码，并将它作为 **install.bat** 保存到 **MobSvcWindows** 文件夹
> [!NOTE]
> 请记得将下面脚本中的 [CSIP] 占位符替换为配置服务器的实际 IP 地址值。

```
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
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
REM ==== Extract the installer ======================
CD %Temp%\MobSvc\Extracted
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed run install command ========
REM ==== Else run upgrade command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\temp\logfile.log
REM SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1} >> C:\Temp\logfile.log 2>&1
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
REM IF NOT %ERRORLEVEL% EQU 0 (GOTO :INSTALL) ELSE GOTO :UPDATE
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UPDATE
:INSTALL
    echo "Install" >> c:\Temp\logfile.log
     UnifiedAgent.exe /Role "Agent" /CSEndpoint "10.10.20.168" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
GOTO :ENDSCRIPT
:UPDATE
    echo "Update" >> C:\Temp\logfile.log
    UnifiedAgent.exe /upgrade
:ENDSCRIPT

```

### <a name="step-2-create-a-package"></a>步骤 2：创建包

1. 登录到 System Center Configuration Manager 控制台
2. 浏览到“软件库” > “应用程序管理” > “包”
3. 右键单击“包”，然后选择“创建包”
4. 提供“名称”、“说明”、“制造商”、“语言”、“版本”的值。
5. 勾选“此包包含源文件”复选框。
6. 单击“浏览”按钮，然后选择存储安装程序的网络共享 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. 在“选择要创建的程序类型”页上，选择“标准程序”，然后单击“下一步”

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. 在“指定此标准程序的相关信息”页上，提供以下输入，然后单击“下一步”。 （其他输入可以保留其默认值）

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)   
| **参数名称** | **值** |
|--|--|
| Name | 安装 Microsoft Azure 移动服务 (Windows) |
| 命令行 | install.bat |
| 程序可以运行 | 用户是否已登录 |
9. 在下一页中，选择目标操作系统。 移动服务只能安装在 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 上。

  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)   
10. 单击“下一步”两次以完成向导。

> [!NOTE]
> 此脚本支持全新安装移动服务代理和升级/更新已安装的代理。

### <a name="step-3-deploy-the-package"></a>步骤 3：部署包
1. 在 SCCM 控制台中，右键单击包，然后选择“分发内容”****
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. 选择包应复制到的**[分发点](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**。
3. 完成向导后，包将开始复制到指定的分发点
4. 完成包分发后，右键单击包，然后选择“部署”****
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. 选择在先决条件部分中创建的 Windows Server 设备集合作为部署的目标集合。

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)
6. 在“指定内容目标”页上，选择**分发点**
7. 在“指定设置以控制此软件的部署方式”页上，确保已根据需要选择目的。

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. 在“为此部署指定计划”中指定计划。 阅读有关[计划包](https://technet.microsoft.com/library/gg682178.aspx)的详细信息
9. 根据数据中心的需求在“分发点”页上配置属性并完成向导。

> [!TIP]
> 若要避免不必要的重新启动，请在每月的维护时段或软件更新时段计划包安装。

可以通过转到“监视”**** > “部署”**** > *[你的包名称]*
  ![monitor-sccm](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)，使用 SCCM 控制台监视部署进度

## <a name="deploy-mobility-service-on-computers-running-linux-operating-systems"></a>在运行 Linux 操作系统的计算机上部署移动服务
> [!NOTE]
> 本文假定以下设置
> 1. 配置服务器的 IP 地址为 192.168.3.121
> 2. 安全网络文件共享为 \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>步骤 1：准备部署
1. 在网络共享上创建一个文件夹并将其命名为 **MobSvcLinux**
2. 登录到配置服务器，并打开管理命令提示符
3. 运行以下命令以生成密码文件。

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. 将 MobSvc.passphrase 文件复制到网络共享上的 MobSvcWindows 文件夹。
5. 下一步，通过运行以下命令浏览到配置服务器上的安装程序存储库。

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. 将以下文件复制到网络共享上的 **MobSvcLinux** 文件夹
  * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. 复制下面列出的代码，并将它作为 **install_linux.sh** 保存到 **MobSvcLinux** 文件夹
> [!NOTE]
> 请记得将下面脚本中的 [CSIP] 占位符替换为配置服务器的实际 IP 地址值。

```
#!/bin/sh

rm -rf /tmp/MobSvc

mkdir -p /tmp/MobSvc

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
        cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            cp *RHEL7*.tar.gz /tmp/MobSvc
    fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
        echo $OS >> /tmp/MobSvc/sccm.log
        cp *SLES11*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
       cp *UBUNTU*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi
if [ "${OS}" ==  "" ]; then
    exit 1
fi
cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz


if [ -e /usr/local/.vx_version ];
then
    ./install -A u
    echo "Errorcode:$?"
    Error=$?

else
    ./install -t both -a host -R Agent -d /usr/local/ASR -i [CS IP] -p 443 -s y -c https -P MobSvc.passphrase >> /tmp/MobSvc/sccm.log 2>&1 && echo "Install Progress"
    Error=$?
fi
cd /tmp
rm -rf /tm/MobSvc
exit ${Error}
```

### <a name="step-2-create-a-package"></a>步骤 2：创建包

1. 登录到 System Center Configuration Manager 控制台
2. 浏览到“软件库” > “应用程序管理” > “包”
3. 右键单击“包”，然后选择“创建包”
4. 提供“名称”、“说明”、“制造商”、“语言”、“版本”的值。
5. 勾选“此包包含源文件”复选框。
6. 单击“浏览”按钮，然后选择存储安装程序的网络共享 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. 在“选择要创建的程序类型”页上，选择“标准程序”，然后单击“下一步”

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. 在“指定此标准程序的相关信息”页上，提供以下输入，然后单击“下一步”。 （其他输入可以保留其默认值）

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)   
| **参数名称** | **值** |
|--|--|
| Name | 安装 Microsoft Azure 移动服务 (Linux) |
| 命令行 | ./install_linux.sh |
| 程序可以运行 | 用户是否已登录 |

9. 在下一页中，选择“此程序可以在任何平台上运行”****
  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)   

10. 单击“下一步”两次以完成向导。
> [!NOTE]
> 此脚本支持全新安装移动服务代理和升级/更新已安装的代理。

### <a name="step-3-deploy-the-package"></a>步骤 3：部署包
1. 在 SCCM 控制台中，右键单击包，然后选择“分发内容”****
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. 选择包应复制到的**[分发点](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)**。
3. 完成向导后，包将开始复制到指定的分发点。
4. 完成包分发后，右键单击包，然后选择“部署”****
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. 选择在先决条件部分中创建的 Linux 服务器设备集合作为部署的目标集合。

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)
6. 在“指定内容目标”页上，选择**分发点**
7. 在“指定设置以控制此软件的部署方式”页上，确保已根据需要选择目的。

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. 在“为此部署指定计划”中指定计划。 阅读有关[计划包](https://technet.microsoft.com/library/gg682178.aspx)的详细信息
9. 根据数据中心的需求在“分发点”页上配置属性并完成向导。

移动服务将按照你配置的计划安装在 Linux 服务器设备集合上。

## <a name="other-methods-to-install-mobility-services"></a>安装移动服务的其他方法
阅读有关安装移动服务的其他方法的详细信息。
* [使用 GUI 手动安装](http://aka.ms/mobsvcmanualinstall)
* [使用命令行手动安装](http://aka.ms/mobsvcmanualinstallcli)
* [使用配置服务器推送安装](http://aka.ms/pushinstall)
* [使用 Azure 自动化和 Desired State Configuration 自动安装](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>卸载移动服务
与安装一样，可以创建 SCCM 包来卸载移动服务。 使用以下脚本卸载移动服务。

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
现在可以为虚拟机[启用保护](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications)了。

