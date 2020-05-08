---
title: 管理用于服务器（预览版）的 Azure Arc 代理
description: 本文介绍常见的管理任务，这些任务通常会在 Azure Arc 的服务器连接的计算机代理生命周期内执行。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/29/2020
ms.topic: conceptual
ms.openlocfilehash: 685c56c7ef270acb416d4b76c6aceb8553e9a07f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82581709"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>管理并维护连接的计算机代理

初始部署适用于服务器（预览版）的 Azure Arc （适用于 Windows 或 Linux）的已连接计算机代理后，你可能需要重新配置并升级该代理，或将其从计算机中删除（如果它在其生命周期中已达到停用阶段）。 可以轻松地手动或自动管理这些日常维护任务，从而减少运行错误并降低费用。

## <a name="upgrading-agent"></a>升级代理

适用于 Windows 和 Linux 的 Azure Connected Machine 代理可以手动或自动升级到最新版本，具体取决于你的要求。 下表介绍了执行代理升级所支持的方法。

| 操作系统 | 升级方法 |
|------------------|----------------|
| Windows | 手动<br> Windows 更新 |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise，Amazon，CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows 代理

若要将 Windows 计算机上的代理更新到最新版本，可以从 Microsoft 更新获取该代理，并使用现有的软件更新管理过程进行部署。 还可以从命令提示符、脚本或其他自动化解决方案手动运行，也可以通过执行`AzureConnectedMachine.msi`从 UI 向导手动运行。 

> [!NOTE]
> * 若要升级代理，你必须拥有*管理员*权限。
> * 若要手动升级，必须首先将安装程序包下载并复制到目标服务器上的文件夹，或从共享的网络文件夹中。 

如果不熟悉 Windows Installer 包的命令行选项，请查看[Msiexec 标准命令行选项](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options)和[Msiexec 命令行选项](https://docs.microsoft.com/windows/win32/msi/command-line-options)。

#### <a name="to-upgrade-using-the-setup-wizard"></a>使用安装向导进行升级

1. 使用具有管理权限的帐户登录到计算机。

2. 执行**AzureConnectedMachineAgent**以启动安装向导。

安装向导将发现以前的版本是否存在，然后自动执行代理的升级。 升级完成后，安装向导会自动关闭。

#### <a name="to-upgrade-from-the-command-line"></a>从命令行进行升级

1. 使用具有管理权限的帐户登录到计算机。

2. 若要在无提示的情况下升级代理并在`C:\Support\Logs`文件夹中创建安装日志文件，请运行以下命令。

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 代理

若要将 Linux 计算机上的代理更新到最新版本，则它涉及两个命令。 使用存储库中的最新可用包列表更新本地包索引和升级本地包的一个命令。 

> [!NOTE]
> 若要升级代理，必须具有*根*访问权限，或者具有使用 Sudo 提升权限的帐户。

#### <a name="upgrade-ubuntu"></a>升级 Ubuntu

1. 若要用存储库中所做的最新更改更新本地包索引，请运行以下命令：

    ```bash
    apt update
    ```

2. 若要升级系统，请运行以下命令：

    ```bash
    apt upgrade
    ```

[Apt](https://help.ubuntu.com/lts/serverguide/apt.html)命令的操作（如安装和删除包）会记录在`/var/log/dpkg.log`日志文件中。

#### <a name="upgrade-red-hatcentosamazon-linux"></a>升级 Red Hat/CentOS/Amazon Linux

1. 若要用存储库中所做的最新更改更新本地包索引，请运行以下命令：

    ```bash
    yum check-update
    ```

2. 若要升级系统，请运行以下命令：

    ```bash
    yum update
    ```

[Yum](https://access.redhat.com/articles/yum-cheat-sheet)命令的操作（如安装和删除包）会记录在`/var/log/yum.log`日志文件中。 

#### <a name="upgrade-suse-linux-enterprise"></a>升级 SUSE Linux Enterprise

1. 若要用存储库中所做的最新更改更新本地包索引，请运行以下命令：

    ```bash
    zypper refresh
    ```

2. 若要升级系统，请运行以下命令：

    ```bash
    zypper update
    ```

[Zypper](https://en.opensuse.org/Portal:Zypper)命令的操作（如安装和删除包）会记录在`/var/log/zypper.log`日志文件中。 

## <a name="about-the-azcmagent-tool"></a>关于 Azcmagent 工具

Azcmagent 工具（Azcmagent）用于在安装过程中配置用于服务器（预览版）的 Azure Arc 连接的计算机代理，或在安装后修改代理的初始配置。 Azcmagent 提供了用于自定义代理并查看其状态的命令行参数：

* **连接**-将计算机连接到 Azure Arc

* **断开**-从 Azure Arc 断开计算机连接

* **重新连接**-将断开连接的计算机重新连接到 Azure Arc

* **显示**-查看代理状态及其配置属性（资源组名称、订阅 ID、版本等），这有助于排查与代理有关的问题。

* **-h 或--help** -显示可用的命令行参数

    例如，若要查看**重新连接**参数的详细帮助，请`azcmagent reconnect -h`键入。 

* **-v 或--verbose** -启用详细日志记录

可以在交互式登录时手动执行**连接**、**断开**连接和**重新连接**，或使用用于连接多个代理的相同服务主体或使用 Microsoft 标识平台[访问令牌](../../active-directory/develop/access-tokens.md)自动执行。 如果未使用服务主体向服务器注册 Azure Arc （预览版），请参阅以下[文章](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)来创建服务主体。

### <a name="connect"></a>连接

此参数指定 Azure 资源管理器中的资源，该资源表示在 Azure 中创建的计算机。 资源在指定的订阅和资源组中，计算机上的数据存储在`--location`设置指定的 Azure 区域中。 如果未指定，则默认资源名称是此计算机的主机名。

然后，将下载与系统分配的计算机标识相对应的证书，并将其存储在本地。 完成此步骤后，Azure 连接的计算机元数据服务和来宾配置代理将开始与用于服务器的 Azure Arc 同步（预览版）。

若要使用服务主体进行连接，请运行以下命令：

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

若要使用访问令牌进行连接，请运行以下命令：

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

若要连接到提升的登录凭据（交互式），请运行以下命令：

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>断开连接

此参数指定资源管理器 Azure 中的资源，该资源表示在 Azure 中删除的计算机。 它不会从计算机中删除代理，这必须是单独的步骤。 断开计算机的连接后，如果想要使用 Azure Arc 为服务器（预览版）重新注册，请使用`azcmagent connect` ，以便在 azure 中为其创建新资源。

若要使用服务主体断开连接，请运行以下命令：

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

若要使用访问令牌断开连接，请运行以下命令：

`azcmagent disconnect --access-token <accessToken>`

若要与已提升的登录凭据断开连接（交互式），请运行以下命令：

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>重新连接

此参数将已注册或已连接的计算机与用于服务器的 Azure Arc （预览版）重新连接。 如果计算机已关闭（至少45天）其证书过期，则可能需要执行此功能。 此参数使用提供的身份验证选项来检索与表示此计算机的 Azure 资源管理器资源相对应的新凭据。

此命令需要比[Azure 连接的计算机加入](overview.md#required-permissions)角色更高的特权。

若要使用服务主体重新连接，请运行以下命令：

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

若要使用访问令牌重新连接，请运行以下命令：

`azcmagent reconnect --access-token <accessToken>`

若要重新连接到已提升的登录凭据（交互式），请运行以下命令：

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>删除代理

执行以下方法之一，从计算机中卸载 Windows 或 Linux 连接的计算机代理。 删除代理时，不会为服务器（预览版）的 Arc 取消注册计算机，这是你不再需要在 Azure 中管理计算机时执行的单独过程。

### <a name="windows-agent"></a>Windows 代理

以下两种方法都将删除代理，但不会删除计算机上的*C:\Program Files\AzureConnectedMachineAgent*文件夹。

#### <a name="uninstall-from-control-panel"></a>从控制面板卸载

1. 若要从计算机中卸载 Windows 代理，请执行以下操作：

    a. 使用拥有管理员权限的帐户登录到计算机。  
    b. 在“控制面板”中，选择“程序和功能”。********  
    c. 在“程序和功能”中，依次选择“Azure Connected Machine Agent”、“卸载”、“是”。****************  

    >[!NOTE]
    > 也可以双击 **AzureConnectedMachineAgent.msi** 安装程序包运行代理安装向导。

#### <a name="uninstall-from-the-command-line"></a>从命令行卸载

若要从命令提示符手动卸载代理或使用自动方法（如脚本），可以使用以下示例。 首先，需要从操作系统中检索产品代码，该代码是应用程序包主体标识符的 GUID。 使用 Msiexec 命令行执行卸载`msiexec /x {Product Code}`。
    
1. 打开注册表编辑器。

2. 在注册表项 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 下，查找并复制产品代码 GUID。

3. 然后，可以使用 Msiexec 通过以下示例卸载代理：

   * 从命令行类型：

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * 可以使用 PowerShell 执行相同的步骤：

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 代理

> [!NOTE]
> 若要卸载代理，则必须具有*根*访问权限，或者具有使用 Sudo 提升权限的帐户。

要卸载 Linux 代理，使用的命令取决于 Linux 操作系统。

- 对于 Ubuntu，请运行以下命令：

    ```bash
    sudo apt purge azcmagent
    ```

- 对于 RHEL、CentOS 和 Amazon Linux，请运行以下命令：

    ```bash
    sudo yum remove azcmagent
    ```

- 对于 SLES，请运行以下命令：

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>注销计算机

如果计划在 Azure 中停止管理具有支持服务的计算机，请执行以下步骤以通过 Arc 为服务器取消注册计算机（预览版）。 你可以在从计算机中删除连接的计算机代理之前或之后执行这些步骤。

1. 转到 [Azure 门户](https://aka.ms/hybridmachineportal)并打开 Azure Arc for servers（预览版）。

2. 在列表中选择计算机，选择省略号图标 (**...**)，然后选择“删除”。****

## <a name="update-or-remove-proxy-settings"></a>更新或删除代理设置

若要将代理配置为通过代理服务器与服务进行通信，或在部署后删除此配置，请使用以下方法之一来完成此任务。

### <a name="windows"></a>Windows

若要设置代理服务器环境变量，请运行以下命令：

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

若要将代理配置为停止通过代理服务器进行通信，请运行以下命令删除代理服务器环境变量并重新启动代理服务：

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

若要设置代理服务器，请从下载代理安装包的目录运行以下命令：

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

若要将代理配置为停止通过代理服务器进行通信，请运行以下命令来删除代理配置：

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>后续步骤

- 了解如何使用[Azure 策略](../../governance/policy/overview.md)管理计算机，例如 VM[来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区进行报告，使用[vm Azure Monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)启用监视等操作。

- 详细了解[Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 如果希望主动监视计算机上运行的操作系统和工作负荷，请使用自动化 runbook 或功能（如更新管理）管理它，或使用[Azure 安全中心](../../security-center/security-center-intro.md)之类的其他 azure 服务，则需要使用适用于 Windows 和 Linux 的 Log Analytics 代理。