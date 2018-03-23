---
title: 自动优化电子邮件通知操作指南 - Azure SQL 数据库 | Microsoft Docs
description: Azure SQL 数据库可分析 SQL 查询并自动适应用户工作负荷。
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: a2799e45fbb54531289a89082f13e5ce0856c376
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="email-notifications-for-automatic-tuning"></a>自动优化的电子邮件通知

SQL 数据库优化建议由 Azure SQL 数据库[自动优化](sql-database-automatic-tuning.md)生成。 此解决方案持续监视和分析 SQL 数据库的工作负载，为与索引创建、索引删除和查询执行计划优化相关的每个数据库提供自定义优化建议。

SQL 数据库自动优化建议可在 [Azure 门户](sql-database-advisor-portal.md)中查看，使用 [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) 调用或通过 [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) 和 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction) 命令可以进行检索。 本文立足于使用 PowerShell 脚本检索自动优化建议。

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>自动发送有关自动优化建议的电子邮件通知

以下解决方案可自动发送包含自动优化建议的电子邮件通知。 所述解决方案包括使用 [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)自动执行 PowerShell 脚本以检索优化建议，以及使用 [Microsoft Flow](https://flow.microsoft.com) 自动计划电子邮件的发送作业。

## <a name="create-azure-automation-account"></a>创建 Azure 自动化帐户

要使用 Azure 自动化，第一步是创建自动化帐户并使用 Azure 资源配置该帐户，以用于 PowerShell 脚本的执行。 要了解 Azure 自动化及其功能的详细信息，请参阅 [Azure 自动化入门](https://docs.microsoft.com/azure/automation/automation-offering-get-started)。

请通过从 Marketplace 选择和配置自动化应用的方法，执行以下步骤来创建 Azure 自动化帐户：

- 登录到 Azure 门户
- 单击左上角的“+ 创建资源”
- 搜索“自动化”（按 Enter）。
- 单击搜索结果中的自动化应用

![添加 Azure 自动化](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- 进入“创建自动化帐户”窗格后，单击“创建”
- 填充所需信息：输入此自动化帐户的名称，选择要用于 PowerShell 脚本执行的 Azure 订阅 ID 和 Azure 资源
- 对于“创建 Azure 运行方式帐户”选项，选择“是”以配置 PowerShell 脚本借助 Azure 自动化来运行所使用的帐户的类型。 要了解有关帐户类型的详细信息，请参阅[运行方式帐户](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- 通过单击“创建”确定自动化帐户的创建

> [!TIP]
> 创建自动化应用时，请完全按照输入内容记录 Azure 自动化帐户名称、订阅 ID 和资源（如复制粘贴到记事本）。 稍后需要使用此信息。
>

如果要为多个 Azure 订阅生成相同的自动化，则需要为其他订阅重复此过程。

## <a name="update-azure-automation-modules"></a>更新 Azure 自动化模块

检索自动优化建议的 PowerShell 脚本使用 [Get-azurermresource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) 和 [Get AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) 命令，为此需要将 Azure 模块更新到版本 4 及更高版本。

请按照以下步骤更新 Azure PowerShell 模块：

- 访问“自动化应用”窗格，并选择左侧菜单上的“模块”（向下滚动，因为此菜单项位于“共享资源”下）。
- 在“模块”窗格中，单击顶部的“更新 Azure 模块”，然后等待，直到显示“Azure 模块已更新”的消息。 此过程可能需要几分钟才能完成。

![更新 Azure 自动化模块](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

AzureRM.Resources 和 AzureRM.Sql 模块的版本需为版本 4 和更高版本。

## <a name="create-azure-automation-runbook"></a>创建 Azure 自动化 Runbook

下一步是的 Azure 自动化中创建 Runbook，用于检索优化建议的 PowerShell 脚本也会驻留在 Azure 自动化中。

请按照以下步骤创建新的 Azure 自动化 Runbook：

- 访问上一步中创建的 Azure 自动化帐户
- 进入“自动化帐户”窗格后，单击左侧的“Runbook”菜单项，使用 PowerShell 脚本创建新的 Azure 自动化 runbook。 要了解有关创建自动化 runbook 的详细信息，请参阅[创建新的 runbook](../automation/automation-creating-importing-runbook.md)。
- 要添加新的 runbook，请单击“+ 添加 runbook”菜单选项，然后单击“快速创建 - 创建新的 runbook”。
- 在“Runbook”窗格中，键入 runbook 的名称（在本示例中，使用“AutomaticTuningEmailAutomation”），选择 runbook 的类型为“PowerShell”并写入此 runbook 的说明，描述其用途。
- 单击“创建”按钮，完成创建新的 runbook

![添加 Azure 自动化 Runbook](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

请按照以下步骤在创建的 runbook 中加载 PowerShell 脚本：

- 在“编辑 PowerShell Runbook”窗格中，选择菜单树上的“RUNBOOKS”并展开视图，直到看到 runbook 的名称（此示例中为“AutomaticTuningEmailAutomation”）。 选择此 runbook。
- 在“编辑 PowerShell Runbook”的第一行（以数字 1 开头），复制粘贴以下 PowerShell 脚本代码。 此 PowerShell 脚本按原样提供，可帮助你入门。 修改脚本以满足需求。

在提供的 PowerShell 脚本的标头中，需要使用 Azure 订阅 ID 替换 `<SUBSCRIPTION_ID_WITH_DATABASES>`。 要了解如何检索 Azure 订阅 ID，请参阅 [Getting your Azure Subscription GUID](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/)（获取 Azure 订阅 GUID）。

如果有多个订阅，则可将它们以逗号分隔的形式添加到脚本标头中的“$subscriptions”属性。

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

单击右上角的“保存”按钮保存脚本。 如果对脚本满意，单击“发布”按钮发布此 runbook。 

在“主 runbook”窗格中，可选择单击“启动”按钮来测试脚本。 单击“输出”，查看已执行脚本的结果。 此输出将是电子邮件的内容。 脚本的示例输出如以下屏幕截图中所示。

![使用 Azure 自动化运行视图自动优化建议](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

确保通过根据需求自定义 PowerShell 脚本来调整内容。

通过上述步骤，Azure 自动化中已加载用于检索自动优化建议的 PowerShell 脚本。 下一步是自动化和计划电子邮件发送作业。

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>使用 Microsoft Flow 自动化电子邮件作业

要完成作为最后一步的该解决方案，请在 Microsoft Flow 中创建包含三个操作（作业）的自动化流： 

1. “Azure 自动化 - 创建作业”- 用于执行 PowerShell 脚本以检索 Azure 自动化 runbook 中的自动优化建议
2. “Azure 自动化 - 获取作业输出”- 用于检索已执行的 PowerShell 脚本的输出
3. “Office 365 Outlook - 发送电子邮件”- 用于发送电子邮件。 电子邮件使用创建此流的个人的 Office 365 帐户发送。

要了解有关 Microsoft Flow 功能的详细信息，请参阅[开始使用 Microsoft Flow](https://docs.microsoft.com/flow/getting-started)。

此步骤的先决条件是注册 [Microsoft Flow](https://flow.microsoft.com) 帐户并登录。 进入解决方案后，请按照以下步骤设置新流

- 访问“我的流”菜单项
- 在“我的流”中，选择页面顶部的“+ 从空白创建”链接
- 单击页面底部的“搜索数百个连接器和触发器”链接
- 在“搜索”字段中键入“定期”，然后从搜索结果中选择“计划 - 定期”，计划要运行的电子邮件发送作业。
- 在“频率”字段的“定期”窗格中，选择执行此流的计划频率，如按每分钟、每小时、每天、每周等间隔自动发送电子邮件。

下一步是向新创建的定期流添加三个作业（创建、获取输出和发送电子邮件）。 要完成向流中添加所需作业，请执行以下步骤：

1. 创建操作以执行检索优化建议的 PowerShell 脚本
- 在“定期流”窗格中选择“+ 新建步骤”，然后选择“添加操作”
- 在“搜索”字段中键入“自动化”，然后从搜索结果中选择“Azure 自动化 - 创建作业”
- 在“创建作业”窗格中，配置作业属性。 对于此配置，需要之前在“自动化帐户”窗格上记录的 Azure 订阅 ID、资源组和自动化帐户的详细信息。 要了解本部分提供选项的详细信息，请参阅 [Azure 自动化 - 创建作业](https://docs.microsoft.com/connectors/azureautomation/#Create_job)。
- 单击“保存流”完成创建此操作

2. 创建从已执行的 PowerShell 脚本检索输出的操作
- 在“定期流”窗格中选择“+ 新建步骤”，然后选择“添加操作”
- 在“搜索”字段中键入“自动化”，然后从搜索结果中选择“Azure 自动化 - 获取作业输出” 要了解本部分提供选项的详细信息，请参阅 [Azure 自动化 - 获取作业输出](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output)。
- 填充所需字段（类似于创建上一个作业）- 填充 Azure 订阅 ID、资源组和自动化帐户（与“自动化帐户”窗格中输入的内容一样）
- 单击“作业 ID”字段内部，以便显示“动态内容”菜单。 从此菜单中选择“作业 ID”选项。
- 单击“保存流”完成创建此操作

3. 创建使用 Office 365 集成发送电子邮件的操作
- 在“定期流”窗格中选择“+ 新建步骤”，然后选择“添加操作”
- 在“搜索”字段中键入“发送电子邮件”，然后从搜索结果中选择“Office 365 Outlook - 发送电子邮件”
- 在“收件人”字段中，键入需要发送通知电子邮件的电子邮件地址
- 在“主题”字段中键入电子邮件的主题，例如“自动优化建议电子邮件通知”
- 单击“正文”字段内部，以便显示“动态内容”菜单。 在此菜单中的“获取作业输出”下，选择“内容” 
- 单击“保存流”完成创建此操作

> [!TIP]
> 要将自动电子邮件发送给不同的收件人，请创建单独的流。 在这些附加流中，更改“收件人”字段中的收件人电子邮件地址，以及“主题”字段中的电子邮件主题行。 使用自定义 PowerShell 脚本在 Azure 自动化中创建新的 runbook（例如更改 Azure 订阅 ID）可进一步自定义自动化方案，例如向单独的收件人发送有关单独订阅的自动优化建议的电子邮件。
>

上面总结了配置电子邮件发送作业工作流所需的步骤。 生成的包含三个操作的整个流如下图所示。

![查看自动优化电子邮件通知流](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

要测试流，请单击“流”窗格中右上角的“立即运行”。

从“流分析”窗格中，可以查看运行自动作业、显示已发送电子邮件通知成功与否的统计信息。

![运行流以用于自动优化电子邮件通知](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

流分析对监视作业执行是否成功很有帮助，对故障排除也很有帮助（如果需要）。  对于故障排除，还可能需要检查可通过 Azure 自动化应用访问的 PowerShell 脚本执行日志。

自动电子邮件的最终输出类似于生成和运行此解决方案后接收的以下电子邮件：

![从自动优化电子邮件通知中对电子邮件输出采样](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

可以通过调整 PowerShell 脚本来调整输出和自动电子邮件的格式以满足需求。

可进一步自定义解决方案，生成基于特定优化事件、发送给多个收件人或者有关多个订阅或数据库的电子邮件通知，具体取决于自定义方案。 

## <a name="next-steps"></a>后续步骤

- 了解自动优化如何帮助提高数据库性能的详细信息，请参阅 [Azure SQL 数据库中的自动优化](sql-database-automatic-tuning.md)。
- 若要在 Azure SQL 数据库中启用自动优化以管理工作负载，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要手动查看并应用自动优化建议，请参阅[查找并应用性能建议](sql-database-advisor-portal.md)。
