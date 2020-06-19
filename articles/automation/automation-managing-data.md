---
title: 管理 Azure 自动化数据
description: 本文提供 Azure 自动化中的数据管理概念，包括数据保留和备份。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: de60ef31a39a698f9a797a5836546f9b75b67594
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835200"
---
# <a name="management-of-azure-automation-data"></a>管理 Azure 自动化数据

本文包含有关在 Azure 自动化环境管理数据的多个主题。

## <a name="data-retention"></a>数据保留

在删除 Azure 自动化中的某个资源时，该资源在被永久删除之前将保留若干天以供审核。 在此期间，无法查看或使用该资源。 此策略也适用于属于已删除的自动化帐户的资源。

下表汇总了各种资源的保留策略。

| 数据 | 策略 |
|:--- |:--- |
| 帐户 |在帐户被用户删除 30 天后将其永久删除。 |
| 资产 |在资产被用户删除 30 天后或者在包含该资产的帐户被用户删除 30 天后将其永久删除。 |
| DSC 节点 |在使用 Azure 门户或 Windows PowerShell 中的 [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) cmdlet 从自动化帐户中取消注册 DSC 节点 30 天后，将永久删除该节点。 在用户删除保存节点的帐户 30 天后，也会永久删除该节点。 |
| 作业 |作业在修改之后（例如在作业完成、停止或暂停之后）删除，并在 30 天后永久删除。 |
| 模块 |在模块被用户删除 30 天后或者在包含该模块的帐户被用户删除 30 天后将其永久删除。 |
| 节点配置/MOF 文件 |生成新节点配置 30 天后，将永久删除旧节点配置。 |
| 节点报告 |在生成该节点的新报告 90 天后永久删除节点报告。 |
| Runbook |在 runbook 被用户删除资源 30 天后或者在包含该资源的帐户被用户删除 30 天后将其永久删除。 |

保留策略应用于所有用户并且当前无法自定义。 但是，如果需要将数据保留更长时间，则可以[将 Azure 自动化作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

## <a name="data-backup"></a>数据备份

删除 Azure 中的某个自动化帐户时，该帐户中的所有对象都会删除。 对象包括 runbook、模块、配置、设置、作业和资产。 在删除帐户后，这些对象不可恢复。 在删除自动化帐户之前，可以参考以下信息来备份该帐户的内容。

### <a name="runbooks"></a>Runbook

可以使用 Azure 门户或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet 将 Runbook 导出到脚本文件。 可以将这些脚本文件导入到另一个自动化帐户中，如 [在 Azure 自动化中管理 runbook](manage-runbooks.md) 中所述。

### <a name="integration-modules"></a>集成模块

无法从 Azure 自动化导出集成模块。 必须使它们在自动化帐户外部可用。

### <a name="assets"></a>资产

无法导出 Azure 自动化资产：证书、连接、凭据、计划和变量。 可以改为使用 Azure 门户和 Azure cmdlet 来记录这些资产的详细信息。 然后使用这些详细信息创建导入到另一个自动化帐户中的 Runbook 使用的任何资产。

无法使用 cmdlet 检索已加密变量或凭据密码字段的值。 如果不知道这些值，可以在 runbook 中检索它们。 若要检索变量值，请参阅 [Azure 自动化中的变量资产](shared-resources/variables.md)。 若要了解有关检索凭据值的详细信息，请参阅 [Azure 自动化中的凭据资产](shared-resources/credentials.md)。

 ### <a name="dsc-configurations"></a>DSC 配置

可以使用 Azure 门户或 Windows PowerShell 中的 [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) cmdlet 将 DSC 配置导出到脚本文件。 可以在另一个自动化帐户中导入并使用这些配置。

## <a name="geo-replication-in-azure-automation"></a>Azure 自动化中的异地复制

异地复制在 Azure 自动化帐户中是标准的。 设置帐户时需选择主要区域。 内部自动化异地复制服务会自动将次要区域分配给帐户。 该服务随后会将帐户数据从主要区域连续备份到次要区域。 主要区域和次要区域的完整列表位于[业务连续性和灾难恢复 (BCDR)：Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 

自动化异地复制服务创建的备份是自动化资产、配置等的完整副本。 如果主要区域发生故障并丢失数据，则可以使用此备份。 如果主要区域丢失（这种情况很少见），Microsoft 将尝试恢复它。 如果公司无法恢复主要数据，则使用自动故障转移，并通过 Azure 订阅向你通知情况。 

如果出现区域故障，外部客户无法直接访问自动异地复制服务。 如果要在区域故障期间维护自动化配置和 runbook：

1. 选择要与主要自动化帐户的地理区域配对的次要区域。

2. 在次要区域中创建自动化帐户。

3. 在主要帐户中，将 runbook 导出为脚本文件。

4. 将 runbook 导入到次要区域中的自动化帐户。

## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure 自动化中的安全资产的详细信息，请参阅[加密 Azure 自动化中的安全资产](automation-secure-asset-encryption.md)。
* 若要了解有关地域复制的详细信息，请参阅[创建和使用活动异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)。