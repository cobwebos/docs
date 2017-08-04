---
title: "使用 Azure 网络观察程序故障排除功能监视 VPN 网关 | Microsoft 文档"
description: "本文介绍如何使用 Azure 自动化和网络观察程序诊断本地连接"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: 655469b88a77bcf54b775cbde991b8cba415c024
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---

# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>使用网络观察程序故障排除功能监视 VPN 网关

若要向客户提供可靠的服务，获取网络性能的深入见解至关重要。 因此，必须能够快速检测网络中断状态，并采取纠正措施来缓解中断状态。 在 Azure 自动化中，可以通过 Runbook 以编程方式实施和运行任务。 使用 Azure 自动化可以创建完美的脚本来执行连续、主动的网络监视和警报。

## <a name="scenario"></a>方案

下图中的方案是一个多层应用程序，其中使用 VPN 网关和隧道建立了本地连接。 确保 VPN 网关正常运行对于应用程序的性能至关重要。

可以创建一个包含脚本的 Runbook 来检查 VPN 隧道的连接状态，使用资源故障排除 API 来检查连接隧道的状态。 如果状态不正常，可向管理员发送电子邮件触发器。

![方案示例][scenario]

此方案将：

- 创建一个可以调用 `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet 来排查连接状态的 Runbook
- 将计划链接到 Runbook

## <a name="before-you-begin"></a>开始之前

在开始学习本方案之前，必须满足以下先决条件：

- 在 Azure 中有一个 Azure 自动化帐户。
- 必须在 Azure 自动化中配置一组凭据。 在 [Azure 自动化安全性](../automation/automation-security-overview.md)中了解详细信息。
- 在 Azure 自动化中定义有效的 SMTP 服务器（Office 365、本地电子邮件或其他服务器）和凭据
- 在 Azure 配置的虚拟网络网关。
- 用于存储日志的现有存储帐户。

> [!NOTE]
> 上图所示的基础结构用于演示目的，不是使用本文中的步骤创建的。

### <a name="create-the-runbook"></a>创建 Runbook

配置示例的第一个步骤是创建 Runbook。 本示例使用运行方式帐户。 若要了解运行方式帐户，请访问[使用 Azure 运行方式帐户进行 Runbook 身份验证](../automation/automation-sec-configure-azure-runas-account.md)

### <a name="step-1"></a>步骤 1

在 [Azure 门户](https://portal.azure.com)中导航到“Azure 自动化”，然后单击“Runbook”

![自动化帐户概述][1]

### <a name="step-2"></a>步骤 2

单击“添加 Runbook”启动 Runbook 创建过程。

![Runbook 边栏选项卡][2]

### <a name="step-3"></a>步骤 3

在“快速创建”下面，单击“创建新的 Runbook”创建 Runbook。

![添加 Runbook 边栏选项卡][3]

### <a name="step-4"></a>步骤 4

在此步骤中，我们将为 Runbook 命名，在本示例中命名为 **Get-VPNGatewayStatus**。 必须为 Runbook 指定一个描述性的名称，我们建议遵循正式的 PowerShell 命名标准来命名。 本示例中的 Runbook 类型为“PowerShell”，其他选项包括“图形”、“PowerShell 工作流”和“图形 PowerShell 工作流”。

![Runbook 边栏选项卡][4]

### <a name="step-5"></a>步骤 5

在此步骤中创建 Runbook。以下代码示例提供了本示例所需的所有代码。 需要将代码中包含 \<value\> 的项替换为订阅中的值。

使用以下代码，然后单击“保存”

```PowerShell
# Get credentials for Office 365 account
$MyCredential = "Office 365 account"
$Cred = Get-AutomationPSCredential -Name $MyCredential
$username = "<from email address>"

# Get the connection "AzureRunAsConnection "
$connectionName = "AzureRunAsConnection"
$servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
$subscriptionId = "<subscription id>"
"Logging in to Azure..."
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "<Azure Region>" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "<vpn connection name>" -ResourceGroupName "<resource group name>"
$sa = Get-AzureRmStorageAccount -Name "<storage account name>" -ResourceGroupName "<resource group name>" 
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)logs"


if($result.code -ne "Healthy")
    {
        $Body = "Connection for $($connection.name) is: $($result.code). View the logs at $($sa.PrimaryEndpoints.Blob)logs to learn more."
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To 'admin@contoso.com' `
        -Subject $subject `
        -Body $Body `
        -UseSsl `
        -Port 587 `
        -SmtpServer 'smtp.office365.com' `
        -From $username `
        -BodyAsHtml `
        -Credential $Cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>步骤 6

保存 Runbook 后，必须向它链接一个计划才能自动启动它。 若要启动链接过程，请单击“计划”。

![步骤 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>将计划链接到 Runbook

必须创建一个新计划。 单击“将计划链接到 Runbook”。

![步骤 7][7]

### <a name="step-1"></a>步骤 1

在“计划”边栏选项卡中，单击“创建新计划”

![步骤 8][8]

### <a name="step-2"></a>步骤 2

在“新建计划”边栏选项卡中填写计划信息。 以下列表提供了可设置的值：

- **名称** - 计划的友好名称。
- **说明** - 计划的说明。
- **开始时间** - 此值是日期、时间和时区的组合，构成计划触发时间。
- **重复周期** - 此值确定计划的重复方式。  有效值为“一次”或“定期”。
- **重复间隔** - 以小时、天、周或月为单位的计划重复间隔。
- **设置过期时间** - 该值确定计划是否会过期。 可设置为“是”或“否”。 如果选择“是”，则要提供有效的日期和时间。

> [!NOTE]
> 如果需要以高于每隔一小时的频率运行某个 Runbook，必须以不同的间隔创建多个计划（即，在小时后面指定 15、30 或 45 分钟）

![步骤 9][9]

### <a name="step-3"></a>步骤 3

单击“保存”，将计划保存到 Runbook。

![步骤 10][10]

## <a name="next-steps"></a>后续步骤

了解如何将网络观察程序故障排除功能与 Azure 自动化集成后，接下来请访问 [Create an alert triggered packet capture with Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md)（使用 Azure 网络观察程序创建警报触发的数据包捕获），了解如何根据 VM 警报触发数据包捕获。

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png

