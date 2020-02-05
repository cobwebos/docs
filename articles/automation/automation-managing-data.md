---
title: 管理 Azure 自动化数据
description: 本文包含管理 Azure 自动化环境的多个主题。  Azure 自动化目前包括数据保留和备份 Azure 自动化灾难恢复。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: daa5bab7c8d4cbe98ffe9a8a8a4b66da029fef5c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421892"
---
# <a name="managing-azure-automation-data"></a>管理 Azure 自动化数据
本文包含管理 Azure 自动化环境的多个主题。

## <a name="data-retention"></a>数据保留
在删除 Azure 自动化中的某个资源时，该资源在被永久删除之前将保留 90 天以供审核。  在此期间，无法查看或使用该资源。  此策略也适用于属于已删除的自动化帐户的资源。

Azure 自动化会自动删除并永久移除 90 天之前的作业。

下表汇总了各种资源的保留策略。

| 数据 | 策略 |
|:--- |:--- |
| 帐户 |在帐户被用户删除 90 天后将其永久移除。 |
| 资产 |在资产被用户删除 90 天后或者在包含该资产的帐户被用户删除 90 天后将其永久移除。 |
| 模块 |在模块被用户删除 90 天后或者在包含该模块的帐户被用户删除 90 天后将其永久移除。 |
| Runbook |在资源被用户删除 90 天后或者在包含该资源的帐户被用户删除 90 天后将其永久移除。 |
| 工作 |在上次修改 90 天后删除并永久移除。 这可能发生在作业已完成、已停止或已暂停之后。 |
| 节点配置/MOF 文件 |生成新节点配置 90 天后，将永久删除旧节点配置。 |
| DSC 节点 |在使用 Azure 门户或 Windows PowerShell 中的 [Unregister-AzureRMAutomationDscNode cmdlet](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) 从自动化帐户中取消注册节点 90 天后，将永久删除该节点。 在用户删除保存节点的帐户 90 天后，也会永久删除该节点。 |
| 节点报告 |在生成该节点的新报告 90 天后永久删除 |

保留策略应用于所有用户并且当前无法自定义。

但是，如果需要更长时间保留数据，可以将 runbook 作业日志转发到 Azure Monitor 日志。  有关详细信息，请参阅[将 Azure 自动化作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。   

## <a name="backing-up-azure-automation"></a>备份 Azure 自动化
删除 Microsoft Azure 中的某个自动化帐户时，该帐户中的所有对象都会被删除，包括 Runbook、模块、配置、设置、作业和资产。 在删除帐户后，这些对象不可恢复。  在删除自动化帐户之前，可以参考以下信息来备份该帐户的内容。 

### <a name="runbooks"></a>Runbook
可以使用 Azure 门户或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet 将 Runbook 导出到脚本文件。  可以根据[创建或导入 Runbook](/previous-versions/azure/dn643637(v=azure.100)) 中所述，将这些脚本文件导入另一个自动化帐户。

### <a name="integration-modules"></a>集成模块
无法从 Azure 自动化导出集成模块。  必须确保这些模块可在自动化帐户外部使用。

### <a name="assets"></a>资产
不能从 Azure 自动化导出[资产](/previous-versions/azure/dn939988(v=azure.100))。  使用 Azure 门户时，必须记下变量、凭据、证书、连接和计划的详细信息。  然后，必须手动创建导入到另一个自动化中的 Runbook 使用的任何资产。

但可以使用 [Azure cmdlet](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) 检索未加密资产的详细信息，然后保存这些资产供将来参考，或在另一个自动化帐户中创建等效的资产。

无法使用 cmdlet 检索已加密变量或凭据密码字段的值。  如果不知道这些值，可以使用 [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) 和 [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) 活动从 Runbook 中检索这些值。

无法从 Azure 自动化导出证书。  必须确保所有证书在 Azure 外部可用。

### <a name="dsc-configurations"></a>DSC 配置
可以使用 Azure 门户或 Windows PowerShell 中的 [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) cmdlet 将配置导出到脚本文件。 可以在另一个自动化帐户中导入并使用这些配置。

## <a name="geo-replication-in-azure-automation"></a>Azure 自动化中的异地复制
Azure 自动化帐户中标配的异地复制可将帐户数据备份到其他地理区域以实现冗余。 可以在设置帐户时选择主要区域，然后会自动向它分配次要区域。 从主要区域复制的辅助数据将持续更新，以防数据丢失。  

下表显示了可用的主要区域和次要区域配对。

| 主 | 辅助副本 |
| --- | --- |
| 美国中南部 |美国中北部 |
| 美国东部 2 |美国中部 |
| 西欧 |北欧 |
| 东南亚 |东亚 |
| 日本东部 |日本西部 |

如果主要区域发生数据丢失（这种情况很少见），Microsoft 将尝试恢复数据。 如果主数据无法恢复，系统将执行异地故障转移，并通过订阅通知受影响的客户。


