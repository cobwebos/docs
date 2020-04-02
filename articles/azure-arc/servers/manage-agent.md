---
title: 管理服务器（预览）代理的 Azure 弧
description: 本文介绍了在 Azure Arc 生命周期中为服务器连接的计算机代理执行的不同管理任务。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 8bcf59ee863bb2fd2a3213480372ad215c2fc00d
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528589"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>管理和维护连接的计算机代理

在 Windows 或 Linux 的服务器（预览）连接计算机代理的 Azure Arc 初始部署后，如果代理在其生命周期中已达到停用阶段，则可能需要重新配置代理、升级代理或将其从计算机中删除。 可以轻松地手动或自动管理这些日常维护任务，从而减少运行错误并降低费用。

## <a name="upgrading-agent"></a>升级代理

Windows 和 Linux 的 Azure 连接计算机代理可以手动或自动升级到最新版本，具体取决于您的要求。 下表描述了为执行代理升级而支持的方法。

| 操作系统 | 升级方法 |
|------------------|----------------|
| Windows | 手动<br> Windows 更新 |
| Ubuntu | [容易](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [齐珀](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| 红帽企业， 亚马逊， CentOS Linux | [百 胜](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows 代理

要将 Windows 计算机上的代理更新到最新版本，该代理可从 Microsoft Update 获得，并且可以使用现有的软件更新管理过程进行部署。 也可以从命令提示符、脚本或其他自动化解决方案手动运行它，也可以通过执行 从 UI 向导手动运行`AzureConnectedMachine.msi`。 

> [!NOTE]
> * 要升级代理，您必须具有*管理员*权限。
> * 要手动升级，必须首先将安装程序包下载并复制到目标服务器上的文件夹或从共享网络文件夹中。 

如果您不熟悉 Windows 安装程序包的命令行选项，请查看[Msiexec 标准命令行选项](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options)和[Msiexec 命令行选项](https://docs.microsoft.com/windows/win32/msi/command-line-options)。

#### <a name="to-upgrade-using-the-setup-wizard"></a>使用安装向导进行升级

1. 使用具有管理权限的帐户登录到计算机。

2. 执行**Azure 连接计算机代理.msi**以启动安装程序向导。

安装程序向导发现是否存在以前的版本，然后它会自动执行代理的升级。 升级完成后，设置向导将自动关闭。

#### <a name="to-upgrade-from-the-command-line"></a>从命令行进行升级

1. 使用具有管理权限的帐户登录到计算机。

2. 要静默升级代理并在文件夹中创建设置日志文件，`C:\Support\Logs`请运行以下命令。

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 代理

要将 Linux 计算机上的代理更新到最新版本，它涉及两个命令。 一个命令使用存储库中的最新可用包列表更新本地包索引，以及一个用于升级本地包的命令。 

> [!NOTE]
> 要升级代理，您必须具有*根*访问权限或使用 Sudo 具有提升权限的帐户。

#### <a name="upgrade-ubuntu"></a>升级乌本图

1. 要使用存储库中所做的最新更改更新本地包索引，请运行以下命令：

    ```bash
    apt update
    ```

2. 要升级系统，请运行以下命令：

    ```bash
    apt upgrade
    ```

[apt](https://help.ubuntu.com/lts/serverguide/apt.html)命令的操作（如包的安装和删除）将记录在日志文件中`/var/log/dpkg.log`。

#### <a name="upgrade-red-hatcentosamazon-linux"></a>升级红帽/CentOS/亚马逊Linux

1. 要使用存储库中所做的最新更改更新本地包索引，请运行以下命令：

    ```bash
    yum check-update
    ```

2. 要升级系统，请运行以下命令：

    ```bash
    yum update
    ```

[yum](https://access.redhat.com/articles/yum-cheat-sheet)命令的操作（如包的安装和删除）将记录在日志文件中`/var/log/yum.log`。 

#### <a name="upgrade-suse-linux-enterprise"></a>升级 SUSE Linux 企业

1. 要使用存储库中所做的最新更改更新本地包索引，请运行以下命令：

    ```bash
    zypper refresh
    ```

2. 要升级系统，请运行以下命令：

    ```bash
    zypper update
    ```

[zypper](https://en.opensuse.org/Portal:Zypper)命令的操作（如包的安装和删除）将记录在日志文件中`/var/log/zypper.log`。 

## <a name="remove-the-agent"></a>删除代理

执行以下方法之一，从计算机卸载 Windows 或 Linux 连接的计算机代理。 删除代理不会将计算机与 Arc 注销为服务器（预览），这是当您不再需要在 Azure 中管理计算机时执行的单独过程。

### <a name="windows-agent"></a>Windows 代理

以下两种方法都删除代理，但它们不会删除计算机上的*C：*程序文件_AzureConnectedMachineAgent 文件夹*。

#### <a name="uninstall-from-control-panel"></a>从控制面板卸载

1. 若要从计算机中卸载 Windows 代理，请执行以下操作：

    a. 使用拥有管理员权限的帐户登录到计算机。  
    b. 在“控制面板”中，选择“程序和功能”。********  
    c. 在“程序和功能”中，依次选择“Azure Connected Machine Agent”、“卸载”、“是”。****************  

    >[!NOTE]
    > 也可以双击 **AzureConnectedMachineAgent.msi** 安装程序包运行代理安装向导。

#### <a name="uninstall-from-the-command-line"></a>从命令行卸载

要从命令提示符手动卸载代理或使用自动方法（如脚本），可以使用以下示例。 首先，您需要从操作系统中检索产品代码，即作为应用程序包的主要标识符的 GUID。 卸载使用 msiexec.exe 命令行 -`msiexec /x {Product Code}`执行。
    
1. 打开注册表编辑器。

2. 在注册表项 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 下，查找并复制产品代码 GUID。

3. 然后，您可以使用以下示例使用 Msiexec 卸载代理：

   * 从命令行类型：

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * 您可以使用 PowerShell 执行相同的步骤：

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 代理

> [!NOTE]
> 要卸载代理，您必须具有*根*访问权限或使用 Sudo 具有提升权限的帐户。

要卸载 Linux 代理，要使用的命令取决于 Linux 操作系统。

- 对于 Ubuntu，运行以下命令：

    ```bash
    sudo apt purge azcmagent
    ```

- 对于 RHEL、CentOS 和 Amazon Linux，运行以下命令：

    ```bash
    sudo yum remove azcmagent
    ```

- 对于 SLES，运行以下命令：

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>取消注册计算机

如果计划停止使用 Azure 中的支持服务管理计算机，请执行以下步骤，将计算机与 Arc 注销以用于服务器（预览）。 您可以在从机器中删除已连接的计算机代理之前或之后执行这些步骤。

1. 转到 [Azure 门户](https://aka.ms/hybridmachineportal)并打开 Azure Arc for servers（预览版）。

2. 在列表中选择计算机，选择省略号图标 (**...**)，然后选择“删除”。****
