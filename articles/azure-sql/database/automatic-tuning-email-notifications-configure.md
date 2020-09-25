---
title: 自动优化电子邮件通知操作指南
description: 为 Azure SQL 数据库自动查询优化启用电子邮件通知。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 06/03/2019
ms.openlocfilehash: 01daf0af039a00f6a81b850b07cfcf6d901a7732
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284629"
---
# <a name="email-notifications-for-automatic-tuning"></a>自动优化的电子邮件通知
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL 数据库优化建议由 Azure SQL 数据库 [自动优化](automatic-tuning-overview.md)生成。 此解决方案持续监视和分析数据库的工作负荷，为每个与索引创建、索引删除和查询执行计划优化相关的单独数据库提供自定义的优化建议。

可以在 [Azure 门户](database-advisor-find-recommendations-portal.md)中查看 Azure SQL 数据库自动优化建议，使用 [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) 调用进行检索，或使用 [t-sql](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) 和 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) 命令进行查看。 本文立足于使用 PowerShell 脚本检索自动优化建议。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>自动执行自动优化建议的电子邮件通知

以下解决方案自动发送包含自动优化建议的电子邮件通知。 所述解决方案包括使用 [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)自动执行 PowerShell 脚本以检索优化建议，以及使用 [Microsoft Flow](https://flow.microsoft.com) 自动计划电子邮件的发送作业。

## <a name="create-azure-automation-account"></a>创建 Azure 自动化帐户

要使用 Azure 自动化，第一步是创建自动化帐户并使用 Azure 资源配置该帐户，以用于 PowerShell 脚本的执行。 要了解 Azure 自动化及其功能的详细信息，请参阅 [Azure 自动化入门](https://docs.microsoft.com/azure/automation/automation-offering-get-started)。

按照以下步骤，通过从 Azure Marketplace 中选择和配置自动化应用的方法来创建 Azure 自动化帐户：

1. 登录到 Azure 门户。
1. 单击左上角的 "**+ 创建资源**"。
1. 搜索 "**自动化**" (按 enter) 。
1. 在搜索结果中单击 "自动化" 应用。

    ![添加 Azure 自动化](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. 在 "创建自动化帐户" 窗格内，单击 "**创建**"。
1. 填写所需的信息：输入此自动化帐户的名称，选择要用于 PowerShell 脚本执行的 Azure 订阅 ID 和 Azure 资源。
1. 对于 "**创建 Azure 运行方式帐户**" 选项，请选择 **"是** " 以配置在其下运行 PowerShell 脚本和 Azure 自动化帮助的帐户类型。 若要了解有关帐户类型的详细信息，请参阅 [运行方式帐户](https://docs.microsoft.com/azure/automation/automation-create-runas-account)。
1. 通过单击 " **创建**" 结束自动化帐户的创建。

> [!TIP]
> 创建自动化应用时，请完全按照输入内容记录 Azure 自动化帐户名称、订阅 ID 和资源（如复制粘贴到记事本）。 稍后需要使用此信息。

如果要为多个 Azure 订阅生成相同的自动化，则需要为其他订阅重复此过程。

## <a name="update-azure-automation-modules"></a>更新 Azure 自动化模块

用于检索自动优化建议的 PowerShell 脚本使用 [AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) 和 [AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) 命令，该命令适用于所需的 Azure 模块版本4及更高版本。

- 如果需要更新 Azure 模块，请参阅 [Azure 自动化中的 Az module 支持](../../automation/shared-resources/modules.md)。

## <a name="create-azure-automation-runbook"></a>创建 Azure 自动化 runbook

下一步是的 Azure 自动化中创建 Runbook，用于检索优化建议的 PowerShell 脚本也会驻留在 Azure 自动化中。

请按照以下步骤创建新的 Azure 自动化 Runbook：

1. 访问在上一步中创建的 Azure 自动化帐户。
1. 在 "自动化帐户" 窗格中，单击左侧的 "**runbook**" 菜单项，使用 PowerShell 脚本创建新的 Azure 自动化 runbook。 若要详细了解如何创建自动化 runbook，请参阅 [创建新的 runbook](../../automation/manage-runbooks.md#create-a-runbook)。
1. 若要添加新 runbook，请单击 "**+ 添加 runbook**" 菜单选项，然后单击 "**快速创建-创建新 runbook**"。
1. 在 "Runbook" 窗格中，在此示例中键入 runbook (的名称，使用 "**AutomaticTuningEmailAutomation**") ，选择 runbook 类型作为 **PowerShell** 并写入此 runbook 的说明以描述其用途。
1. 单击 " **创建** " 按钮以完成创建新的 runbook。

    ![添加 Azure 自动化 Runbook](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

请按照以下步骤在创建的 runbook 中加载 PowerShell 脚本：

1. 在 "**编辑 PowerShell Runbook**" 窗格中，在菜单树中选择 "**runbook**"，然后展开视图，直到在此示例中看到 Runbook (的名称 ) 。**AutomaticTuningEmailAutomation** 选择此 runbook。
1. 在 "编辑 PowerShell Runbook" 的第一行中 (从数字 1) 开始，复制-粘贴以下 PowerShell 脚本代码。 此 PowerShell 脚本按原样提供，可帮助你入门。 修改脚本以满足需求。

在提供的 PowerShell 脚本的标头中，需要使用 Azure 订阅 ID 替换 `<SUBSCRIPTION_ID_WITH_DATABASES>`。 要了解如何检索 Azure 订阅 ID，请参阅 [Getting your Azure Subscription GUID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/)（获取 Azure 订阅 GUID）。

如果有多个订阅，可以将它们以逗号分隔的形式添加到脚本标头中的 "$subscriptions" 属性。

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

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

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
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

单击右上角的 "**保存**" 按钮以保存脚本。 如果对脚本感到满意，请单击 "**发布**" 按钮发布此 runbook。

在主 runbook 窗格中，可以选择单击 "**启动**" 按钮来 **测试** 脚本。 单击 "**输出**" 可查看执行的脚本的结果。 此输出将是电子邮件的内容。 脚本的示例输出如以下屏幕截图中所示。

![使用 Azure 自动化运行视图自动优化建议](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

确保通过根据需求自定义 PowerShell 脚本来调整内容。

在上述步骤中，Azure 自动化中加载了用于检索自动优化建议的 PowerShell 脚本。 下一步是自动化和计划电子邮件发送作业。

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>使用 Microsoft Flow 自动化电子邮件作业

要完成作为最后一步的该解决方案，请在 Microsoft Flow 中创建包含三个操作（作业）的自动化流：

- "**Azure 自动化-创建作业**" –用于执行 PowerShell 脚本以检索 Azure 自动化 runbook 中的自动优化建议。
- "**Azure 自动化-获取作业输出**" –用于从已执行的 PowerShell 脚本检索输出。
- "**Office 365 Outlook –发送电子邮件**" –用于发送电子邮件。 电子邮件使用创建此流的个人的 Office 365 帐户发送。

要了解有关 Microsoft Flow 功能的详细信息，请参阅[开始使用 Microsoft Flow](https://docs.microsoft.com/flow/getting-started)。

此步骤的先决条件是注册 [Microsoft Flow](https://flow.microsoft.com) 帐户并登录。 进入解决方案后，请按照以下步骤设置新流****

1. 访问 "**我的流**" 菜单项。
1. 在 "我的流" 中，选择页面顶部的 "**+ 从空白位置创建**" 链接。
1. 单击页面底部的 "**搜索数百个连接器和触发器**" 链接。
1. 在搜索字段中，键入 "**定期**"，然后从搜索结果中选择 "**计划-定期**"，计划要运行的电子邮件发送作业。
1. 在“频率”字段的“定期”窗格中，选择执行此流的计划频率，如按每分钟、每小时、每天、每周等间隔自动发送电子邮件。

下一步是向新创建的定期流添加三个作业（创建、获取输出和发送电子邮件）。 要完成向流中添加所需作业，请执行以下步骤：

1. 创建操作以执行检索优化建议的 PowerShell 脚本

   - 选择 "**+ 新步骤**"，然后在 "定期流" 窗格中选择 "**添加操作**"。
   - 在搜索字段中键入 "**自动化**"，然后从搜索结果中选择 "**Azure 自动化-创建作业**"。
   - 在“创建作业”窗格中，配置作业属性。 对于此配置，需要之前在“自动化帐户”窗格上记录的 Azure 订阅 ID、资源组和自动化帐户的详细信息********。 要了解本部分提供选项的详细信息，请参阅 [Azure 自动化 - 创建作业](https://docs.microsoft.com/connectors/azureautomation/#create-job)。
   - 单击 "**保存流**" 完成创建此操作的操作。

2. 创建操作以从已执行的 PowerShell 脚本检索输出

   - 选择 "**+ 新步骤**"，然后在 "定期流" 窗格中选择 "**添加操作**"
   - 在搜索字段中键入 "**自动化**"，然后从搜索结果中选择 "**Azure 自动化-获取作业输出**"。 要了解本部分提供选项的详细信息，请参阅 [Azure 自动化 - 获取作业输出](https://docs.microsoft.com/connectors/azureautomation/#get-job-output)。
   - 在 "自动化帐户" 窗格) 中输入 (类似于创建以前的作业) 填充所需 (的字段。
   - 单击 "**作业 ID**" 字段内显示的 "**动态内容**" 菜单。 从该菜单中，选择 "**作业 ID**" 选项。
   - 单击 "**保存流**" 完成创建此操作的操作。

3. 使用 Office 365 集成创建用于发送电子邮件的操作

   - 选择 "**+ 新步骤**"，然后在 "定期流" 窗格中选择 "**添加操作**"。
   - 在搜索字段中，键入 "**发送电子邮件**"，然后从搜索结果中选择 "**Office 365 Outlook –发送电子邮件**"。
   - 在 "**目标**" 字段中，键入要向其发送通知电子邮件的电子邮件地址。
   - 在 "**主题**" 字段中键入电子邮件的主题，例如 "自动优化建议电子邮件通知"。
   - 单击 "**正文**" 字段内显示的 "**动态内容**" 菜单。 在此菜单内的 "**获取作业输出**" 下，选择 "**内容**"。
   - 单击 "**保存流**" 完成创建此操作的操作。

> [!TIP]
> 要将自动电子邮件发送给不同的收件人，请创建单独的流。 在这些附加的流中，更改 "收件人" 字段中的收件人电子邮件地址和 "主题" 字段中的电子邮件主题行。 在 Azure 自动化中通过自定义的 PowerShell 脚本创建新的 runbook (例如更改 Azure 订阅 ID) 可以进一步自定义自动方案，例如，例如通过电子邮件将单独的收件人用于单独订阅的自动优化建议。

上面总结了配置电子邮件发送作业工作流所需的步骤。 生成的包含三个操作的整个流如下图所示。

![查看自动优化电子邮件通知流](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

若要测试流，请单击 "流" 窗格中右上角的 "**立即运行**"。

从“流分析”窗格中，可以查看运行自动作业、显示已发送电子邮件通知成功与否的统计信息。

![运行流以用于自动优化电子邮件通知](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

"流分析" 窗格有助于监视作业执行是否成功，以及是否需要进行故障排除。  在进行故障排除的情况下，你可能还希望检查可通过 Azure 自动化应用程序访问的 PowerShell 脚本执行日志。

自动电子邮件的最终输出类似于生成和运行此解决方案后接收的以下电子邮件：

![从自动优化电子邮件通知中对电子邮件输出采样](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

可以通过调整 PowerShell 脚本来调整输出和自动电子邮件的格式以满足需求。

可进一步自定义解决方案，生成基于特定优化事件、发送给多个收件人或者有关多个订阅或数据库的电子邮件通知，具体取决于自定义方案。

## <a name="next-steps"></a>后续步骤

- 了解自动优化如何帮助提高数据库性能的详细信息，请参阅 [Azure SQL 数据库中的自动优化](automatic-tuning-overview.md)。
- 若要在 Azure SQL 数据库中启用自动优化以管理工作负载，请参阅[启用自动优化](automatic-tuning-enable.md)。
- 若要手动查看和应用自动优化建议，请参阅 [查找和应用性能建议](database-advisor-find-recommendations-portal.md)。
