---
title: 从 Azure 门户将混合计算机连接到 Azure
description: 本文介绍如何安装代理，并通过 Azure 门户使用 Azure Arc for servers（预览版）将计算机连接到 Azure。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: ac0a795c98673eba30531f586ff634c62673cdd6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980943"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>从 Azure 门户将混合计算机连接到 Azure

可以通过手动执行一系列步骤，为环境中的一台或少量 Windows 或 Linux 计算机启用 Azure Arc for servers（预览版）。 或者，可以运行我们提供的模板脚本来使用自动化方法。 此脚本可以自动下载和安装两个代理。

这种安装和配置代理的方法要求你在计算机上拥有管理员权限。 在 Linux 上，需使用 root 帐户；在 Windows 上，你需是“本地管理员组”的成员。

在开始之前，请务必查看[先决条件](overview.md#prerequisites)，并验证你的订阅和资源是否符合要求。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="generate-the-installation-script-from-the-azure-portal"></a>从 Azure 门户生成安装脚本

Azure 门户中提供了用于自动下载和安装以及与 Azure Arc 建立连接的脚本。 若要完成该过程，请执行以下操作：

1. 在浏览器中转到 [Azure 门户](https://aka.ms/hybridmachineportal)。

1. 在“计算机 - Azure Arc”页上，选择左上角的“添加”，或者选择中间窗格底部的“创建计算机 - Azure Arc”选项。************ 

1. 在“选择方法”页上选择“使用交互式脚本添加计算机”磁贴，然后选择“生成脚本”。************

1. 在“生成脚本”页上，选择你要在 Azure 中管理的计算机所在的订阅和资源组。**** 选择要将计算机元数据存储到的 Azure 位置。

    >[!NOTE]
    >Azure Arc for servers（预览版）仅支持以下区域：
    >- 美国西部 2
    >- 西欧
    >- 西亚
    >
    >在概述[文章中选择区域时，请](overview.md#supported-regions)查看其他注意事项。

1. 在“生成脚本”页上的“操作系统”下拉列表中，选择运行脚本的操作系统。********

1. 如果计算机通过代理服务器进行通信以连接到 internet，请选择 "**下一步：代理服务器**"。 
1. 在“代理服务器”选项卡上，指定计算机用来与代理服务器通信的代理服务器 IP 地址或名称以及端口号。**** 按格式 `http://<proxyURL>:<proxyport>` 输入值。 
1. 选择“查看 + 生成”。****

1. 在“查看 + 生成”选项卡上查看摘要信息，然后选择“下载”。******** 如果仍需进行更改，请选择“上一页”。****

## <a name="install-and-validate-the-agent-on-windows"></a>在 Windows 上安装并验证代理

### <a name="install-manually"></a>手动安装

可以运行 Windows Installer 包 *AzureConnectedMachineAgent.msi* 来手动安装 Connected Machine 代理。 

> [!NOTE]
> * 若要安装或卸载代理，必须拥有“管理员”权限。**
> * 必须先下载 Installer 包并将其复制到目标服务器上的某个文件夹，或者从共享网络文件夹下载。 如果在不指定任何选项的情况下运行该 Installer 包，它将启动一个安装向导，以交互方式指导用户安装代理。

如果计算机需要通过代理服务器来与服务进行通信，则在安装代理后，需要运行本文稍后所述的某个命令。 此命令将设置代理服务器系统环境变量 `https_proxy`。

如果不熟悉 Windows Installer 包的命令行选项，请查看[Msiexec 标准命令行选项](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options)和[Msiexec 命令行选项](https://docs.microsoft.com/windows/win32/msi/command-line-options)。

例如，运行带有`/?`参数的安装程序以查看帮助和快速参考选项。 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

若要以无提示方式安装代理并在存在的`C:\Support\Logs`文件夹中创建安装日志文件，请运行以下命令。

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

默认情况下，将在*C:\Program Files\AzureConnectedMachineAgent*中安装连接的计算机代理的文件。 如果完成安装后代理无法启动，请检查日志以获取详细的错误信息。 日志目录为 *%Programfiles%\AzureConnectedMachineAgentAgent\logs*。

### <a name="install-with-the-scripted-method"></a>使用脚本方法安装

1. 登录到服务器。

1. 打开权限提升的 PowerShell 命令提示符。

1. 切换到脚本所复制到的文件夹或共享，然后在服务器上运行 `./OnboardingScript.ps1` 脚本来执行复制的脚本。

### <a name="configure-the-agent-proxy-setting"></a>配置代理程序代理设置

若要设置代理服务器环境变量，请运行以下命令：

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>此预览版中的代理程序不支持设置代理身份验证。
>

### <a name="configure-agent-communication"></a>配置代理通信

安装代理后，需要运行以下命令来配置代理，使其能够与 Azure Arc 服务通信：

`"%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>在 Linux 上安装并验证代理

适用于 Linux 的 Connected Machine 代理以 Microsoft [包存储库](https://packages.microsoft.com/)中分发版的首选包格式（.RPM 或 .DEB）提供。 [shell 脚本捆绑包 `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) 执行以下操作：

- 将主机配置为从 packages.microsoft.com 下载代理包。
- 安装混合资源提供程序包。

（可选）可以通过包含 `--proxy "{proxy-url}:{proxy-port}"` 参数使用代理信息来配置代理。

该脚本还包含用于识别受支持和不受支持分发包的逻辑，并可验证执行安装所需的权限。 

以下示例将下载并安装代理：

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

若要下载并安装代理，请结合 `--proxy` 参数（用于将代理配置为通过代理服务器通信）运行以下命令：

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>配置代理通信

安装代理后，请运行以下命令，将其配置为与 Azure Arc 服务通信：

`azcmagent connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>验证与 Azure Arc 的连接

安装代理并将其配置为连接到 Azure Arc for servers（预览版）后，请转到 Azure 门户，以验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

![服务器连接成功](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

- 了解如何使用[Azure 策略](../../governance/policy/overview.md)管理计算机，例如 VM[来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区进行报告，使用[vm Azure Monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)启用监视等操作。

- 详细了解[Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 若要主动监视计算机上运行的 OS 和工作负荷、使用自动化 Runbook 或更新管理等解决方案对其进行管理，或使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-intro.md)），需要安装适用于 Windows 和 Linux 的 Log Analytics 代理。
