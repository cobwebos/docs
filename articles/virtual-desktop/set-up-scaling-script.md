---
title: 使用 Azure 自动化来改变会话主机规模 - Azure
description: 如何利用 Azure 自动化自动改变 Windows 虚拟桌面会话主机规模。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12a15ab1a4c7369c448e9f65862121b03ca05bba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078548"
---
# <a name="scale-session-hosts-using-azure-automation"></a>使用 Azure 自动化来改变会话主机规模

可以通过改变虚拟机 (VM) 规模来降低 Windows 虚拟桌面总部署成本。 这是指在非高峰使用时段关闭和取消分配会话主机 VM，并在高峰时段重新启用和重新分配。

在本文中，你将了解如何利用 Azure 自动化帐户和 Azure 逻辑应用生成的缩放工具，该工具可自动缩放 Windows 虚拟桌面环境中的会话主机 Vm。 若要了解如何使用缩放工具，请转至[先决条件](#prerequisites)。

## <a name="how-the-scaling-tool-works"></a>缩放工具的工作原理

缩放工具为想要优化其会话主机 VM 成本的客户提供了低成本的自动化选项。

可以使用缩放工具执行以下操作：

- 根据高峰和非高峰营业时间，安排 VM 的启动和停止时间。
- 基于每个 CPU 核心的会话数量横向扩展 VM。
- 在非高峰时段横向缩减 VM 规模，仅让最小数量的会话主机 VM 保持运行。

缩放工具结合使用 Azure 自动化帐户、PowerShell runbook、webhook 和 Azure 逻辑应用来发挥作用。 当该工具运行时，Azure 逻辑应用会调用 webhook 来启动 Azure 自动化 runbook。 然后，runbook 会创建一个作业。

在高峰使用时段，该作业会检查每个主机池当前正在运行的会话主机的当前会话数和 VM 容量。 它使用此信息来计算正在运行的会话主机 Vm 是否可支持基于为**CreateOrUpdateAzLogicApp.ps1**文件定义的*SessionThresholdPerCPU*参数的现有会话。 如果会话主机 VM 无法支持现有会话，该作业会在主机池中启动其他会话主机 VM。

>[!NOTE]
>SessionThresholdPerCPU 不会限制虚拟机上的会话数。 此参数仅确定需要在何时启动新 VM，以便对连接数实施负载平衡。 若要限制会话数，需要按照说明 [AzWvdHostPool](configure-host-pool-load-balancing.md#configure-breadth-first-load-balancing) 来相应地配置 *MaxSessionLimit* 参数。

在非高峰使用时间内，作业根据 *MinimumNumberOfRDSH* 参数确定应关闭多少个会话主机 vm。 如果将 *LimitSecondsToForceLogOffUser* 参数设置为非零正值，则作业会将会话主机 vm 设置为排出模式，以防新会话连接到主机。 然后，该作业通知所有当前已登录的用户是否保存其工作，等待配置的时间长度，然后强制用户注销。会话主机 VM 上的所有用户会话注销后，该作业将关闭 VM。 VM 关闭后，该作业将重置其会话主机排出模式。

>[!NOTE]
>如果手动将会话主机 VM 设置为排出模式，则该作业不会管理会话主机 VM。 如果会话主机 VM 正在运行并设置为排出模式，则将其视为不可用，这会使作业启动其他 Vm 来处理负载。 建议在将任何 Azure Vm 手动设置为排出模式之前，对其进行标记。 以后创建 Azure 逻辑应用计划程序时，可以用 *MaintenanceTagName* 参数命名标记。 标记将帮助你将这些 Vm 与缩放工具所管理的 Vm 区分开来。 设置维护标记还会阻止缩放工具更改 VM，直到删除标记。

如果将 *LimitSecondsToForceLogOffUser* 参数设置为零，则作业允许指定组策略中的会话配置设置处理用户会话的注销。 若要查看这些组策略，请访问**计算机配置**  >  **策略**  >  **管理模板**  >  **Windows 组件**  >  **远程桌面服务**  >  **远程桌面会话主机**  >  **会话时间限制**。 如果某个会话主机 VM 上存在任何活动会话，该作业会使该会话主机 VM 保持运行。 如果没有任何活动会话，则该作业将关闭会话主机 VM。

在任何时候，作业还会考虑主机池的 *MaxSessionLimit* ，以确定当前会话数是否超过最大容量的90%。 如果是，则该作业将启动其他会话主机 Vm。

作业基于所设置的重复周期间隔定期运行。 你可以根据 Windows 虚拟桌面环境的大小更改此间隔，但请记住，启动和关闭 Vm 可能需要一些时间，因此请记住延迟。 建议将重复周期间隔设置为每 15 分钟一次。

然而，该工具还存在以下限制：

- 此解决方案仅适用于共用的多会话会话主机 Vm。
- 此解决方案管理任何区域中的 Vm，但只能在与 Azure 自动化帐户和 Azure 逻辑应用相同的订阅中使用。
- Runbook 中的作业的最大运行时间为3小时。 如果启动或停止主机池中的 Vm 所用时间超过此时间，则作业将失败。 有关更多详细信息，请参阅 [共享资源](../automation/automation-runbook-execution.md#fair-share)。

>[!NOTE]
>缩放工具控制其当前正在缩放的主机池的负载平衡模式。 该工具使用广泛的负载均衡模式来实现高峰时间和非高峰时段。

## <a name="prerequisites"></a>先决条件

开始设置缩放工具之前，请确保已准备好以下内容：

- [Windows 虚拟桌面主机池](create-host-pools-azure-marketplace.md)
- 已配置了 Windows 虚拟桌面服务并向其注册了的会话主机池 VM
- 对 Azure 订阅具有[参与者访问权限](../role-based-access-control/role-assignments-portal.md)的用户

用于部署此工具的计算机必须具有：

- Windows PowerShell 5.1 或更高版本
- Microsoft Az PowerShell 模块

如果一切已准备就绪，现在就可以开始了。

## <a name="create-or-update-an-azure-automation-account"></a>创建或更新 Azure 自动化帐户

>[!NOTE]
>如果你已有一个 Azure 自动化帐户，其中包含运行较低版本缩放脚本的 runbook，则你只需按照以下说明进行操作，以确保更新。

首先，你需要一个 Azure 自动化帐户来运行 PowerShell runbook。 即使已有要用于设置 PowerShell runbook 的现有 Azure 自动化帐户，本部分介绍的过程也是有效的。 设置方式如下：

1. 打开 Windows PowerShell。

2. 运行以下 cmdlet 登录到 Azure 帐户。

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >你的帐户必须对要在其中部署缩放工具的 Azure 订阅具有参与者权限。

3. 运行以下 cmdlet，下载用于创建 Azure 自动化帐户的脚本：

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. 运行以下 cmdlet 以执行脚本并创建 Azure 自动化帐户。 您可以填写参数的值或注释它们以使用其默认值。

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "UseARMAPI"             = $true
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. 此 cmdlet 的输出包含一个 webhook URI。 请确保保留 URI 记录，因为当你为 Azure 逻辑应用设置执行计划时，将使用该 URI 作为参数。

6. 如果为 Log Analytics 指定了参数 **WorkspaceName** ，则该 cmdlet 的输出还将包含 Log Analytics 的工作区 ID 和主键。 请确保记住 URI，因为当你为 Azure 逻辑应用设置执行计划时，你将需要再次将其用作参数。

7. 设置 Azure 自动化帐户后，登录到 Azure 订阅，并检查以确保 Azure 自动化帐户和相关 runbook 已显示在指定的资源组中，如下图所示：

    >[!div class="mx-imgBorder"]
    >![显示新创建的 Azure 自动化帐户和 runbook 的 Azure 概述页的图像。](media/automation-account.png)

    若要检查 webhook 是否位于其预期位置，请选择 runbook 的名称。 接下来，转到 runbook 的“资源”部分，选择“Webhook”。

## <a name="create-an-azure-automation-run-as-account"></a>创建 Azure 自动化运行方式帐户

使用 Azure 自动化帐户后，还需要创建 Azure 自动化运行方式帐户（如果尚未安装）。 此帐户将允许此工具访问你的 Azure 资源。

[Azure 自动化运行方式帐户](../automation/manage-runas-account.md)为使用 azure Cmdlet 管理 azure 中的资源提供身份验证。 创建运行方式帐户时，它会在 Azure Active Directory 中创建新的服务主体用户，并在订阅级别将参与者角色分配给服务主体用户。 Azure 运行方式帐户是使用证书和服务主体名称安全进行身份验证的一种绝佳方式，无需在凭据对象中存储用户名和密码。 若要了解有关运行方式帐户身份验证的详细信息，请参阅 [限制运行方式帐户权限](../automation/manage-runas-account.md#limit-run-as-account-permissions)。

如果任何用户是订阅管理员角色的成员，并且共同管理员订阅，则可以创建运行方式帐户。

在 Azure 自动化帐户中创建运行方式帐户：

1. 在 Azure 门户中，选择“所有服务”。 在资源列表中，输入并选择 " **自动化帐户**"。

2. 在 " **自动化帐户** " 页上，选择 Azure 自动化帐户的名称。

3. 在窗口左侧的窗格中，选择 "**帐户设置**" 部分下的 "**运行方式帐户**"。

4. 选择 " **Azure 运行方式帐户**"。 当 " **添加 Azure 运行方式帐户** " 窗格出现时，查看概述信息，然后选择 " **创建** " 以启动帐户创建过程。

5. 等待几分钟，让 Azure 完成运行方式帐户的创建。 可以在“通知”下的菜单中跟踪创建进度。

6. 完成此过程后，它将在指定的 Azure 自动化帐户中创建名为 **AzureRunAsConnection** 的资产。 选择 " **Azure 运行方式帐户**"。 该连接资产保存应用程序 ID、租户 ID、订阅 ID 和证书指纹。 您还可以在 " **连接** " 页上找到相同的信息。 若要切换到此页，请在窗口左侧的窗格中，选择 "**共享资源**" 部分下的 "**连接**"，然后单击名为**AzureRunAsConnection**的连接资产。

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>创建 Azure 逻辑应用和执行计划

最后，需要创建 Azure 逻辑应用，并为新的缩放工具设置执行计划。 首先，下载并导入要在 PowerShell 会话中使用的 [桌面虚拟化 powershell 模块](powershell-module.md) （如果尚未这样做）。

1. 打开 Windows PowerShell。

2. 运行以下 cmdlet 登录到 Azure 帐户。

    ```powershell
    Login-AzAccount
    ```

3. 运行以下 cmdlet，下载用于创建 Azure 逻辑应用的脚本。

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. 运行以下 PowerShell 脚本，为主机池创建 Azure 逻辑应用和执行计划

    >[!NOTE]
    >需要为要自动缩放的每个主机池运行此脚本，但只需要一个 Azure 自动化帐户。

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $WVDHostPool = Get-AzResource -ResourceType "Microsoft.DesktopVirtualization/hostpools" | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURIARMBased' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "UseARMAPI"                     = $true
         "HostPoolName"                  = $WVDHostPool.Name
         "HostPoolResourceGroupName"     = $WVDHostPool.ResourceGroupName           # Optional. Default: same as ResourceGroupName param value
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    运行该脚本后，Azure 逻辑应用应出现在资源组中，如下图所示。

    >[!div class="mx-imgBorder"]
    >![示例 Azure 逻辑应用的“概述”页的图像。](media/logic-app.png)

    若要更改执行计划（例如更改定期间隔或时区），请参阅 Azure 逻辑应用自动缩放计划程序，并选择 " **编辑** " 以进入 Azure 逻辑应用设计器。

    >[!div class="mx-imgBorder"]
    >![Azure 逻辑应用设计器的图像。 定期和 webhook 菜单允许用户编辑重复执行时间，webhook 文件处于打开状态。](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>管理缩放工具

现在已创建缩放工具，可以访问其输出了。 本部分介绍可能对你有所帮助的一些功能。

### <a name="view-job-status"></a>查看作业状态

可以查看所有 runbook 作业的概述性状态或在 Azure 门户中深入了解特定 runbook 作业的状态和情况。

在选定的 Azure 自动化帐户的右侧，在 "作业统计信息" 下，你可以查看所有 runbook 作业的摘要列表。 在窗口左侧打开“作业”页后，其中会显示当前作业状态、开始时间和完成时间。

>[!div class="mx-imgBorder"]
>![作业状态页的屏幕截图。](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>查看日志和缩放工具输出

可以通过打开 runbook 并选择作业来查看扩展和扩展操作的日志。

导航到托管 Azure Automation 帐户的资源组中的 runbook，然后选择 " **概述**"。 在 "概述" 页上，选择 " **最近的作业** " 下的作业以查看其缩放工具输出，如下图所示。

>[!div class="mx-imgBorder"]
>![缩放工具的输出窗口的图像。](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>检查 runbook 脚本版本号

可以通过在 Azure 自动化帐户中打开 runbook 文件并选择 " **查看**" 来检查正在使用的 runbook 脚本的版本。 Runbook 的脚本将出现在屏幕的右侧。 在脚本中，将在部分下的格式中看到版本号 `v#.#.#` `SYNOPSIS` 。 可在 [此处](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/ARM_based/basicScale.ps1#L1)找到最新的版本号。 如果未在 runbook 脚本中看到版本号，则表示你运行的是早期版本的脚本，应立即更新。 如果需要更新 runbook 脚本，请按照 [创建或更新 Azure 自动化帐户](#create-or-update-an-azure-automation-account)中的说明进行操作。

### <a name="reporting-issues"></a>报告问题

报告问题时，需要提供以下信息来帮助我们进行故障排除：

- 作业中引起问题的 " **所有日志** " 选项卡上的完整日志。 若要了解如何获取日志，请按照 [查看日志和缩放工具输出](#view-logs-and-scaling-tool-output)中的说明进行操作。 如果日志中有任何敏感信息或私有信息，可以将其删除，然后将问题提交给我们。

- 正在使用的 runbook 脚本的版本。 若要了解如何获取版本号，请参阅 [检查 runbook 脚本版本号](#check-the-runbook-script-version-number)

- Azure 自动化帐户中安装的以下每个 PowerShell 模块的版本号。 若要查找这些模块，请打开 Azure Automation 帐户，在窗口左侧窗格中的 "**共享资源**" 部分下选择 "**模块**"，然后搜索模块的名称。
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Az.DesktopVirtualization

- [运行方式帐户](#create-an-azure-automation-run-as-account)的过期日期。 若要找到此项，请打开 Azure 自动化帐户，然后在窗口左侧的窗格中选择 "**帐户设置**" 下的 "**运行方式帐户**"。 到期日期应为 **Azure 运行方式帐户**。

### <a name="log-analytics"></a>Log Analytics

如果决定使用 Log Analytics，则可以在 Log Analytics 工作区的 "**日志**" 视图中的 "**自**定义日志" 下查看名为**WVDTenantScale_CL**的自定义日志中的所有日志数据。 我们列出了一些可能有用的示例查询。

- 若要查看主机池的所有日志，请输入以下查询

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 若要查看主机池中当前正在运行的会话主机 Vm 和活动用户会话的总数，请输入以下查询

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 若要查看主机池中所有会话主机 Vm 的状态，请输入以下查询

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 若要查看任何错误和警告，请输入以下查询

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>报告问题

缩放工具的问题报告当前正在由 Microsoft 支持部门处理。 发出问题报告时，请确保按照 [报告问题](#reporting-issues)中的说明进行操作。 如果你有关于该工具或要请求新功能的反馈，请在 [RDS GitHub 页](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool)上打开一个标有 "4-WVD" 的 GitHub 问题。
