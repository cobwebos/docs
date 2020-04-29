---
title: 管理 Azure 自动化数据
description: 本文包含管理 Azure 自动化环境的多个主题。  Azure 自动化目前包括数据保留和备份 Azure 自动化灾难恢复。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984651"
---
# <a name="managing-azure-automation-data"></a>管理 Azure 自动化数据

本文包含多个有关管理 Azure 自动化环境中的数据的主题。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="data-retention"></a>数据保留

删除 Azure 自动化中的某个资源时，会在永久删除之前保留一整天的时间进行审核。 在此期间，无法查看或使用该资源。 此策略也适用于属于已删除的自动化帐户的资源。

下表汇总了各种资源的保留策略。

| 数据 | 策略 |
|:--- |:--- |
| 帐户 |帐户将在用户删除30天后被永久删除。 |
| 资产 |在用户删除资产后的30天内，或在用户删除包含该资产的帐户后的30天后，将永久删除该资产。 |
| DSC 节点 |使用 Azure 门户或 Windows PowerShell 中的 AzAutomationDscNode cmdlet 从自动化帐户中[取消](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0)注册后，会从自动化帐户中永久删除 DSC 节点。 在用户删除保存节点的帐户30天后，也会永久删除该节点。 |
| 作业 |修改后将删除作业，并在修改后永久删除30天，例如在作业完成、停止或挂起后。 |
| 模块 |在用户删除模块30天后，或在用户删除保存该模块的帐户30天后，将永久删除该模块。 |
| 节点配置/MOF 文件 |生成新节点配置后，将永久删除旧节点配置。 |
| 节点报告 |为该节点生成新报表90天后，将永久删除该节点的节点。 |
| Runbook |在用户删除资源30天后，或在用户删除保存该资源的帐户30天后，将永久删除 runbook。 |

保留策略应用于所有用户，当前无法自定义。 但是，如果需要将数据保留更长时间，可以将[Azure 自动化作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

## <a name="data-backup"></a>数据备份

删除 Azure 中的某个自动化帐户时，该帐户中的所有对象都将被删除。 对象包括 runbook、模块、配置、设置、作业和资产。 删除帐户后，无法恢复这些帐户。 在删除自动化帐户之前，可以使用以下信息来备份该帐户的内容。

### <a name="runbooks"></a>Runbook

可以使用 Azure 门户或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet 将 Runbook 导出到脚本文件。 可以将这些脚本文件导入到另一个自动化帐户，如在[Azure Automation 中管理 runbook](manage-runbooks.md)中所述。

### <a name="integration-modules"></a>集成模块

无法从 Azure 自动化导出集成模块。 必须使其在自动化帐户外部可用。

### <a name="assets"></a>资产

无法导出 Azure 自动化资产：证书、连接、凭据、计划和变量。 相反，可以使用 Azure 门户和 Azure cmdlet 来记录这些资产的详细信息。 然后，使用这些详细信息来创建导入到另一个自动化帐户的 runbook 所使用的任何资产。

不能使用 cmdlet 检索已加密变量或凭据的密码字段的值。 如果不知道这些值，可以在 runbook 中检索这些值。 若要检索变量值，请参阅[Azure 自动化中的变量资产](shared-resources/variables.md)。 若要了解有关检索凭据值的详细信息，请参阅[Azure 自动化中的凭据资产](shared-resources/credentials.md)。

 ### <a name="dsc-configurations"></a>DSC 配置

你可以使用 Azure 门户或 Windows PowerShell 中的[AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) CMDLET 将 DSC 配置导出到脚本文件。 可以在另一个自动化帐户中导入并使用这些配置。

## <a name="geo-replication-in-azure-automation"></a>Azure 自动化中的异地复制

异地复制在 Azure 自动化帐户中是标准的。 设置帐户时选择主要区域。 内部自动化异地复制服务会自动将次要区域分配给帐户。 然后，该服务会将帐户数据从主要区域连续备份到次要区域。 可以在[业务连续性和灾难恢复（BCDR）](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)上找到主要区域和次要区域的完整列表： Azure 配对区域。 

自动化异地复制服务创建的备份是自动化资产、配置等的完整副本。 如果主要区域发生故障并丢失数据，则可以使用此备份。 如果主要区域的数据丢失，则可能会导致 Microsoft 尝试恢复它。 如果公司无法恢复主数据，则使用自动故障转移，并通过 Azure 订阅通知你这种情况。 

如果出现区域性故障，自动异地复制服务无法直接访问外部客户。 如果要在区域故障期间维护自动化配置和 runbook：

1. 选择要与主自动化帐户的地理区域配对的次要区域。

2. 在次要区域中创建自动化帐户。

3. 在主帐户中，将 runbook 导出为脚本文件。

4. 将 runbook 导入到次要区域中的自动化帐户。

## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure 自动化中安全资产的详细信息，请参阅[在 Azure 自动化中加密安全资产](automation-secure-asset-encryption.md)。

* 若要了解有关地域复制的详细信息，请参阅[创建和使用活动异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)。