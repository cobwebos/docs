---
title: 部署 Azure Monitor
description: 使用 Azure 策略大规模部署 Azure Monitor 功能。
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: fbfc0cafe83f53bd7cab2b93899e9c2cb02d52e3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505204"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>使用 Azure 策略大规模部署 Azure Monitor
虽然某些 Azure Monitor 功能配置了一次或几次限制，但必须为要监视的每个资源重复使用其他功能。 本文介绍使用 Azure 策略实现大规模 Azure Monitor 的方法，以确保对所有 Azure 资源一致且准确地配置监视。

例如，需要为所有现有 Azure 资源和创建的每个新资源创建诊断设置。 每次创建虚拟机时，还需要安装并配置代理。 可以使用 PowerShell 或 CLI 等方法执行这些操作，因为这些方法可用于 Azure Monitor 的所有功能。 使用 Azure 策略时，你可以准备好逻辑，以便在每次创建或修改资源时自动执行相应的配置。


## <a name="azure-policy"></a>Azure Policy
本部分简要介绍[Azure 策略](../../governance/policy/overview.md)，该策略允许你以最少的工作量在整个 Azure 订阅或管理组中评估和强制实施组织标准。 有关完整的详细信息，请参阅[Azure 策略文档](../../governance/policy/overview.md)。

通过 Azure 策略，你可以为创建的任何资源指定配置要求，并识别不符合的资源，阻止创建资源，或添加所需的配置。 它的工作原理是：截获调用以创建新资源或修改现有资源。 它可以使用此类效果进行响应，如拒绝请求与策略定义中所需的属性不匹配、将其标记为不符合或部署相关资源时拒绝请求。 可以使用**deployIfNotExists**或**修改**策略定义来修正现有资源。

Azure 策略由下表中的对象组成。 有关每个对象的更详细说明，请参阅[Azure 策略对象](../../governance/policy/overview.md#azure-policy-objects)。

| 项 | 说明 |
|:---|:---|
| 策略定义 | 描述在满足条件时要采取的资源符合性条件和效果。 这可能是特定类型的所有资源，也可能只是匹配某些属性的资源。 这种效果可能是只是将资源标记为符合或部署相关资源。 策略定义使用 JSON 编写，如[Azure 策略定义结构](../../governance/policy/concepts/definition-structure.md)中所述。 [了解 Azure 策略效果](../../governance/policy/concepts/effects.md)中介绍了影响。
| 策略计划 | 应该一起应用的一组策略定义。 例如，你可能有一个策略定义，用于将资源日志发送到 Log Analytics 工作区，另一个用于将资源日志发送到事件中心。 创建一个包含两个策略定义的计划，并将该计划应用于资源而不是单个策略定义。 按照[Azure 策略计划结构](../../governance/policy/concepts/initiative-definition-structure.md)中所述，使用 JSON 编写计划。 |
| 分配 | 策略定义或计划在分配到作用域之前不会生效。 例如，将策略分配给资源组，将其应用于在该资源中创建的所有资源，或将其应用于订阅，以将其应用于该订阅中的所有资源。  有关更多详细信息，请参阅[Azure 策略分配结构](../../governance/policy/concepts/assignment-structure.md)。 |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor 的内置策略定义
Azure 策略包含多个与 Azure Monitor 相关的预生成定义。 您可以将这些策略定义分配给现有订阅，也可以将它们用作基础来创建自己的自定义定义。 有关 "**监视**" 类别中内置政治的完整列表，请参阅 Azure Monitor 的[Azure 策略内置策略定义](../samples/policy-samples.md)。

若要查看与监视相关的内置策略定义，请执行以下操作：

1. 在 Azure 门户中，请参阅**Azure 策略**。
2. 选择 "**定义**"。
3. 对于 "**类型**"，选择 "*内置*" 和 "**类别**"，然后选择 "*监视*"。

  ![内置策略定义](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>诊断设置
[诊断设置](../platform/diagnostic-settings.md)将 Azure 资源中的资源日志和指标收集到多个位置，通常为 Log Analytics 工作区，该工作区允许使用[日志查询](../log-query/log-query-overview.md)和[日志警报](alerts-log.md)分析数据。 使用 "策略" 可在每次创建资源时自动创建诊断设置。

每个 Azure 资源类型都有一组唯一的类别，这些类别需要在诊断设置中列出。 因此，每个资源类型都需要一个单独的策略定义。 某些资源类型具有可在不修改的情况下分配的内置策略定义。 对于其他资源类型，需要创建自定义定义。

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor 的内置策略定义
每个资源类型都有两个内置的策略定义，一个用于发送到 Log Analytics 工作区，另一个用于事件中心。 如果只需要一个位置，则为资源类型分配该策略。 如果需要这两者，请为资源分配两个策略定义。

例如，下图显示了 Data Lake Analytics 的内置诊断设置策略定义。

  ![内置策略定义](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>自定义策略定义
对于没有内置策略的资源类型，需要创建自定义策略定义。 可以通过复制现有的内置策略，然后修改资源类型，在 Azure 门户中手动执行此操作。 不过，使用 PowerShell 库中的脚本以编程方式创建策略更有效。

脚本[AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy)创建可使用 POWERSHELL 或 CLI 安装的特定资源类型的策略文件。 使用以下过程为诊断设置创建自定义策略定义。


1. 确保已安装[Azure PowerShell](/powershell/azure/install-az-ps) 。
2. 通过以下命令安装脚本：
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. 使用参数运行脚本，以指定要将日志发送到的位置。 系统将提示你指定订阅和资源类型。 例如，若要创建发送到 Log Analytics 工作区和事件中心的策略定义，请使用以下命令。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. 或者，可以在命令中指定订阅和资源类型。 例如，若要创建发送到 Azure SQL Server 数据库 Log Analytics 工作区和事件中心的策略定义，请使用以下命令。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. 此脚本为每个策略定义创建单独的文件夹，每个文件夹都包含三个名为 azurepolicy、json azurepolicy.rules.jsazurepolicy.parameters.js的文件。 如果要在 Azure 门户中手动创建策略，可以复制并粘贴 azurepolicy.js上的内容，因为它包括整个策略定义。 使用 PowerShell 或 CLI 的其他两个文件通过命令行创建策略定义。

    下面的示例演示如何通过 PowerShell 和 CLI 安装策略定义。 每个都包括用于指定**监视**类别的元数据，以将新策略定义与内置策略定义组合在一起。

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>计划
常见的策略是创建一个包含策略定义的计划，以创建每个 Azure 服务的诊断设置，而不是为每个策略定义创建分配。 根据环境的管理方式，在计划和管理组、订阅或资源组之间创建分配。 此策略具有以下优势：

- 为计划创建单个分配，而不是为每个资源类型创建多个分配。 对于多个监视组、订阅或资源组，请使用同一计划。
- 需要添加新的资源类型或目标时，请修改计划。 例如，最初的要求可能是仅将数据发送到 Log Analytics 工作区，但稍后要添加事件中心。 修改计划，而不是创建新分配。

有关创建计划的详细信息，请参阅[创建和分配计划定义](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition)。 考虑以下建议：

- 将**类别**设置为 "**监视**"，以将其与相关的内置和自定义策略定义组合在一起。
- 请使用常见的计划参数，而不是为计划中包含的策略定义指定 Log Analytics 工作区和事件中心的详细信息。 这使你可以轻松地为所有策略定义指定一个公用值，并根据需要更改此值。

![计划定义](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>分配 
根据要监视的资源的作用域，将计划分配给 Azure 管理组、订阅或资源组。 如果你的组织有多个订阅，则[管理组](../../governance/management-groups/overview.md)特别适用于范围策略。

![计划分配](media/deploy-scale/initiative-assignment.png)

通过使用计划参数，你可以为该计划中的所有策略定义指定工作区或任何其他详细信息。 

![计划参数](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>修正
计划将应用于创建的每个虚拟机。 [修正任务](../../governance/policy/how-to/remediate-resources.md)会将计划中的策略定义部署到现有资源，因此，您可以为已创建的任何资源创建诊断设置。 当使用 Azure 门户创建分配时，可以选择同时创建修正任务。 有关修正的详细信息，请参阅[用 Azure 策略修正不符合的资源](../../governance/policy/how-to/remediate-resources.md)。

![计划修正](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor
[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)是 Azure Monitor 用于监视虚拟机的主要工具。 启用用于 VM 的 Azure Monitor 将同时安装 Log Analytics 代理和依赖项代理。 使用 Azure 策略可以确保在创建每个虚拟机时配置每个虚拟机，而不是手动执行这些任务。

用于 VM 的 Azure Monitor 包括两个称为**Enable 用于 VM 的 Azure Monitor**的内置计划，并**为虚拟机规模集启用 Azure Monitor**。 这些计划包括安装 Log Analytics 代理所需的一组策略定义，以及启用用于 VM 的 Azure Monitor 所需的依赖关系代理。 

用于 VM 的 Azure Monitor 包括一项功能，可用于检查每个范围中的虚拟机数，以确定是否已应用该计划，而不是使用 Azure 策略界面为这些计划创建分配。 然后，你可以配置工作区，并使用该接口创建任何所需的分配。

有关此过程的详细信息，请参阅[使用 Azure 策略启用用于 VM 的 Azure Monitor](../insights/vminsights-enable-at-scale-policy.md)。

![用于 VM 的 Azure Monitor 策略](../platform/media/deploy-scale/vminsights-policy.png)


## <a name="next-steps"></a>后续步骤

- 阅读有关[Azure 策略](../../governance/policy/overview.md)的详细信息。
- 详细了解[诊断设置](diagnostic-settings.md)。
