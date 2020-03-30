---
title: 缩放会话承载 Azure 自动化 - Azure
description: 如何使用 Azure 自动化自动缩放 Windows 虚拟桌面会话主机。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349866"
---
# <a name="scale-session-hosts-using-azure-automation"></a>使用 Azure 自动化扩展会话主机

您可以通过扩展虚拟机 （VM） 来降低 Windows 虚拟桌面部署总成本。 这意味着在非高峰使用时间内关闭和释放会话主机 VM，然后在高峰时段重新打开并重新分配它们。

在本文中，您将了解使用 Azure 自动化和 Azure 逻辑应用构建的缩放工具，该工具将自动缩放 Windows 虚拟桌面环境中的会话主机虚拟机。 要了解如何使用缩放工具，请跳到[先决条件](#prerequisites)。

## <a name="how-the-scaling-tool-works"></a>缩放工具的工作原理

扩展工具为希望优化其会话主机 VM 成本的客户提供了低成本的自动化选项。

您可以使用缩放工具：
 
- 根据高峰和非高峰营业时间安排 VM 启动和停止。
- 根据每个 CPU 内核的会话数横向扩展 VM。
- 在非高峰时间以 VM 进行缩放，使会话主机 VM 的最小数量处于运行状态。

缩放工具使用 Azure 自动化 PowerShell 运行簿、Webhook 和 Azure 逻辑应用的组合来运行。 该工具运行时，Azure 逻辑应用调用 Webhook 以启动 Azure 自动化运行簿。 然后，Runbook 创建一个作业。

在高峰使用期间，作业会检查每个主机池的当前正在运行的会话主机的当前会话数和 VM 容量。 它使用此信息计算正在运行的会话主机 VM 是否可以基于为**createazurelogicapp.ps1**文件定义的*会话阈值PerCPU*参数支持现有会话。 如果会话主机 VM 不支持现有会话，作业将在主机池中启动其他会话主机 VM。

>[!NOTE]
>*会话阈值PerCPU*不限制 VM 上的会话数。 此参数仅确定何时需要启动新 VM 来负载平衡连接。 要限制会话数，您需要按照说明["设置-RdsHostPool"](/powershell/module/windowsvirtualdesktop/set-rdshostpool/)相应地配置*MaxSessionLimit*参数。

在非高峰使用时间内，作业根据*最小数量RDSH*参数确定应关闭的会话主机 VM。 作业将会话主机 VM 设置为耗尽模式，以防止连接到主机的新会话。 如果将 *"限制秒秒到ForceLogOffUser"* 参数设置为非零正值，作业将通知当前登录的任何用户保存其工作，等待配置的时间量，然后强制用户注销。一旦会话主机 VM 上的所有用户会话都已注销，作业将关闭 VM。

如果将 *"限制秒秒到ForceLogOffUser"* 参数设置为零，作业将允许指定组策略中的会话配置设置来处理注销用户会话。 要查看这些组策略，请访问**计算机配置** > **策略** > **管理模板** > **Windows 组件** > **终端服务** > **终端服务器** > **会话时间限制**。 如果会话主机 VM 上有任何活动会话，则作业将使会话主机 VM 保持运行。 如果没有活动会话，作业将关闭会话主机 VM。

作业根据设置的定期间隔定期运行。 您可以根据 Windows 虚拟桌面环境的大小更改此间隔，但请记住，启动和关闭虚拟机可能需要一些时间，因此请记住考虑延迟。 我们建议将定期间隔设置为每 15 分钟一次。

但是，该工具也有以下限制：

- 此解决方案仅适用于池会话主机 VM。
- 此解决方案管理任何区域中的 VM，但只能在与 Azure 自动化帐户和 Azure 逻辑应用相同的订阅中使用。

>[!NOTE]
>缩放工具控制正在缩放的主机池的负载平衡模式。 它将其设置为高峰和非高峰时段的广度优先负载平衡。

## <a name="prerequisites"></a>先决条件

在开始设置缩放工具之前，请确保已准备好以下事项：

- [Windows 虚拟桌面租户和主机池](create-host-pools-arm-template.md)
- 在 Windows 虚拟桌面服务中配置和注册的会话主机池 VM
- 在 Azure 订阅上具有[参与者访问权限](../role-based-access-control/role-assignments-portal.md)的用户

用于部署该工具的计算机必须具有： 

- Windows PowerShell 5.1 或更高版本
- 微软 Az PowerShell 模块

如果你已经做好了一切准备，那就让我们开始吧。

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户

首先，您需要一个 Azure 自动化帐户来运行 PowerShell 运行簿。 以下是设置帐户的方式：

1. 以管理员身份打开 Windows PowerShell。
2. 运行以下 cmdlet 以登录到 Azure 帐户。

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >您的帐户必须具有要在 Azure 订阅上部署缩放工具的参与者权限。

3. 运行以下 cmdlet 以下载用于创建 Azure 自动化帐户的脚本：

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. 运行以下 cmdlet 以执行脚本并创建 Azure 自动化帐户：

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. cmdlet 的输出将包括一个 webhook URI。 请确保保留 URI 的记录，因为在为 Azure 逻辑应用设置执行计划时，会将其用作参数。

6. 设置 Azure 自动化帐户后，请登录到 Azure 订阅并进行检查，以确保 Azure 自动化帐户和相关 Runbook 已显示在指定的资源组中，如下图所示：

   ![显示新创建的自动化帐户和 Runbook 的 Azure 概览页的图像。](media/automation-account.png)

  要检查 Webhook 是否位于其应该的位置，请选择 Runbook 的名称。 接下来，转到 Runbook 的资源部分并选择**Webhook。**

## <a name="create-an-azure-automation-run-as-account"></a>创建 Azure 自动化作为帐户运行

现在，您已经拥有了 Azure 自动化帐户，还需要创建 Azure 自动化运行为帐户来访问 Azure 资源。

[Azure 自动化运行为帐户](../automation/manage-runas-account.md)提供用于使用 Azure cmdlet 管理 Azure 中的资源的身份验证。 创建"运行为"帐户时，它会在 Azure 活动目录中创建新的服务主体用户，并在订阅级别将参与者角色分配给服务主体用户，Azure 运行作为帐户是使用证书和服务主体名称，无需在凭据对象中存储用户名和密码。 要了解有关"作为身份验证运行"的更多内容，请参阅[限制"作为帐户运行"权限](../automation/manage-runas-account.md#limiting-run-as-account-permissions)。

任何作为订阅管理员角色的成员和订阅共同管理员的用户都可以按照下一节的说明创建"运行为"帐户。

要在 Azure 帐户中创建"运行"帐户，请执行如下设置：

1. 在 Azure 门户中，选择“所有服务”。**** 在资源列表中，输入并选择 **"自动化帐户**"。

2. 在 **"自动化帐户"** 页上，选择自动化帐户的名称。

3. 在窗口左侧的窗格中，在"帐户设置"部分下选择 **"以帐户身份运行**"。

4. 选择**Azure 以帐户身份运行**。 当"**添加 Azure 运行为帐户"** 窗格出现时，查看概览信息，然后选择 **"创建"** 以启动帐户创建过程。

5. 等待几分钟，Azure 创建"以运行为帐户"。" 您可以在"通知"下跟踪菜单中的创建进度。

6. 该过程完成后，它将在指定的自动化帐户中创建名为 AzureRunAsConnect 的资产。 连接资产包含应用程序 ID、租户 ID、订阅 ID 和证书指纹。 请记住应用程序 ID，因为稍后将使用它。

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>在 Windows 虚拟桌面中创建角色分配

接下来，您需要创建角色分配，以便 AzureRunAsConnect 可以与 Windows 虚拟桌面进行交互。 请确保使用 PowerShell 使用具有创建角色分配权限的帐户登录。

首先，下载并导入[Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)，以便如果您尚未在 PowerShell 会话中使用。 运行以下 PowerShell cmdlet 连接到 Windows 虚拟桌面，并显示你的租户。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

当您找到具有要缩放的主机池的租户时，请按照[创建 Azure 自动化帐户](#create-an-azure-automation-account)中的说明操作，并使用以下 cmdlet 中以前的 cmdlet 中的租户名称创建角色分配：

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>创建 Azure 逻辑应用和执行计划

最后，您需要创建 Azure 逻辑应用并为新的缩放工具设置执行计划。

1.  以管理员身份打开 Windows 电源外壳

2.  运行以下 cmdlet 以登录到 Azure 帐户。

     ```powershell
     Login-AzAccount
     ```

3. 运行以下 cmdlet 以在本地计算机上下载 createazurelogicapp.ps1 脚本文件。

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. 运行以下 cmdlet 以使用具有 RDS 所有者或 RDS 参与者权限的帐户登录到 Windows 虚拟桌面。

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 运行以下 PowerShell 脚本以创建 Azure 逻辑应用和执行计划。

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     运行脚本后，逻辑应用应显示在资源组中，如下图所示。

     ![示例 Azure 逻辑应用的概览页的图像。](media/logic-app.png)

要更改执行计划（如更改定期间隔或时区），请转到自动缩放计划程序并选择 **"编辑"** 以转到逻辑应用设计器。

![逻辑应用设计器的图像。 允许用户编辑重复次数和 Webhook 文件打开的定期和 Webhook 菜单。](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>管理缩放工具

现在，您已经创建了缩放工具，您可以访问其输出。 本节介绍您可能认为有用的一些功能。

### <a name="view-job-status"></a>查看作业状态

您可以查看所有 Runbook 作业的汇总状态，或在 Azure 门户中查看特定 Runbook 作业的更深入状态。

在所选自动化帐户的右侧"作业统计信息"下，您可以查看所有 Runbook 作业的摘要列表。 打开窗口左侧的**作业**页将显示当前作业状态、开始时间和完成时间。

![作业状态页的屏幕截图。](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>查看日志和缩放工具输出

您可以通过打开 Runbook 并选择作业的名称来查看横向扩展和扩展操作的日志。

导航到托管 Azure 自动化帐户的资源组中的 Runbook（默认名称为 WVDAutoScaleRunbook），然后选择 **"概述**"。 在概览页上，选择"最近作业"下的作业以查看其缩放工具输出，如下图所示。

![缩放工具的输出窗口的图像。](media/tool-output.png)

## <a name="report-issues"></a>报告问题

如果缩放工具遇到任何问题，可以在[RDS GitHub 页](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)中报告这些问题。
