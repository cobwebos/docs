---
title: Azure 数据工厂中的持续集成和交付
description: 了解如何使用持续集成和交付将数据工厂管道从一个环境（开发、测试、生产）移到另一个环境。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 6aad01808ad155b745b614d8de6009386f0d2914
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687962"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure 数据工厂中的持续集成和交付

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概述

持续集成是尽早自动测试对代码库所做的每个更改的做法。持续交付遵循连续集成期间发生的测试，并将更改推送到暂存或生产系统。

在 Azure 数据工厂中，持续集成和交付 （CI/CD） 意味着将数据工厂管道从一个环境（开发、测试、生产）移动到另一个环境。 您可以使用数据工厂 UX 与 Azure 资源管理器模板集成来执行 CI/CD。

在数据工厂 UX 中，可以从**ARM 模板**下拉菜单生成资源管理器模板。 当您选择 **"导出 ARM 模板**"时，门户将生成数据工厂的资源管理器模板以及包含所有连接字符串和其他参数的配置文件。 然后为每个环境（开发、测试、生产）创建一个配置文件。 所有环境的主资源管理器模板文件始终相同。

有关此功能的九分钟介绍和演示，请观看此视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 生命周期

下面是使用 Azure 存储库 Git 配置的 Azure 数据工厂中的 CI/CD 生命周期示例概述。 有关如何配置 Git 存储库的详细信息，请参阅 Azure[数据工厂中的源代码管理](source-control.md)。

1.  使用 Azure 存储库 Git 创建和配置开发数据工厂。 所有开发人员都应具有创作数据工厂资源（如管道和数据集）的权限。

1.  当开发人员对其功能分支进行更改时，他们会调试其管道运行及其最新更改。 有关如何调试管道运行的详细信息，请参阅[使用 Azure 数据工厂进行迭代开发和调试](iterative-development-debugging.md)。

1.  开发人员对其更改感到满意后，他们将创建从其功能分支到主分支或协作分支的拉取请求，以便由对等方审核其更改。

1.  批准拉取请求并将更改合并到主分支中后，可以将更改发布到开发工厂。

1.  当团队准备将更改部署到测试工厂，然后部署到生产工厂时，团队将从主分支导出资源管理器模板。

1.  导出的资源管理器模板使用不同的参数文件部署到测试工厂和生产工厂。

## <a name="create-a-resource-manager-template-for-each-environment"></a>为每个环境创建资源管理器模板

1. 在**ARM 模板**列表中，选择 **"导出 ARM 模板**"以在开发环境中导出数据工厂的资源管理器模板。

   ![导出资源管理器模板](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 在测试和生产数据工厂中，选择 **"导入 ARM 模板**"。 此操作会将你转到 Azure 门户，可以在其中导入已导出的模板。 在**编辑器中选择"构建您自己的模板**"以打开资源管理器模板编辑器。

   ![构建您自己的模板](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. 选择 **"加载文件**"，然后选择生成的资源管理器模板。 这是位于步骤 1 中导出的 .zip 文件中的**arm_template.json**文件。

   ![编辑模板](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 在"设置"部分中，输入配置值，如链接的服务凭据。 完成后，选择 **"购买"** 以部署资源管理器模板。

   ![设置部分](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>连接字符串

有关如何配置连接字符串的信息，请参阅连接器的文章。 例如，对于 Azure SQL 数据库，请参阅[使用 Azure 数据工厂向/从 Azure SQL 数据库复制数据](connector-azure-sql-database.md)。 要验证连接字符串，可以在数据工厂 UX 中打开资源的代码视图。 在代码视图中，将删除连接字符串的密码或帐户密钥部分。 要打开代码视图，请选择此处突出显示的图标：

![打开代码视图以查看连接字符串](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>使用 Azure 管道版本自动实现连续集成

以下是设置 Azure 管道版本的指南，该版本自动将数据工厂部署到多个环境。

![与 Azure Pipelines 的持续集成示意图](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>要求

-   链接到使用 [Azure 资源管理器服务终结点](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)的 Azure 工作室团队基础服务器或 Azure 存储库的订阅。

-   使用 Azure 存储库 Git 集成配置的数据工厂。

-   包含每个环境机密的 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)。

### <a name="set-up-an-azure-pipelines-release"></a>设置 Azure Pipelines 发布

1.  在[Azure DevOps](https://dev.azure.com/)中，打开使用数据工厂配置的项目。

1.  在页面的左侧，选择**管道**，然后选择 **"释放**"。

    ![选择管道，发布](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  选择 **"新建管道**"，或者，如果现有管道，请选择 **"新建**"和 **"新建"管道**。

1.  选择 **"空作业**模板"。

    ![选择空作业](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  在 **"阶段名称"** 框中，输入环境的名称。

1.  选择 **"添加项目**"，然后选择与数据工厂一起配置的存储库。 选择**默认分支**的**adf_publish。** 对于**默认版本**，**从默认分支中选择"最新"。**

    ![添加项目](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  添加 Azure 资源管理器部署任务：

    a.  在舞台视图中，选择 **"查看阶段任务**"。

    ![舞台视图](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  创建新任务。 搜索**Azure 资源组部署**，然后选择 **"添加**"。

    c.  在"部署"任务中，选择目标数据工厂的订阅、资源组和位置。 如有必要，提供凭据。

    d.  在 **"操作**"列表中，选择 **"创建"或"更新资源组**"。

    e.  选择**模板**框旁边的省略号按钮 （**...）。** 浏览在本文的每个环境部分的"[创建资源管理器资源管理器"模板](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)中使用 **"导入 ARM 模板**"创建的 Azure 资源管理器模板。 在adf_publish分支的<FactoryName>文件夹中查找此文件。

    f.  选择 **...** 在 **"模板参数"** 框旁边，以选择参数文件。 您选择的文件将取决于您是创建副本还是正在使用默认文件 ARMTemplate 参数 ForFactory.json。

    g.  选择 **...** 在 **"覆盖模板参数"** 框旁边，然后输入目标数据工厂的信息。 对于来自 Azure 密钥保管库的凭据，请在双引号之间输入机密的名称。 例如，如果机密的名称为 cred1，请输入此值的 **"$（cred1）"。**

    h. 为**部署模式**选择**增量**。

    > [!WARNING]
    > 如果为 **"部署"模式**选择 **"完成"，** 则可能会删除现有资源，包括"资源管理器"模板中未定义的目标资源组中的所有资源。

    ![数据工厂部署](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  保存发布管道。

1. 要触发版本，请选择 **"创建版本**"。

   ![选择"创建发布"](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> 在 CI/CD 方案中，不同环境中的集成运行时 （IR） 类型必须相同。 例如，如果在开发环境中具有自托管 IR，则相同的 IR 还必须在其他环境中（如测试和生产）中采用自托管类型。 同样，如果要跨多个阶段共享集成运行时，则必须将所有环境（如开发、测试和生产）中的自托管链接配置集成运行时。

### <a name="get-secrets-from-azure-key-vault"></a>从 Azure 密钥保管库获取机密

如果有要在 Azure 资源管理器模板中传递的机密，我们建议您在 Azure 管道发布中使用 Azure 密钥保管库。

处理机密的方法有两种：

1.  将机密添加到参数文件。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/templates/key-vault-parameter.md)。

    创建上载到发布分支的参数文件的副本。 使用此格式设置要从密钥保管库获取的参数的值：

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    使用此方法时，会自动从密钥保管库拉取机密。

    参数文件也需位于 publish 分支中。

1. 在上一节中介绍的 Azure 资源管理器部署任务之前添加[Azure 密钥保管库任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)：

    1.  在"**任务"** 选项卡上，创建新任务。 搜索**Azure 密钥保管库**并添加它。

    1.  在密钥保管库任务中，选择创建密钥保管库的订阅。 如有必要，提供凭据，然后选择密钥保管库。

    ![添加密钥保管库任务](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>向 Azure Pipelines 代理授权

如果未设置正确的权限，Azure 密钥保管库任务可能会失败，并出现"访问被拒绝"错误。 下载版本的日志，并找到包含向 Azure 管道代理授予权限的命令的 .ps1 文件。 您可以直接运行该命令。 或者，可以从文件复制主体 ID，并在 Azure 门户中手动添加访问策略。 `Get`是`List`所需的最低权限。

### <a name="update-active-triggers"></a>更新活动触发器

如果尝试更新活动触发器，部署可能会失败。 要更新活动触发器，您需要手动停止它们，然后在部署后重新启动它们。 可以使用 Azure PowerShell 任务执行此操作：

1.  在版本的 **"任务"** 选项卡上，添加**Azure PowerShell**任务。 选择任务版本 4。* 

1.  选择工厂正在的订阅。

1.  选择**脚本文件路径**作为脚本类型。 这要求您将 PowerShell 脚本保存在存储库中。 以下 PowerShell 脚本可用于停止触发器：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

您可以完成类似的步骤（使用`Start-AzDataFactoryV2Trigger`函数）在部署后重新启动触发器。

### <a name="sample-pre--and-post-deployment-script"></a>部署前和部署后脚本示例

以下示例脚本可用于在部署之前停止触发器，并在部署后重新启动触发器。 此脚本还包括用于删除已移除资源的代码。 将脚本保存在 Azure DevOps git 存储库中，并使用版本 4 通过 Azure PowerShell 任务引用它。

运行预部署脚本时，需要在**脚本参数**字段中指定以下参数的变体。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


运行部署后脚本时，需要在 **"脚本参数"** 字段中指定以下参数的变体。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell 任务](media/continuous-integration-deployment/continuous-integration-image11.png)

下面是可用于部署前和部署后使用的脚本。 它考虑已删除的资源和资源引用。

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>将自定义参数用于资源管理器模板

如果处于 GIT 模式，则可以覆盖资源管理器模板中的默认属性，以设置模板中参数化的属性和硬编码属性。 在这些情况下，您可能希望重写默认参数化模板：

* 您使用自动 CI/CD，并希望在资源管理器部署期间更改某些属性，但默认情况下，这些属性不会参数化。
* 您的工厂非常大，默认资源管理器模板无效，因为它具有超过最大允许的参数 （256）。

在这些情况下，要覆盖默认参数化模板，请将其指定为数据工厂 git 集成的根文件夹中的名为**arm-template-参数-定义.json**的文件。 您必须使用该确切的文件名。 数据工厂从 Azure 数据工厂门户中当前位于哪个分支（而不仅仅是协作分支）读取此文件。 可以从专用分支创建或编辑文件，通过选择 UI 中的 **"导出 ARM 模板**"来测试更改。 然后，您可以将文件合并到协作分支中。 如果未找到文件，则使用默认模板。

> [!NOTE]
> 自定义参数化模板不会更改 ARM 模板参数限制 256。 它允许您选择和减少参数化属性的数量。

### <a name="syntax-of-a-custom-parameters-file"></a>自定义参数文件的语法

以下是创建自定义参数文件时应遵循的一些准则。 该文件由每个实体类型的部分组成：触发器、管道、链接服务、数据集、集成运行时等。
* 输入相关实体类型下的属性路径。
* 将属性名称设置为 `*`指示要参数化其下的所有属性（仅下到第一个级别，而不是递归）。 您还可以为此配置提供异常。
* 将属性的值设置为字符串表示要对属性进行参数化。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是以下字符之一：
      * `=` 表示将当前值保留为参数的默认值。
      * `-` 表示不保留参数的默认值。
      * `|` 是连接字符串或密钥的 Azure 密钥保管库中机密的特殊情况。
   * `<name>` 是参数的名称。 如果为空，则获取属性的名称。 如果值以字符开头`-`，则名称将缩短。 例如，`AzureStorage1_properties_typeProperties_connectionString`将缩短为`AzureStorage1_connectionString`。
   * `<stype>` 是参数的类型。 如果 `<stype>` 为空，则默认类型为`string`。 支持的值： `string` `bool`、 `number` `object`、`securestring`和 。
* 在定义文件中指定数组表示模板中的匹配属性是数组。 数据工厂通过使用在数组的集成运行时对象中指定的定义对数组中的所有对象进行遍舍。 第二个对象（一个字符串）成为属性的名称，这用作每次遍历的参数的名称。
* 定义不能特定于资源实例。 任何定义都适用于该类型的所有资源。
* 默认情况下，所有安全字符串（如密钥保管库机密）和安全字符串（如连接字符串、键和令牌）均参数化。
 
### <a name="sample-parameterization-template"></a>示例参数化模板

下面是参数化模板可能是什么样子的示例：

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
以下是如何构造前面的模板的说明，按资源类型细分。

#### <a name="pipelines"></a>管道
    
* 路径`activities/typeProperties/waitTimeInSeconds`中的任何属性都进行了参数化。 管道中具有名称为代码`waitTimeInSeconds`级属性的任何活动（例如，`Wait`活动）都参数化为数字，并带有默认名称。 但它在资源管理器模板中没有默认值。 它将是资源管理器部署期间的必填输入。
* 同样，名为`headers`（例如，在`Web`活动中）的属性使用类型`object`（JObject） 进行参数化。 它有一个默认值，该值与源工厂的值相同。

#### <a name="integrationruntimes"></a>集成运行时间

* 路径`typeProperties`下的所有属性都使用其各自的默认值进行参数化。 例如，类型属性下`IntegrationRuntimes`有两个属性：`computeProperties`和`ssisProperties`。 这两种属性类型都是使用各自的默认值和类型 （Object） 创建的。

#### <a name="triggers"></a>触发器

* 下`typeProperties`为 参数化了两个属性。 第一个是`maxConcurrency`，它指定为具有默认值，并且类型`string`为 。 它有默认参数名称`<entityName>_properties_typeProperties_maxConcurrency`。
* 属性`recurrence`也进行了参数化。 在它下，该级别上的所有属性都指定为字符串参数化，具有默认值和参数名称。 属性除外，该`interval`属性参数化为 类型`number`。 参数名称后缀与`<entityName>_properties_typeProperties_recurrence_triggerSuffix`。 同样，该`freq`属性是一个字符串，并参数化为字符串。 但是，`freq`该属性在没有默认值的情况下进行参数化。 名称被缩短和后缀。 例如，`<entityName>_freq` 。

#### <a name="linkedservices"></a>链接服务

* 链接的服务是唯一的。 由于链接的服务和数据集类型范围很广，因此可以提供特定于类型的自定义。 在此示例中，对于类型`AzureDataLakeStore`的所有链接服务，将应用特定的模板。 对于所有其他（通过`*`），将应用不同的模板。
* 该`connectionString`属性将参数化为`securestring`值。 它不会有默认值。 它将具有一个缩短的参数名称，该参数名称后缀为`connectionString`。
* 该属性`secretAccessKey`恰好是`AzureKeyVaultSecret`a（例如，在 Amazon S3 链接服务中）。 它会自动参数化为 Azure 密钥保管库密钥，并从配置的密钥保管库提取。 您还可以对密钥保管库本身进行参数化。

#### <a name="datasets"></a>数据集

* 尽管特定于类型的自定义可用于数据集，但您可以提供配置，而无需显式配置\*-level 配置。 在前面的示例中，下`typeProperties`的所有数据集属性都进行了参数化。

### <a name="default-parameterization-template"></a>默认参数化模板

下面是当前默认参数化模板。 如果只需要添加几个参数，则直接编辑此模板可能是个好主意，因为不会丢失现有的参数化结构。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

下面的示例演示如何向默认参数化模板添加单个值。 我们只想为数据砖块链接服务添加到参数文件的现有 Azure 数据砖块交互式群集 ID。 请注意，此文件与上一个文件相同，除了 在`existingClusterId``Microsoft.DataFactory/factories/linkedServices`的属性字段下添加。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>链接的资源管理器模板

如果已为数据工厂设置 CI/CD，则随着工厂规模越来越大，可能会超出 Azure 资源管理器模板限制。 例如，一个限制是资源管理器模板中的最大资源数。 为了容纳大型工厂，同时为工厂生成完整的资源管理器模板，数据工厂现在生成链接的资源管理器模板。 使用此功能，整个工厂负载将分解为多个文件，这样您就不会受到限制的限制。

如果配置了 Git，则链接模板将生成并保存与adf_publish分支中的完整资源管理器模板一起保存在称为链接模板的新文件夹中：

![链接的资源管理器模板文件夹](media/continuous-integration-deployment/linked-resource-manager-templates.png)

链接的资源管理器模板通常由主模板和一组链接到主模板的子模板组成。 父模板称为 ArmTemplate_master.json，子模板使用模式 ArmTemplate_0.json、ArmTemplate_1.json 等命名。 

要使用链接模板而不是完整的资源管理器模板，请更新 CI/CD 任务以指向 ArmTemplate_master.json 而不是 ArmTemplateForFactory.json（完整的资源管理器模板）。 资源管理器还要求将链接的模板上载到存储帐户中，以便 Azure 可以在部署期间访问它们。 有关详细信息，请参阅使用[VSTS 部署链接的资源管理器模板](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)。

不要忘记在执行部署任务之前和之后在 CI/CD 管道中添加数据工厂脚本。

如果未配置 Git，则可以通过**ARM 模板**列表中**的导出 ARM 模板**访问链接模板。

## <a name="hotfix-production-branch"></a>修补程序生产分支

如果将工厂部署到生产部门，并意识到存在需要马上修复的 Bug，但无法部署当前协作分支，则可能需要部署修补程序。 此方法称为快速修复工程或 QFE。

1.    在 Azure DevOps 中，转到部署到生产中的版本。 查找部署的最后一个提交。

2.    从提交消息中获取协作分支的提交 ID。

3.    从该提交创建新的修补程序分支。

4.    转到 Azure 数据工厂 UX 并切换到修补程序分支。

5.    通过使用 Azure 数据工厂 UX，修复 Bug。 测试更改。

6.    验证修复程序后，选择 **"导出 ARM 模板**"以获取修补程序资源管理器模板。

7.    手动将此生成签入adf_publish分支。

8.    如果已将发布管道配置为根据adf_publish签入自动触发，则新版本将自动启动。 否则，手动对发布进行排队。

9.    将修补程序版本部署到测试和生产工厂。 此版本包含以前的生产负载以及您在步骤 5 中所做的修复。

10.    将修补程序中的更改添加到开发分支，以便以后的版本不会包含相同的 Bug。

## <a name="best-practices-for-cicd"></a>CI/CD 最佳做法

如果您使用 Git 集成数据工厂，并且具有将更改从开发移动到测试，然后移动到生产中的 CI/CD 管道，我们建议采用以下最佳做法：

-   **Git 集成**。 您只需使用 Git 集成配置开发数据工厂。 测试和生产更改通过 CI/CD 部署，不需要 Git 集成。

-   **数据工厂 CI/CD 脚本**。 在 CI/CD 中的资源管理器部署步骤之前，您需要完成某些任务，例如停止和重新启动触发器以及执行清理。 我们建议您在部署前后使用 PowerShell 脚本。 有关详细信息，请参阅[更新活动触发器](#update-active-triggers)。

-   **集成运行时和共享**。 集成运行时不会经常更改，并且在 CI/CD 中的所有阶段都类似。 因此，数据工厂希望您在 CI/CD 的所有阶段具有相同的名称和类型的集成运行时。 如果要跨所有阶段共享集成运行时，请考虑使用三元工厂来包含共享集成运行时。 您可以在所有环境中使用此共享工厂作为链接集成运行时类型。

-   **密钥保管库**。 使用基于 Azure 密钥保管库的链接服务时，可以通过为不同的环境保留单独的密钥保管库来进一步利用这些服务。 您还可以为每个密钥保管库配置单独的权限级别。 例如，您可能不希望团队成员具有生产机密的权限。 如果您遵循此方法，我们建议您在所有阶段都保留相同的机密名称。 如果保留相同的名称，则不必跨 CI/CD 环境更改资源管理器模板，因为唯一更改的是密钥保管库名称，这是资源管理器模板参数之一。

## <a name="unsupported-features"></a>不支持的功能

- 根据设计，数据工厂不允许精心挑选提交或选择性地发布资源。 发布将包括在数据工厂中所做的所有更改。

    - 数据工厂实体相互依赖。 例如，触发器依赖于管道，管道依赖于数据集和其他管道。 选择性地发布资源子集可能会导致意外的行为和错误。
    - 在需要选择性发布时，请考虑使用修补程序。 有关详细信息，请参阅[修补程序生产分支](#hotfix-production-branch)。

-   无法从专用分支发布。

-   当前无法在 Bitbucket 上承载项目。
