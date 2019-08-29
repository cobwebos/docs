---
title: 在 Azure 数据工厂中进行持续集成和交付 | Microsoft Docs
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
ms.date: 01/17/2019
ms.openlocfilehash: 4d4fe32b5f457e2b223132006afd20bfe3161bbd
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142605"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>在 Azure 数据工厂中进行持续集成和交付 (CI/CD)

## <a name="overview"></a>概述

持续集成是这样一种做法：自动地尽早测试对代码库所做的每项更改。 在测试之后进行的持续交付可将更改推送到过渡或生产系统，而测试发生在持续集成期间。

在 Azure 数据工厂中, 持续集成 & 提供意味着将数据工厂管道从一个环境 (开发、测试、生产) 移到另一个环境。 若要 & 传递进行持续集成, 你可以将数据工厂 UX 集成与 Azure 资源管理器模板结合使用。 数据工厂 UX 可以从**ARM 模板**下拉列表中生成资源管理器模板。 选择“导出 ARM 模板”时，门户会为数据工厂生成资源管理器模板，并生成一个包含所有连接字符串和其他参数的配置文件。 然后, 你必须为每个环境 (开发、测试、生产) 创建一个配置文件。 所有环境的主资源管理器模板文件始终相同。

有关此功能的 9 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>持续集成生命周期

下面是使用 Azure Repos Git 配置的 Azure 数据工厂中的持续集成和交付生命周期的示例概述。 有关如何配置 Git 存储库的详细信息, 请参阅[Azure 数据工厂中的源代码管理](source-control.md)。

1.  开发数据工厂使用 Azure Repos Git 创建和配置, 其中所有开发人员都有权创作数据工厂资源, 如管道和数据集。

1.  当开发人员在其功能分支中进行更改时, 它们会使用最新的更改对其管道运行进行调试。 有关如何调试管道运行的详细信息, 请参阅[使用 Azure 数据工厂进行迭代开发和调试](iterative-development-debugging.md)。

1.  当开发人员对更改感到满意后, 他们会创建一个从其功能分支到主分支或协作分支的拉取请求, 以获取对等方所做的更改。

1.  批准拉取请求并将更改合并到主分支后, 它们可发布到开发工厂。

1.  当团队准备好将所做的更改部署到测试工厂, 然后再部署到生产工厂时, 它们将从 master 分支导出资源管理器模板。

1.  将导出的资源管理器模板部署到测试工厂和生产工厂的不同参数文件。

## <a name="create-a-resource-manager-template-for-each-environment"></a>为每个环境创建资源管理器模板

从**ARM 模板**下拉列表中, 选择 "**导出 ARM 模板**", 为开发环境中的数据工厂导出资源管理器模板。

![](media/continuous-integration-deployment/continuous-integration-image1.png)

在测试和生产数据工厂中, 选择 "**导入 ARM 模板**"。 此操作会将你转到 Azure 门户，可以在其中导入已导出的模板。 选择 **"在编辑器中生成自己的模板**" 以打开 "资源管理器模板编辑器"。

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

单击 "**加载文件**", 然后选择生成的资源管理器模板。

![](media/continuous-integration-deployment/continuous-integration-image4.png)

在 "设置" 窗格中, 输入配置值, 例如链接的服务凭据。 完成后, 单击 "**购买**" 以部署资源管理器模板。

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>连接字符串

有关如何配置连接字符串的信息, 请参阅每个连接器的文章。 例如，对于 Azure SQL 数据库，请参阅[使用 Azure 数据工厂向/从 Azure SQL 数据库复制数据](connector-azure-sql-database.md)。 若要验证连接字符串, 可以在数据工厂 UX 中打开资源的 "代码" 视图。 在 "代码" 视图中, 将删除连接字符串的密码或帐户密钥部分。 若要打开代码视图，请选择下面的屏幕截图中突出显示的图标。

![打开代码视图来查看连接字符串](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>使用 Azure Pipelines 发布自动完成持续集成

下面是设置 Azure Pipelines 版本的指南, 它自动将数据工厂部署到多个环境。

![与 Azure Pipelines 的持续集成示意图](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>要求

-   使用 [azure 资源管理器服务终结点](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)链接到 Team Foundation Server 或 Azure Repos 的 azure 订阅。

-   使用 Azure Repos Git 集成配置的数据工厂。

-   一个 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , 其中包含每个环境的机密。

### <a name="set-up-an-azure-pipelines-release"></a>设置 Azure Pipelines 发布

1.  在[Azure DevOps 用户体验](https://dev.azure.com/)中, 打开配置了数据工厂的项目。

1.  在页面左侧, 单击 "**管道**", 然后选择 "**发布**"。

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  选择 "**新建管道**"; 如果您有现有管道, 则选择 "**新建**", 然后选择**新的发布管道**。

1.  选择**空作业**模板。

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  在 "**阶段名称**" 字段中, 输入环境的名称。

1.  选择 "**添加项目**", 并选择配置有数据工厂的相同存储库。 选择 `adf_publish` 作为使用最新默认版本的默认分支。

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  添加 Azure 资源管理器部署任务：

    a.  在 "阶段" 视图中, 单击 "**查看阶段任务**" 链接。

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  创建新的任务。 搜索 " **Azure 资源组部署**", 然后单击 "**添加**"。

    c.  在部署任务中选择目标数据工厂对应的订阅、资源组和位置，然后根据需要提供凭据。

    d.  在 "操作" 下拉列表中, 选择 "**创建或更新资源组**"。

    e.  在“替代模板参数”字段旁边 选择“…”。 通过为[每个环境创建资源管理器模板](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)中的 "**导入 ARM 模板**" 步骤, 浏览 Azure 资源管理器模板创建。 在 `adf_publish` 分支的文件夹 `<FactoryName>` 中查找该文件。

    f.  在“替代模板参数”字段旁边 在 "**模板参数" 字段中。** 选择参数文件。 选择正确的文件，具体取决于你是创建了副本，还是使用默认的 *ARMTemplateParametersForFactory.json* 文件。

    g.  在“替代模板参数”字段旁边 选择“…”，然后填充目标数据工厂的信息。 对于来自密钥保管库的凭据, 请在双引号之间输入密钥名称。 例如, 如果密码的名称为`cred1`, 则输入`"$(cred1)"`其值。

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. 选择“增量”部署模式。

    > [!WARNING]
    > 如果选择 "**完成**部署模式", 则可能会删除现有资源, 其中包括未在资源管理器模板中定义的目标资源组中的所有资源。

1.  保存发布管道。

1. 若要触发发布, 请单击 "**创建发布**"

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>从 Azure Key Vault 获取机密

如果你有权传入 Azure 资源管理器模板中的机密, 则建议在 Azure Pipelines 版本中使用 Azure Key Vault。

可以通过两种方式来处理机密:

1.  将机密添加到参数文件。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/resource-manager-keyvault-parameter.md)。

    -   创建上传到 publish 分支的参数文件的副本，并使用以下格式设置需要从密钥保管库获取的参数的值：

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

    -   使用此方法时，会自动从密钥保管库拉取机密。

    -   参数文件也需位于 publish 分支中。

1.  在上一部分中介绍的 Azure 资源管理器部署之前添加 [Azure Key Vault 任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)：

    -   选择“任务”选项卡，创建新的任务，然后搜索并添加“Azure Key Vault”。

    -   在 Key Vault 任务中，选择在其中创建了密钥保管库的订阅，根据需要提供凭据，然后选择密钥保管库。

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>向 Azure Pipelines 代理授权

如果没有适当的权限, Azure Key Vault 任务可能会失败并出现 "拒绝访问" 错误。 请下载此发行版的日志，并使用此命令找到 `.ps1` 文件，以便向 Azure Pipelines 代理授权。 可以直接运行此命令，也可以从文件中复制主体 ID，然后在 Azure 门户中手动添加访问策略。 **Get**和**List**是所需的最小权限。

### <a name="update-active-triggers"></a>更新活动触发器

如果尝试更新活动触发器，部署可能会失败。 若要更新活动触发器，需手动将其停止，在部署后再将其启动。 可以通过 Azure Powershell 任务实现此目的。

1.  在版本的 "任务" 选项卡中, 添加**Azure Powershell**任务。

1.  选择“Azure 资源管理器”作为连接类型，然后选择订阅。

1.  选择“内联脚本”作为脚本类型，然后提供代码。 以下示例停止触发器：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

部署后, 可以执行类似的步骤`Start-AzDataFactoryV2Trigger`来重新启动触发器。

> [!IMPORTANT]
> 在持续集成和部署方案中，不同环境之间的集成运行时类型必须相同。 例如，如果在开发环境中有自承载集成运行时 (IR)，则在测试和生产等其他环境中同一 IR 的类型必须为自承载。 同样，如果跨多个阶段共享集成运行时，则必须在所有环境（如开发、测试和生产）中将集成运行时配置为“链接自承载”。

#### <a name="sample-prepostdeployment-script"></a>示例预/部署后脚本

下面是一个示例脚本, 用于在部署之前停止触发器并随后重启触发器。 此脚本还包括用于删除已移除资源的代码。 若要安装最新版本的 Azure PowerShell，请参阅[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>将自定义参数用于资源管理器模板

如果处于 GIT 模式, 则可以覆盖资源管理器模板中的默认属性, 以设置在模板中参数化的属性和硬编码的属性。 在以下情况下, 你可能希望重写默认参数化模板:

* 你使用自动 CI/CD, 并且想要在资源管理器部署过程中更改某些属性, 但默认情况下不会对属性进行参数化。
* 由于默认资源管理器模板超过允许的最大参数 (256), 因此您的工厂非常大。

在这些情况下, 若要重写默认参数化模板, 请在存储库的根文件夹中创建一个名为 " *arm-模板-参数-json* " 的文件。 文件名必须完全匹配。 数据工厂尝试从你当前在 Azure 数据工厂门户中的哪个分支读取此文件, 而不仅仅是从协作分支读取此文件。 你可以从私有分支创建或编辑该文件, 你可以使用 UI 中的 "**导出 ARM" 模板**来测试你的更改。 然后, 可以将文件合并到协作分支。 如果未找到任何文件, 则使用默认模板。


### <a name="syntax-of-a-custom-parameters-file"></a>自定义参数文件的语法

下面是在创作自定义参数文件时使用的一些准则。 该文件由每个实体类型的部分组成: 触发器、管道、链接服务、数据集、集成运行时等。
* 输入相关实体类型下的属性路径。
* 将属性名称设置为 "\*" 时, 表示要将其下的所有属性参数化 (仅向下一级, 而不是递归)。 你还可以向此提供任何例外。
* 将属性的值设置为字符串时，表示你希望参数化该属性。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是下列字符之一:
      * `=` 表示保留当前值作为参数的默认值。
      * `-` 表示不要保留参数的默认值。
      * `|` 是 Azure Key Vault 用于连接字符串或键的密码的特例。
   * `<name>` 参数的名称。 如果为空, 则采用属性的名称。 如果值以`-`字符开头, 则将缩短名称。 例如, `AzureStorage1_properties_typeProperties_connectionString`将缩短为`AzureStorage1_connectionString`。
   * `<stype>` 参数的类型。 `string`如果 `<stype>`为空,则 默认类型为。 支持的值`string`: `bool`、 `number`、 `object`、和`securestring`。
* 如果在定义文件中指定数组, 则表明模板中的匹配属性是数组。 数据工厂使用数组的 Integration Runtime 对象中指定的定义来循环访问数组中的所有对象。 第二个对象（一个字符串）成为属性的名称，这用作每次遍历的参数的名称。
* 不能有特定于资源实例的定义。 任何定义都适用于该类型的所有资源。
* 默认情况下, 所有安全字符串 (如 Key Vault 机密) 和安全字符串 (如连接字符串、密钥和令牌) 都是参数化的。
 
### <a name="sample-parameterization-template"></a>示例参数化模板

下面是参数化模板的示例:

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
下面说明如何构造上述模板, 按资源类型细分。

#### <a name="pipelines"></a>管道
    
* Path activity/typeProperties/waitTimeInSeconds 中的任何属性均已参数化。 管道中具有名为`waitTimeInSeconds` (例如`Wait` , 活动) 的代码级别属性的任何活动均被参数化为数字, 并且具有默认名称。 但资源管理器模板中没有默认值。 在资源管理器部署过程中, 它将是必需的输入。
* 同样, 名`headers`为的属性 (例如, `Web`在活动中) 使用 type `object` (JObject) 进行参数化。 它有一个默认值, 该值与源工厂中的值相同。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 路径`typeProperties`下的所有属性均由其各自的默认值参数化。 例如, **IntegrationRuntimes** type properties 下有两个属性: `computeProperties`和。 `ssisProperties` 这两个属性类型都是用各自的默认值和类型 (对象) 创建的。

#### <a name="triggers"></a>Triggers

* 在`typeProperties`下, 参数化两个属性。 第一个是`maxConcurrency`, 它被指定为具有默认值, 并且的类型`string`为。 它的默认参数名称为`<entityName>_properties_typeProperties_maxConcurrency`。
* 此`recurrence`属性也是参数化的。 在该级别下, 将指定该级别的所有属性指定为字符串, 并将默认值和参数名称指定为参数。 异常是`interval`属性, 该属性被参数化为数值类型, 参数名称以`<entityName>_properties_typeProperties_recurrence_triggerSuffix`后缀作为后缀。 同样, 此`freq`属性是一个字符串, 参数化为字符串。 但是, 属性`freq`的参数化没有默认值。 名称将被缩短并带有后缀。 例如， `<entityName>_freq` 。

#### <a name="linkedservices"></a>LinkedServices

* 链接服务是唯一的。 由于链接服务和数据集具有各种类型, 因此你可以提供特定于类型的自定义。 在此示例中, 将应用类型`AzureDataLakeStore`为的所有链接服务, 将应用特定的模板, 并且将应用其他模板 (通过\*)。
* 该`connectionString`属性将被参数化为`securestring`一个值, 它不会有默认值, 并且它将具有一个后缀为`connectionString`的缩写参数名称。
* 属性`secretAccessKey`碰巧`AmazonS3`为 (例如, 在链接服务中)。 `AzureKeyVaultSecret` 它自动参数化为 Azure Key Vault 机密, 并从配置的密钥保管库提取。 还可以参数化密钥保管库本身。

#### <a name="datasets"></a>数据集

* 尽管特定于类型的自定义可用于数据集, 但可以提供配置, 而\*无需明确地进行配置。 在上面的示例中, 所有`typeProperties`的数据集属性都是参数化的。

### <a name="default-parameterization-template"></a>默认参数化模板

下面是当前默认参数化模板。 如果只需要添加一个或几个参数, 则直接编辑此项可能会很有用, 因为不会丢失现有的参数化结构。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

下面是如何将单个值添加到默认参数化模板的示例。 我们只想要将 Databricks 链接服务的现有 Databricks 交互式群集 ID 添加到参数文件。 请注意, 下面的文件与上述文件相同, 不同之`existingClusterId`处在于在的属性`Microsoft.DataFactory/factories/linkedServices`字段下包含。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

如果已为数据工厂设置持续集成和部署 (CI/CD), 则当工厂增长更大时, 你可能会遇到 Azure 资源管理器模板限制。 限制的一个示例是资源管理器模板中的最大资源数。 为了适应大型工厂, 以及生成工厂的完整资源管理器模板, 数据工厂现在会生成链接资源管理器模板。 利用此功能, 整个工厂有效负载分为多个文件, 因此不会遇到限制。

如果已配置 Git, 则会生成链接的模板, 并将其与`adf_publish`分支中完整资源管理器模板一起保存在一个名`linkedTemplates`为的新文件夹下。

![链接的资源管理器模板文件夹](media/continuous-integration-deployment/linked-resource-manager-templates.png)

链接的资源管理器模板通常有一个主模板和一组链接到主模板的子模板。 父模板名为 `ArmTemplate_master.json`，子模板以如下模式命名：`ArmTemplate_0.json`、`ArmTemplate_1.json`，依此类推。 若要使用链接模板而不是完整资源管理器模板, 请将 CI/CD 任务更新为`ArmTemplate_master.json`指向`ArmTemplateForFactory.json` (而不是完整资源管理器模板)。 资源管理器还要求将链接的模板上传到存储帐户，以便它们在部署期间可供 Azure 访问。 有关详细信息，请参阅[通过 VSTS 部署链接的 ARM 模板](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)。

不要忘记在执行部署任务之前和之后在 CI/CD 管道中添加数据工厂脚本。

如果没有配置 Git，则可以通过**导出 ARM 模板**操作访问链接的模板。

## <a name="hot-fix-production-branch"></a>热修复生产分支

如果将工厂部署到生产环境, 并意识到需要立即修复的 bug, 但无法部署当前的协作分支, 则可能需要部署热修补程序。

1.  在 Azure DevOps 中, 请前往部署到生产环境的版本, 并找到已部署的上次提交。

2.  从提交消息获取协作分支的提交 ID。

3.  从该提交创建新的热修复分支。

4.  转到 Azure 数据工厂 UX, 并切换到此分支。

5.  使用 Azure 数据工厂 UX, 修复错误。 测试更改。

6.  验证修补程序后, 请单击 "**导出 ARM 模板**" 以获取热修补资源管理器模板。

7.  手动将此生成签入到 adf_publish 分支。

8.  如果已将发布管道配置为基于 adf_publish 签入自动触发, 则新版本将自动启动。 否则, 手动将发布排队。

9.  将热修补版本部署到测试工厂和生产工厂。 此版本包含之前的生产负载以及步骤5中所做的修复。

10. 将修复中的更改添加到开发分支, 以便以后版本不会遇到同一 bug。

## <a name="best-practices-for-cicd"></a>CI/CD 最佳做法

如果你使用数据工厂的 Git 集成，并且某个 CI/CD 管道会将更改从“开发”环境依次转移到“测试”和“生产”环境，则我们建议采用以下最佳做法：

-   **Git 集成**。 仅需通过 Git 集成配置开发数据工厂。 通过 CI/CD 部署对测试和生产的更改, 无需 Git 集成。

-   **数据工厂 CI/CD 脚本**。 在 CI/CD 中资源管理器部署步骤之前, 需要执行某些任务 (如停止/启动触发器和清理)。 建议在部署前后使用 powershell 脚本。 有关详细信息, 请参阅[Update active trigger](#update-active-triggers)。 

-   **集成运行时和共享**。 集成运行时不经常更改, 并且在 CI/CD 中的所有阶段都是类似的。 因此，数据工厂预期集成运行时在 CI/CD 的所有阶段具有相同的名称和类型。 如果希望在所有阶段共享集成运行时, 请考虑只使用三元工厂来包含共享集成运行时。 可以在所有环境中将此共享工厂用作链接集成运行时类型。

-   **Key Vault**。 使用基于 Azure Key Vault 的链接服务时, 可以通过为不同的环境保留单独的密钥保管库, 进一步充分利用它。 此外，可为每个 Key Vault 单独配置权限级别。 例如, 你可能不希望团队成员有权使用生产机密。 如果遵循此方法, 则建议在所有阶段保留相同的机密名称。 如果保留相同的名称, 则无需在 CI/CD 环境中更改资源管理器模板, 因为唯一更改的是密钥保管库名称, 这是一个资源管理器模板参数。

## <a name="unsupported-features"></a>不受支持的功能

-   不能发布单个资源。 数据工厂实体相互依赖并跟踪变化的依赖项可能很困难, 并导致意外的行为。 例如, 触发器依赖于管道, 管道依赖于数据集和其他管道, 依此类推。 如果可以仅发布整个更改集的子集, 则可能会发生某些无法预见的错误。

-   无法从专用分支发布。

-   无法在 Bitbucket 上托管项目。
