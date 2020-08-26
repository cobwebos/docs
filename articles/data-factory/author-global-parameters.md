---
title: 全局参数
description: 为每个 Azure 数据工厂环境设置全局参数
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854201"
---
# <a name="global-parameters-in-azure-data-factory"></a>Azure 数据工厂中的全局参数

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

全局参数是可由任意表达式中的管道使用的数据工厂中的常量。 当具有多个具有相同参数名称和值的管道时，它们非常有用。 使用持续集成和部署过程 (CI/CD) 提升数据工厂时，可以在每个环境中重写这些参数。 

## <a name="creating-global-parameters"></a>创建全局参数

若要创建全局参数，请在 "*管理*" 部分中转到 "*全局参数*" 选项卡。 选择 "**新建**" 以打开 "创建" 侧导航栏。

![创建全局参数](media/author-global-parameters/create-global-parameter-1.png)

在侧导航栏中，输入名称，选择数据类型，并指定参数的值。

![创建全局参数](media/author-global-parameters/create-global-parameter-2.png)

创建全局参数后，可以通过单击参数的名称对其进行编辑。 若要同时更改多个参数，请选择 "**全部编辑**"。

![创建全局参数](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>在管道中使用全局参数

全局参数可用于任何[管道表达式](control-flow-expression-language-functions.md)。 如果管道引用其他资源（如数据集或数据流），则可以通过该资源的参数向下传递全局参数值。 全局参数被称为 `pipeline().globalParameters.<parameterName>` 。

![使用全局参数](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a>CI/CD 中的全局参数

全局参数相对于 Azure 数据工厂中的其他实体具有唯一的 CI/CD 进程。 发布工厂或导出带有全局参数的 ARM 模板时，会创建一个名为 " *globalParameters* " 的文件夹，其中包含一个名为 *"your-factory-name_GlobalParameters.js"* 的文件。 此文件是一个 JSON 对象，其中包含已发布工厂中的每个全局参数类型和值。

![发布全局参数](media/author-global-parameters/global-parameters-adf-publish.png)

如果要部署到新的环境（如测试或生产环境），则建议创建此全局参数文件的副本，并覆盖相应的特定于环境的值。 重新发布原始全局参数文件时，将会覆盖该文件，但其他环境的副本将保持不变。

例如，如果你有一个名为 "ADF-DEV" 的工厂，并且有一个名为 "环境" 且值为 "DEV" 的类型字符串的全局参数，则当你发布时，将生成一个名为*ADF-DEV_GlobalParameters.js*的文件。 如果部署到名为 "ADF_TEST" 的测试工厂，请创建 JSON 文件的副本 (例如，在) 上命名为 ADF-TEST_GlobalParameters.js，并将参数值替换为特定于环境的值。 参数 "环境" 现在可能具有值 "test"。 

![部署全局参数](media/author-global-parameters/powershell-task.png)

使用以下 PowerShell 脚本将全局参数提升到其他环境。 在 ARM 模板部署之前添加 Azure PowerShell DevOps 任务。 在 DevOps 任务中，必须指定新参数文件、目标资源组和目标数据工厂的位置。

> [!NOTE]
> 若要使用 PowerShell 部署全局参数，必须至少使用 Az module 的4.4.0 版本。

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>后续步骤

* 了解 Azure 数据工厂的[持续集成和部署过程](continuous-integration-deployment.md)
* 了解如何使用[控制流表达式语言](control-flow-expression-language-functions.md)