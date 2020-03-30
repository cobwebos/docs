---
title: 使用 Windows PowerShell DSC 安装连接的计算机代理
description: 在本文中，您将了解如何使用 Azure Arc 将计算机连接到 Azure 的服务器（预览版）。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164678"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>如何使用 Windows PowerShell DSC 安装连接的计算机代理

使用[Windows PowerShell 所需的状态配置](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7)（DSC），您可以自动安装 Windows 计算机的软件和配置。 本文介绍如何使用 DSC 为混合 Windows 计算机上的服务器连接计算机代理安装 Azure Arc。

## <a name="requirements"></a>要求

- Windows PowerShell 版本 4.0 或更高版本

- [Azure 连接计算机 DSC](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0)模块

- 将计算机连接到 Azure Arc 的服务主体，用于非交互式服务器。 如果您尚未为服务器为 Arc 创建服务主体，请按照"[创建服务主体"](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)下的步骤进行大规模载入。

## <a name="install-the-connectedmachine-dsc-module"></a>安装连接的机器 DSC 模块

1. 要手动安装模块，请下载源代码并将项目目录的内容解压缩到 。 `$env:ProgramFiles\WindowsPowerShell\Modules folder` 或者，运行以下命令，使用 PowerShellGet（在 PowerShell 5.0 中）从 PowerShell 库安装：

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 要确认安装，请运行以下命令并确保看到 Azure 连接的计算机 DSC 资源可用。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   在输出中，您应该会看到类似于以下内容的内容：

   ![已连接机器 DSC 模块安装示例的确认](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

此模块中的资源旨在管理 Azure 连接的计算机代理配置。 还包括一个PowerShell脚本`AzureConnectedMachineAgent.ps1`，在`AzureConnectedMachineDsc\examples`文件夹中找到。 它使用社区资源来自动下载和安装，并与 Azure Arc 建立连接。此脚本执行 Azure 门户文章中在["将混合计算机连接到 Azure"](onboard-portal.md)中描述的类似步骤。

如果计算机需要通过代理服务器与服务通信，则在安装代理后，需要运行[此处](onboard-portal.md#configure-the-agent-proxy-setting)描述的命令。 此命令将设置代理服务器系统环境变量 `https_proxy`。 您可以使用[ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)模块使用 DSC 执行此步骤，而不是手动运行该命令。

>[!NOTE]
>为了允许 DSC 运行，需要将 Windows 配置为接收 PowerShell 远程命令，即使您正在运行本地主机配置。 在提升的 PowerShell 终端中运行 `Set-WsManQuickConfig -Force`，即可轻松地正确配置环境。
>

可以使用`Start-DscConfiguration`cmdlet 将配置文档（MOF 文件）应用于计算机。

以下是您传递给 PowerShell 脚本使用的参数。

- `TenantId`：表示 Azure AD 专用实例的唯一标识符 （GUID）。

- `SubscriptionId`：要在 中的计算机的 Azure 订阅的订阅 ID （GUID）。

- `ResourceGroup`：您希望连接的计算机所属的资源组名称。

- `Location`：请参阅[支持的 Azure 区域](overview.md#supported-regions)。 此位置可以与资源组的位置相同或不同。

- `Tags`：应应用于连接的计算机资源的标记字符串数组。

- `Credential`：具有**应用程序 Id**和**密码**的 PowerShell 凭据对象，用于使用[服务主体](onboard-service-principal.md)大规模注册计算机。 

1. 在 PowerShell 控制台中，导航到保存`.ps1`文件的文件夹。

2. 运行以下 PowerShell 命令来编译 MOF 文档（有关编译 DSC 配置的信息，请参阅 [DSC 配置](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)：

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 这将在名为`localhost.mof file`的新文件夹中创建 一`C:\dsc`个 。

安装代理并将其配置为连接到 Azure Arc for servers（预览版）后，请转到 Azure 门户，以验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

## <a name="adding-to-existing-configurations"></a>添加到现有配置

可以将此资源添加到现有的 DSC 配置中，以表示计算机的端到端配置。 例如，您可能希望将此资源添加到设置安全操作系统设置的配置中。

PowerShell 库中的[CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0)模块可用于创建示例配置的[复合资源](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7)，以进一步简化组合配置。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[Azure 策略](../../governance/policy/overview.md)管理计算机，用于 VM[来宾配置](../../governance/policy/concepts/guest-configuration.md)、验证计算机是否报告到预期的日志分析工作区、使用 VM 启用 Azure[监视器](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)等操作。

- 了解有关[日志分析代理](../../azure-monitor/platform/log-analytics-agent.md)的更多信息。 若要主动监视计算机上运行的 OS 和工作负荷、使用自动化 Runbook 或更新管理等解决方案对其进行管理，或使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-intro.md)），需要安装适用于 Windows 和 Linux 的 Log Analytics 代理。