---
title: 从 Azure 门户将混合计算机连接到 Azure
description: 本文介绍如何安装代理，以及如何使用 Azure 门户中的 Azure Arc for server （预览版）将计算机连接到 Azure。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/12/2020
ms.topic: conceptual
ms.openlocfilehash: 12fc29cf12fba6325af3197e727d94b3073ef2ff
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192306"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>从 Azure 门户将混合计算机连接到 Azure

你可以通过手动执行一系列步骤，为你的环境中的一台或多台 Windows 或 Linux 计算机启用适用于服务器（预览版）的 Azure Arc。 或者，您可以通过运行我们提供的模板脚本来使用自动化方法。 此脚本可自动下载和安装两个代理。

此方法要求您在计算机上具有管理员权限才能安装和配置代理。 在 Linux 上，通过使用根帐户，在 Windows 上，你是本地管理员组的成员。

在开始之前，请务必查看[先决条件](overview.md#prerequisites)，并验证你的订阅和资源是否满足要求。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="generate-the-installation-script-from-the-azure-portal"></a>从 Azure 门户生成安装脚本

Azure 门户提供了用于自动执行下载和安装以及与 Azure Arc 建立连接的脚本。 若要完成此过程，请执行以下操作：

1. 在浏览器中转到[Azure 门户](https://aka.ms/hybridmachineportal)。

1. 在 "**计算机-Azure Arc** " 页上，选择 "**添加**"、左上角或中间窗格底部的 "**创建计算机-Azure Arc** " 选项。 

1. 在 "**选择方法**" 页上，选择 "**使用交互式脚本添加计算机**" 磁贴，然后选择 "**生成脚本**"。

1. 在 "**生成脚本**" 页上，选择要在 Azure 中管理虚拟机的订阅和资源组。 选择将在其中存储计算机元数据的 Azure 位置。

    >[!NOTE]
    >适用于服务器（预览版）的 Azure Arc 仅支持以下区域：
    >- WestUS2
    >- 西欧
    >- WestAsia
    >
    >在概述[文章中选择区域时，请](overview.md#supported-regions)查看其他注意事项。

1. 在 "**生成脚本**" 页上，在 "**操作系统**" 下拉列表中，选择脚本将运行的操作系统。

1. 如果计算机通过代理服务器进行通信以连接到 internet，请选择 "**下一步：代理服务器**"。 
1. 在 "**代理服务器**" 选项卡上，指定计算机将用于与代理服务器通信的代理服务器 IP 地址或名称和端口号。 按格式 `http://<proxyURL>:<proxyport>` 输入值。 
1. 选择 "**查看 + 生成**"。

1. 在 "**查看**" 和 "生成" 选项卡上，查看摘要信息，然后选择 "**下载**"。 如果仍需要进行更改，请选择 "**上一步**"。

## <a name="install-and-validate-the-agent-on-windows"></a>在 Windows 上安装和验证代理

### <a name="install-manually"></a>手动安装
可以通过运行 Windows Installer package *AzureConnectedMachineAgent*，手动安装连接的计算机代理。 

> [!NOTE]
> * 若要安装或卸载代理，你必须拥有*管理员*权限。
> * 必须首先将安装程序包下载并复制到目标服务器上的文件夹，或从共享网络文件夹中。 如果在没有任何选项的情况下运行安装程序包，则会启动一个安装向导，你可以按照该向导以交互方式安装代理。

如果计算机需要通过代理服务器与服务进行通信，则在安装代理后，您需要运行本文后面所述的命令。 这会将代理服务器系统环境变量设置 `https_proxy`。

下表突出显示了通过命令行安装代理时支持的参数。

| 参数 | 说明 |
|:--|:--|
| /? | 返回命令行选项的列表。 |
| /S | 执行无需用户交互的无提示安装。 |

例如，若要用 `/?` 参数运行安装程序，请输入 `msiexec.exe /i AzureConnectedMachineAgent.msi /?`。

默认情况下，将在*C:\Program Files\AzureConnectedMachineAgent*中安装连接的计算机代理的文件。 如果在安装程序完成后代理无法启动，请查看日志以获取详细的错误信息。 日志目录为 *%Programfiles%\AzureConnectedMachineAgentAgent\logs*。

### <a name="install-with-the-scripted-method"></a>用脚本化方法安装

1. 登录到服务器。

1. 打开提升权限的 PowerShell 命令提示符。

1. 更改为你将脚本复制到的文件夹或共享，并通过运行 `./OnboardingScript.ps1` 脚本在服务器上执行该脚本。

### <a name="configure-the-agent-proxy-setting"></a>配置代理的代理设置

若要设置代理服务器环境变量，请运行以下命令：

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>此代理不支持在此预览版中设置代理身份验证。
>

### <a name="configure-agent-communication"></a>配置代理通信

安装代理后，需要通过运行以下命令将代理配置为与 Azure Arc 服务通信：

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>在 Linux 上安装和验证代理

适用于 Linux 的已连接计算机代理以首选包格式提供给分发（。RPM 或。DEB）托管在 Microsoft[包存储库](https://packages.microsoft.com/)中。 [Shell 脚本捆绑 `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent)执行以下操作：

- 将主机配置为从 packages.microsoft.com 下载代理包。
- 安装混合资源提供程序包。

或者，你可以通过包含 `--proxy "{proxy-url}:{proxy-port}"` 参数，使用代理信息来配置代理。

该脚本还包含用于识别受支持的和不受支持的分发的逻辑，并验证执行安装所需的权限。 

下面的示例将下载并安装代理：

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

若要下载并安装代理，包括用于将代理配置为通过代理服务器进行通信的 `--proxy` 参数，请运行以下命令：

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>配置代理通信

安装代理后，请通过运行以下命令将其配置为与 Azure Arc 服务通信：

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>验证是否与 Azure Arc 连接

安装代理并将其配置为连接到用于服务器（预览版）的 Azure Arc 后，请切换到 Azure 门户验证是否已成功连接服务器。 在[Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

![成功的服务器连接](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>清理

若要断开计算机与 Azure （预览版）的 Azure Arc 的连接，请执行以下操作：

1. 转到[Azure 门户](https://aka.ms/hybridmachineportal)打开适用于服务器（预览版）的 Azure Arc。

1. 在列表中选择该计算机，选择省略号（ **...** ），然后选择 "**删除**"。

1. 若要从计算机中卸载 Windows 代理，请执行以下操作：

    a. 使用具有管理员权限的帐户登录计算机。  
    b. 在**控制面板**中，选择 "**程序和功能**"。  
    c. 在 "**程序和功能**" 中，选择 " **Azure 连接的计算机代理**"，选择 "**卸载**"，然后选择 **"是"** 。  

    >[!NOTE]
    > 还可以通过双击**AzureConnectedMachineAgent**安装程序包来运行代理安装向导。

    如果要为删除代理编写脚本，可以使用以下示例，该示例将检索产品代码，并使用 Msiexec 命令行 `msiexec /x {Product Code}`来卸载代理。 为此，请执行以下操作：  
    
    a. 打开注册表编辑器。  
    b. 在 "注册表项 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`" 下，查找并复制 "产品代码 GUID"。  
    c. 然后，可以使用 Msiexec 卸载代理。

    下面的示例演示如何卸载代理：

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. 若要卸载 Linux 代理，请运行以下命令：

      ```bash
      sudo apt purge hybridagent
      ```

## <a name="next-steps"></a>后续步骤

- 了解如何使用[Azure 策略](../../governance/policy/overview.md)管理计算机，例如 VM[来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区进行报告，使用[vm Azure Monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)启用监视等操作。

- 详细了解[Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 如果希望主动监视计算机上运行的操作系统和工作负荷，请使用自动化 runbook 或解决方案（如更新管理）或使用其他 Azure 服务（如[Azure 安全中心](../../security-center/security-center-intro.md)）来管理该计算机，则需要使用适用于 Windows 和 Linux 的 Log Analytics 代理。