---
title: 从 Azure 门户将混合计算机连接到 Azure
description: 本文介绍如何安装代理，并通过 Azure 门户使用 Azure Arc for servers（预览版）将计算机连接到 Azure。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 81083a9d94f782201a8eb765ac1f88093c0337c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024086"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>从 Azure 门户将混合计算机连接到 Azure

可以通过手动执行一系列步骤，为环境中的一台或少量 Windows 或 Linux 计算机启用 Azure Arc for servers（预览版）。 或者，可以运行我们提供的模板脚本来使用自动化方法。 此脚本可以自动下载和安装两个代理。

这种安装和配置代理的方法要求你在计算机上拥有管理员权限。 在 Linux 上，需使用 root 帐户；在 Windows 上，你需是“本地管理员组”的成员。

在开始之前，请务必查看[先决条件](overview.md#prerequisites)，并验证你的订阅和资源是否符合要求。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="generate-the-installation-script-from-the-azure-portal"></a>从 Azure 门户生成安装脚本

Azure 门户中提供了用于自动下载和安装以及与 Azure Arc 建立连接的脚本。 若要完成该过程，请执行以下操作：

1. 在浏览器中转到 [Azure 门户](https://aka.ms/hybridmachineportal)。

1. 在“计算机 - Azure Arc”页上，选择左上角的“添加”，或者选择中间窗格底部的“创建计算机 - Azure Arc”选项。    

1. 在“选择方法”页上选择“使用交互式脚本添加计算机”磁贴，然后选择“生成脚本”。   

1. 在“生成脚本”页上，选择你要在 Azure 中管理的计算机所在的订阅和资源组。  选择要将计算机元数据存储到的 Azure 位置。

    >[!NOTE]
    >Azure Arc for servers（预览版）仅支持以下区域：
    >- 美国西部 2
    >- 西欧
    >- 西亚
    >

1. 在“生成脚本”页上的“操作系统”下拉列表中，选择运行脚本的操作系统。  

1. 如果计算机通过代理服务器连接到 Internet 进行通信，请选择“下一步:  代理服务器”。 
1. 在“代理服务器”选项卡上，指定计算机用来与代理服务器通信的代理服务器 IP 地址或名称以及端口号。  按格式 `http://<proxyURL>:<proxyport>` 输入值。 
1. 选择“查看 + 生成”。 

1. 在“查看 + 生成”选项卡上查看摘要信息，然后选择“下载”。   如果仍需进行更改，请选择“上一页”。 

## <a name="install-and-validate-the-agent-on-windows"></a>在 Windows 上安装并验证代理

### <a name="install-manually"></a>手动安装
可以运行 Windows Installer 包 *AzureConnectedMachineAgent.msi* 来手动安装 Connected Machine 代理。 

> [!NOTE]
> * 若要安装或卸载代理，必须拥有“管理员”权限。 
> * 必须先下载 Installer 包并将其复制到目标服务器上的某个文件夹，或者从共享网络文件夹下载。 如果在不指定任何选项的情况下运行该 Installer 包，它将启动一个安装向导，以交互方式指导用户安装代理。

如果计算机需要通过代理服务器来与服务进行通信，则在安装代理后，需要运行本文稍后所述的某个命令。 此命令将设置代理服务器系统环境变量 `https_proxy`。

下表突出显示了通过命令行安装代理时支持的参数。

| 参数 | 说明 |
|:--|:--|
| /? | 返回命令行选项的列表。 |
| /S | 执行无需用户交互的无提示安装。 |

例如，若要使用 `/?` 参数运行安装程序，请输入 `msiexec.exe /i AzureConnectedMachineAgent.msi /?`。

Connected Machine 代理的文件默认安装在 *C:\Program Files\AzureConnectedMachineAgent* 中。 如果完成安装后代理无法启动，请检查日志以获取详细的错误信息。 日志目录为 *%Programfiles%\AzureConnectedMachineAgentAgent\logs*。

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

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

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

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>验证与 Azure Arc 的连接

安装代理并将其配置为连接到 Azure Arc for servers（预览版）后，请转到 Azure 门户，以验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

![服务器连接成功](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>清除

若要从 Azure Arc for servers（预览版）断开计算机的连接，请执行以下操作：

1. 转到 [Azure 门户](https://aka.ms/hybridmachineportal)并打开 Azure Arc for servers（预览版）。

1. 在列表中选择计算机，选择省略号图标 ( **...** )，然后选择“删除”。 

1. 若要从计算机中卸载 Windows 代理，请执行以下操作：

    a. 使用拥有管理员权限的帐户登录到计算机。  
    b. 在“控制面板”中，选择“程序和功能”。    
    c. 在“程序和功能”中，依次选择“Azure Connected Machine Agent”、“卸载”、“是”。      

    >[!NOTE]
    > 也可以双击 **AzureConnectedMachineAgent.msi** 安装程序包运行代理安装向导。

    若要编写卸载脚本，可以使用以下示例，它会检索产品代码，并使用 Msiexec.exe 命令行 `msiexec /x {Product Code}` 卸载代理。 为此，请执行以下操作：  
    
    a. 打开注册表编辑器。  
    b. 在注册表项 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 下，查找并复制产品代码 GUID。  
    c. 然后，可以使用 Msiexec 卸载代理。

    以下示例演示如何卸载代理：

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

> [!div class="nextstepaction"]
> [将策略分配到联网计算机](../../governance/policy/assign-policy-portal.md)
