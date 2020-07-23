---
title: 使用 Azure 自动化来改变会话主机规模 - Azure
description: 如何利用 Azure 自动化自动改变 Windows 虚拟桌面会话主机规模。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f94852a99f0bc430ac193b9951de607cdd7fa933
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362537"
---
# <a name="scale-session-hosts-using-azure-automation"></a>使用 Azure 自动化来改变会话主机规模

>[!IMPORTANT]
>本教程的内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的 2019 年秋季版。

可以通过改变虚拟机 (VM) 规模来降低 Windows 虚拟桌面总部署成本。 这是指在非高峰使用时段关闭和取消分配会话主机 VM，并在高峰时段重新启用和重新分配。

本文介绍 Azure 自动化和 Azure 逻辑应用的缩放工具，该工具可在 Windows 虚拟桌面环境中自动改变会话主机虚拟机的规模。 若要了解如何使用缩放工具，请转至[先决条件](#prerequisites)。

## <a name="report-issues"></a>报告问题

关于缩放工具的问题反馈，当前是通过 GitHub 进行处理的，而不由 Microsoft 支持部门负责处理。 如有任何关于缩放工具的问题，可以通过在 [RDS GitHub 页](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)上，使用“4a-WVD-scaling-logicapps”标记提交 GitHub 问题，来反馈问题。

## <a name="how-the-scaling-tool-works"></a>缩放工具的工作原理

缩放工具为想要优化其会话主机 VM 成本的客户提供了低成本的自动化选项。

可以使用缩放工具执行以下操作：

- 根据高峰和非高峰营业时间，安排 VM 的启动和停止时间。
- 基于每个 CPU 核心的会话数量横向扩展 VM。
- 在非高峰时段横向缩减 VM 规模，仅让最小数量的会话主机 VM 保持运行。

缩放工具通过结合使用 Azure 自动化 PowerShell runbook、webhook 和 Azure 逻辑应用来发挥作用。 当该工具运行时，Azure 逻辑应用会调用 webhook 来启动 Azure 自动化 runbook。 然后，runbook 会创建一个作业。

在高峰使用时段，该作业会检查每个主机池当前正在运行的会话主机的当前会话数和 VM 容量。 它使用此信息，并基于为 createazurelogicapp.ps1 文件定义的 SessionThresholdPerCPU 参数，来计算正在运行的会话主机 VM 是否能够支持现有会话。 如果会话主机 VM 无法支持现有会话，该作业会在主机池中启动其他会话主机 VM。

>[!NOTE]
>SessionThresholdPerCPU 不会限制虚拟机上的会话数。 此参数仅确定需要在何时启动新 VM，以便对连接数实施负载平衡。 若要限制会话数，需要遵循 [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) 说明，相应配置 MaxSessionLimit 参数。

在非高峰使用时段，作业将根据 MinimumNumberOfRDSH 参数确定要关闭哪些会话主机 VM。 该作业会将会话主机 VM 设置为排出模式，以防止新会话连接到主机。 如果将 LimitSecondsToForceLogOffUser 参数设置为非零正值，该作业将通知当前所有已登录的用户保存其工作，等待到配置的时间量结束，然后强制用户注销。当会话主机 VM 上的所有用户会话均已注销后，该作业将关闭 VM。

如果将 LimitSecondsToForceLogOffUser 参数设置为零，该作业将允许指定的组策略中的会话配置设置处理用户会话的注销。 若要查看这些组策略，请转到“计算机配置” > “策略” > “管理模板” > “Windows 组件” > “终端服务” > “终端服务器” > “会话时间限制”。 如果某个会话主机 VM 上存在任何活动会话，该作业会使该会话主机 VM 保持运行。 如果没有活动会话，该作业将关闭会话主机 VM。

作业基于所设置的重复周期间隔定期运行。 可以根据 Windows 虚拟桌面环境的大小来更改此间隔，但请记住，启动和关闭虚拟机可能需要一些时间，所以应将延迟考虑在内。 建议将重复周期间隔设置为每 15 分钟一次。

然而，该工具还存在以下限制：

- 此解决方案仅适用于共用的会话主机 VM。
- 此解决方案可管理任何区域中的 VM，但只能在 Azure 自动化帐户和 Azure 逻辑应用所在的同一订阅中使用。

>[!NOTE]
>缩放工具控制它正在缩放的主机池的负载均衡模式。 它将该模式设置为在高峰和非高峰时段均实施广度优先负载均衡。

## <a name="prerequisites"></a>先决条件

开始设置缩放工具之前，请确保已准备好以下内容：

- 一个 [Windows 虚拟桌面租户和主机池](create-host-pools-arm-template.md)
- 已配置了 Windows 虚拟桌面服务并向其注册了的会话主机池 VM
- 对 Azure 订阅具有[参与者访问权限](../../role-based-access-control/role-assignments-portal.md)的用户

用于部署此工具的计算机必须具有：

- Windows PowerShell 5.1 或更高版本
- Microsoft Az PowerShell 模块

如果一切已准备就绪，现在就可以开始了。

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户

首先，你需要一个 Azure 自动化帐户来运行 PowerShell runbook。 下面是设置帐户的方法：

1. 以管理员身份打开 Windows PowerShell。
2. 运行以下 cmdlet 登录到 Azure 帐户。

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >你的帐户对要在其上部署缩放工具的 Azure 订阅必须具有参与者权限。

3. 运行以下 cmdlet，下载用于创建 Azure 自动化帐户的脚本：

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. 运行以下 cmdlet 以执行脚本并创建 Azure 自动化帐户：

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. 此 cmdlet 的输出包含一个 webhook URI。 请务必保留一份 URI 的记录，因为在为 Azure 逻辑应用设置执行计划时需要使用它作为参数。

6. 设置 Azure 自动化帐户后，登录到 Azure 订阅，并检查以确保 Azure 自动化帐户和相关 runbook 已显示在指定的资源组中，如下图所示：

> [!div class="mx-imgBorder"]
> ![Azure 概述页的图像，其中显示了新创建的自动化帐户和 runbook。](../media/automation-account.png)

  若要检查 webhook 是否位于其预期位置，请选择 runbook 的名称。 接下来，转到 runbook 的“资源”部分，选择“Webhook”。

## <a name="create-an-azure-automation-run-as-account"></a>创建 Azure 自动化运行方式帐户

现在已有 Azure 自动化帐户，还需要创建 Azure 自动化运行方式帐户，用于访问 Azure 资源。

[Azure 自动化运行方式帐户](../../automation/manage-runas-account.md)用于进行身份验证，使用户能够使用 Azure cmdlet 管理 Azure 中的资源。 当创建了一个运行方式帐户后，它会在 Azure Active Directory 中创建新的服务主体用户，并在订阅级别将参与者角色分配给服务主体用户，Azure 运行方式帐户是使用证书和服务主体名称安全进行身份验证的一种绝佳方式，无需在凭据对象中存储用户名和密码。 若要了解有关运行方式身份验证的详细信息，请参阅[限制运行方式帐户的权限](../../automation/manage-runas-account.md#limit-run-as-account-permissions)。

身份为订阅管理员角色成员以及订阅的共同管理员的任何用户均可以按照下一部分中的说明创建运行方式帐户。

在 Azure 帐户中创建运行方式帐户：

1. 在 Azure 门户中，选择“所有服务”。 在资源列表中，输入并选择“自动化帐户”。

2. 在“自动化帐户”页上选择自动化帐户的名称。

3. 在窗口左侧的窗格中，选择“帐户设置”部分下的“运行方式帐户”。

4. 选择“Azure 运行方式帐户”。 当出现“添加 Azure 运行方式帐户”窗格后，查看概述信息，然后选择“创建”以启动帐户创建过程。

5. 等待几分钟，让 Azure 完成运行方式帐户的创建。 可以在“通知”下的菜单中跟踪创建进度。

6. 创建过程完成后，它会在指定的自动化帐户中创建名为 AzureRunAsConnection 的资产。 该连接资产保存应用程序 ID、租户 ID、订阅 ID 和证书指纹。 请记住应用程序 ID，因为稍后会用到它。

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>在 Windows 虚拟桌面中创建角色分配

接下来，需要创建一个角色分配，以便 AzureRunAsConnection 可以与 Windows 虚拟桌面进行交互。 请确保使用 PowerShell 并使用有权创建角色分配的帐户登录。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 运行以下 PowerShell cmdlet 连接到 Windows 虚拟桌面，并显示你的租户。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

找到含有要缩放的主机池的租户后，按照[创建 Azure 自动化帐户](#create-an-azure-automation-account)中的说明进行操作，并在以下 cmdlet 中使用从上一个 cmdlet 中获取的租户名称创建角色分配：

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>创建 Azure 逻辑应用和执行计划

最后，需要创建 Azure 逻辑应用，并为新的缩放工具设置执行计划。

1.  以管理员身份打开 Windows PowerShell

2.  运行以下 cmdlet 登录到 Azure 帐户。

     ```powershell
     Login-AzAccount
     ```

3. 运行以下 cmdlet，将 createazurelogicapp.ps1 脚本文件下载到本地计算机上。

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. 运行以下 cmdlet，以使用具有 RDS 所有者或 RDS 参与者权限的帐户登录到 Windows 虚拟桌面。

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 运行以下 PowerShell 脚本以创建 Azure 逻辑应用和执行计划。

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id

     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id

     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location

     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName

     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
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

     运行该脚本后，资源组中应会显示逻辑应用，如下图所示。

     > [!div class="mx-imgBorder"]
     > ![示例 Azure 逻辑应用的“概述”页的图像。](../media/logic-app.png)

若要更改执行计划（例如更改重复周期间隔或时区），可转到自动缩放计划程序，在其中选择“编辑”以转入逻辑应用设计器。

> [!div class="mx-imgBorder"]
> ![“逻辑应用设计器”图像。 使用户能够编辑重复执行时间的“定期”和“Webhook”菜单以及 webhook 文件处于打开状态。](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>管理缩放工具

现在已创建缩放工具，可以访问其输出了。 本部分介绍可能对你有所帮助的一些功能。

### <a name="view-job-status"></a>查看作业状态

可以查看所有 runbook 作业的概述性状态或在 Azure 门户中深入了解特定 runbook 作业的状态和情况。

在所选的“自动化帐户”右侧，可在“作业统计信息”下查看所有 Runbook 作业的摘要列表。 在窗口左侧打开“作业”页后，其中会显示当前作业状态、开始时间和完成时间。

> [!div class="mx-imgBorder"]
> ![作业状态页的屏幕截图。](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>查看日志和缩放工具输出

可以通过打开 runbook 并选择作业名称来查看横向扩展和横向缩减操作的日志。

在托管 Azure 自动化帐户的资源组中，导航到 runbook（默认名称为 WVDAutoScaleRunbook），并选择“概述”。 在“概述”页上，选择“最近的作业”下的某个作业，并查看其缩放工具输出，如下图所示。

> [!div class="mx-imgBorder"]
> ![缩放工具的输出窗口的图像。](../media/tool-output.png)

