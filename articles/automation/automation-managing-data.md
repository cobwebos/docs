---
title: 管理 Azure 自动化数据
description: 本文包含管理 Azure 自动化环境的多个主题。  Azure 自动化目前包括数据保留和备份 Azure 自动化灾难恢复。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984651"
---
# <a name="managing-azure-automation-data"></a>管理 Azure 自动化数据

本文包含多个主题，用于在 Azure 自动化环境中管理数据。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="data-retention"></a>数据保留

删除 Azure 自动化中的资源时，在永久删除之前，资源将保留数天以进行审核。 在此期间，无法查看或使用该资源。 此策略也适用于属于已删除的自动化帐户的资源。

下表汇总了各种资源的保留策略。

| 数据 | 策略 |
|:--- |:--- |
| 帐户 |帐户在用户删除帐户 30 天后被永久删除。 |
| 资产 |在用户删除资产 30 天后或用户删除持有该资产的帐户 30 天后，将永久删除资产。 |
| DSC 节点 |使用 Azure 门户或 Windows PowerShell 中的["取消注册-AzAutomationDnode"cmdlet](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0)从自动化帐户注销 30 天后，将永久删除 DSC 节点。 在用户删除保存该节点的帐户 30 天后，节点也会被永久删除。 |
| 作业 |修改后 30 天内删除和永久删除作业，例如，作业完成后，该作业将停止或挂起。 |
| 模块 |模块在用户删除该模块 30 天后或用户删除持有该模块的帐户 30 天后被永久删除。 |
| 节点配置/MOF 文件 |生成新节点配置 30 天后，将永久删除旧节点配置。 |
| 节点报告 |节点报告在生成该节点的新报告 90 天后被永久删除。 |
| Runbook |Runbook 在用户删除资源 30 天后或用户删除保存资源的帐户 30 天后被永久删除。 |

保留策略适用于所有用户，当前无法自定义。 但是，如果需要将数据保留更长时间，可以将[Azure 自动化作业数据转发到 Azure 监视器日志](automation-manage-send-joblogs-log-analytics.md)。

## <a name="data-backup"></a>数据备份

删除 Azure 中的自动化帐户时，该帐户中的所有对象都将被删除。 这些对象包括 Runbook、模块、配置、设置、作业和资产。 删除帐户后无法恢复它们。 在删除自动化帐户之前，可以使用以下信息备份其内容。

### <a name="runbooks"></a>Runbook

可以使用 Azure 门户或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet 将 Runbook 导出到脚本文件。 您可以将这些脚本文件导入到另一个自动化帐户中，如[Azure 自动化 中的管理 Runbook 中](manage-runbooks.md)所述。

### <a name="integration-modules"></a>集成模块

无法从 Azure 自动化导出集成模块。 您必须在自动化帐户之外提供它们。

### <a name="assets"></a>资产

无法导出 Azure 自动化资产：证书、连接、凭据、计划和变量。 相反，您可以使用 Azure 门户和 Azure cmdlet 来记录这些资产的详细信息。 然后使用这些详细信息创建您导入到另一个自动化帐户的 Runbook 使用的任何资产。

无法使用 cmdlet 检索加密变量或凭据的密码字段的值。 如果您不知道这些值，可以在 Runbook 中检索它们。 有关检索变量值，请参阅[Azure 自动化 中的变量资产](shared-resources/variables.md)。 要了解有关检索凭据值的更多信息，请参阅[Azure 自动化 中的凭据资产](shared-resources/credentials.md)。

 ### <a name="dsc-configurations"></a>DSC 配置

您可以使用 Azure 门户或 Windows PowerShell 中的[导出-AzAutomationDsc配置](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
)cmdlet 将 DSC 配置导出到脚本文件。 您可以在另一个自动化帐户中导入和使用这些配置。

## <a name="geo-replication-in-azure-automation"></a>Azure 自动化中的异地复制

异地复制是 Azure 自动化帐户中的标准配置。 设置帐户时，您可以选择主要区域。 内部自动化异地复制服务自动将辅助区域分配给帐户。 然后，该服务会持续将帐户数据从主区域备份到辅助区域。 主区域和辅助区域的完整列表可在[业务连续性和灾难恢复 （BCDR） 中找到：Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 

由自动化异地复制服务创建的备份是自动化资产、配置等的完整副本。 如果主区域出现故障并丢失数据，可以使用此备份。 如果主区域的数据丢失，Microsoft 会尝试恢复它。 如果公司无法恢复主数据，则它使用自动故障转移，并通过 Azure 订阅通知您情况。 

如果出现区域故障，则外部客户无法直接访问自动化异地复制服务。 如果要在区域故障期间维护自动化配置和运行簿：

1. 选择要与主自动化帐户的地理区域配对的辅助区域。

2. 在辅助区域中创建自动化帐户。

3. 在主帐户中，将 Runbook 导出为脚本文件。

4. 将 Runbook 导入辅助区域中的自动化帐户。

## <a name="next-steps"></a>后续步骤

* 要了解有关 Azure 自动化中安全资产的更多信息，请参阅[在 Azure 自动化 中加密安全资产](automation-secure-asset-encryption.md)。

* 要了解有关异地复制的更多，请参阅[创建和使用活动异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)。