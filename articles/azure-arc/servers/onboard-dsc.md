---
title: 使用 Windows PowerShell DSC 安装连接的计算机代理
description: 本文介绍如何使用 Windows PowerShell DSC，使用适用于服务器的 Azure Arc （预览版）将计算机连接到 Azure。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164678"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>如何使用 Windows PowerShell DSC 安装连接的计算机代理

使用[Windows PowerShell Desired State Configuration](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) （DSC），可以自动安装和配置 windows 计算机的软件。 本文介绍如何在混合 Windows 计算机上使用 DSC 为连接的计算机代理安装 Azure Arc。

## <a name="requirements"></a>要求

- Windows PowerShell 版本4.0 或更高版本

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC 模块

- 用于以非交互方式将计算机连接到 Azure 的 Azure Arc 的服务主体。 如果尚未为服务器的 "Arc" 创建服务主体，请按照 "[创建服务主体以进行大规模载入](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)" 一节中的步骤进行操作。

## <a name="install-the-connectedmachine-dsc-module"></a>安装 ConnectedMachine DSC 模块

1. 若要手动安装该模块，请下载源代码，并将项目目录的内容解压缩到 `$env:ProgramFiles\WindowsPowerShell\Modules folder`。 或者，运行以下命令，使用 PowerShellGet 从 PowerShell 库安装（在 PowerShell 5.0 中）：

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 若要确认安装，请运行以下命令，并确保看到 Azure 连接的计算机 DSC 资源可用。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   在输出中，应会看到如下所示的内容：

   ![确认已连接的计算机 DSC 模块安装示例](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

此模块中的资源旨在管理 Azure 连接的计算机代理配置。 还包括 `AzureConnectedMachineDsc\examples` 文件夹中 `AzureConnectedMachineAgent.ps1`的 PowerShell 脚本。 它使用社区资源自动执行下载和安装，并建立与 Azure Arc 的连接。此脚本执行[从 Azure 门户将混合计算机连接到 Azure](onboard-portal.md)一文中所述的类似步骤。

如果计算机需要通过代理服务器与服务进行通信，则在安装代理后，您需要运行[此处](onboard-portal.md#configure-the-agent-proxy-setting)所述的命令。 此命令将设置代理服务器系统环境变量 `https_proxy`。 您可以使用[ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)模块通过 DSC 执行此步骤，而不是手动运行该命令。

>[!NOTE]
>若要允许 DSC 运行，需要将 Windows 配置为接收 PowerShell 远程命令，即使在运行 localhost 配置时也是如此。 在提升的 PowerShell 终端中运行 `Set-WsManQuickConfig -Force`，即可轻松地正确配置环境。
>

可以使用 `Start-DscConfiguration` cmdlet 将配置文档（MOF 文件）应用到计算机。

下面是传递给要使用的 PowerShell 脚本的参数。

- `TenantId`：代表 Azure AD 专用实例的唯一标识符（GUID）。

- `SubscriptionId`：你要将计算机置于其中的 Azure 订阅的订阅 ID （GUID）。

- `ResourceGroup`：希望连接的计算机所属的资源组名称。

- `Location`：请参阅[支持的 Azure 区域](overview.md#supported-regions)。 此位置可以与资源组的位置相同或不同。

- `Tags`：应应用于已连接计算机资源的标记的字符串数组。

- `Credential`：一个使用**ApplicationId**和**密码**的 PowerShell 凭据对象，用于在规模上使用[服务主体](onboard-service-principal.md)注册计算机。 

1. 在 PowerShell 控制台中，导航到保存 `.ps1` 文件的文件夹。

2. 运行以下 PowerShell 命令来编译 MOF 文档（有关编译 DSC 配置的信息，请参阅 [DSC 配置](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)：

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 这会在名为 `C:\dsc`的新文件夹中创建 `localhost.mof file`。

安装代理并将其配置为连接到 Azure Arc for servers（预览版）后，请转到 Azure 门户，以验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

## <a name="adding-to-existing-configurations"></a>添加到现有配置

可以将此资源添加到现有 DSC 配置，以表示计算机的端到端配置。 例如，你可能希望将此资源添加到设置安全操作系统设置的配置中。

PowerShell 库中的[CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0)模块可用于创建示例配置的[复合资源](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7)，以进一步简化组合配置。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[Azure 策略](../../governance/policy/overview.md)管理计算机，例如 VM[来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区进行报告，使用[vm Azure Monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)启用监视等操作。

- 详细了解[Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 若要主动监视计算机上运行的 OS 和工作负荷、使用自动化 Runbook 或更新管理等解决方案对其进行管理，或使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-intro.md)），需要安装适用于 Windows 和 Linux 的 Log Analytics 代理。