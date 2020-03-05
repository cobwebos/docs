---
title: Runbook 输入参数
description: Runbook 输入参数可让你将数据传递到启动的 Runbook，以增加 Runbook 的弹性。 本文介绍在 Runbook 中使用输入参数的不同方案。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 274ee0fe98281e733994f2d5df38886409cbc913
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273654"
---
# <a name="runbook-input-parameters"></a>Runbook 输入参数

Runbook 输入参数允许在启动 runbook 时向它传递数据，从而提高 runbook 的灵活性。 这些参数可让 runbook 操作以特定方案和环境为目标。 本文介绍如何配置和使用 runbook 中的输入参数。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="configuring-input-parameters"></a>配置输入参数

可以为 PowerShell、PowerShell 工作流、图形和 Python runbook 配置输入参数。 一个 Runbook 可以包含具有不同数据类型的多个参数，或者不包含任何参数。 输入参数可以是必需的或可选的，并且可以使用可选参数的默认值。

启动 runbook 时，为 runbook 的输入参数分配值。 可以从 Azure 门户、web 服务或 PowerShell 启动 runbook。 还可以启动一个 Runbook 作为另一个 Runbook 中内联调用的子 Runbook。

### <a name="configure-input-parameters-in-powershell-runbooks"></a>在 PowerShell Runbook 中配置输入参数

Azure Automation 中的 PowerShell 和 PowerShell 工作流 runbook 支持通过以下属性定义的输入参数。 

| **属性** | **说明** |
|:--- |:--- |
| 类型 |必需。 参数值所需的数据类型。 任何 .NET 类型均有效。 |
| 名称 |必需。 参数的名称。 此名称在 runbook 中必须是唯一的，必须以字母开头，并且只能包含字母、数字或下划线字符。 |
| 必需 |可选。 指定参数是否需要值的布尔值。 如果将此值设置为**true**，则在启动 runbook 时必须提供一个值。 如果将此值设置为**false**，则值是可选的。 如果未指定**必需**属性的值，则默认情况下，PowerShell 会将输入参数视为可选参数。 |
| 默认值 |可选。 如果在 runbook 启动时未传入输入值，则为参数使用该值。 Runbook 可以为任何参数设置默认值。 |

Windows PowerShell 支持的输入参数的属性比上面列出的更多，例如验证、别名和参数集。 但是，Azure 自动化目前仅支持列出的输入参数属性。

例如，让我们看一下 PowerShell 工作流 runbook 中的参数定义。 此定义具有以下常规形式，其中，多个参数之间用逗号分隔。

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

现在，让我们配置 PowerShell 工作流 runbook 的输入参数，该 runbook 会输出有关虚拟机的详细信息，即单个 VM 或资源组中的所有 Vm。 此 runbook 有两个参数，如以下屏幕截图所示：虚拟机的名称（*VMName*）和资源组的名称（*resourceGroupName*）。

![自动化 PowerShell 工作流](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

在此参数定义中，输入参数是字符串类型的简单参数。

请注意，PowerShell 和 PowerShell 工作流 runbook 支持所有简单类型和复杂类型，例如输入参数的**object**或**PSCredential** 。 如果 runbook 具有对象输入参数，则必须使用包含名称/值对的 PowerShell 哈希表来传入值。 例如，runbook 中有以下参数。

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

在这种情况下，可以将以下值传递到参数。

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> 如果不将值传递到带有 null 默认值的可选字符串参数，则该参数的值为空字符串而不是**null**。

### <a name="configure-input-parameters-in-graphical-runbooks"></a>在图形 Runbook 中配置输入参数

为了说明图形 runbook 的输入参数的配置，让我们创建一个 runbook，用于输出有关虚拟机（单个 VM 或资源组中的所有 Vm）的详细信息。 有关详细信息，请参阅[我的第一个图形 runbook](automation-first-runbook-graphical.md)。

图形 runbook 使用以下主要 runbook 活动：

* 配置 Azure 运行方式帐户，以便在 Azure 中进行身份验证。 
* 定义获取 VM 属性的[new-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet。
* 使用[写入输出](/powershell/module/microsoft.powershell.utility/write-output)活动输出 VM 名称。 

**New-azvm**活动定义了两个输入： VM 名称和资源组名称。 由于这些名称在每次启动 runbook 时可能不同，因此你必须将输入参数添加到 runbook，才能接受这些输入。 请参阅[Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。

按照以下步骤配置输入参数。

1. 从 " **runbook** " 页中选择图形 runbook，然后单击 "**编辑**"。
2. 在图形编辑器中，单击 "**输入和输出**" 按钮，然后单击 "**添加输入**"，打开 "Runbook 输入参数" 窗格。

   ![自动化图形 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. "输入和输出" 控件显示为 runbook 定义的输入参数的列表。 可在此处添加新的输入参数或编辑现有输入参数的配置。 若要为 runbook 添加新参数，请单击 "**添加输入**"，打开 " **runbook 输入参数**" 边栏选项卡，可以在其中使用在[Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)中定义的属性来配置参数。

    ![添加新输入](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 使用以下属性创建两个参数，供**new-azvm**活动使用，然后单击 **"确定"** 。

   * 参数 1：
        * **名称** -- **VMName**
        * **类型**--字符串
        * **必需** -- 

   * 参数2：
        * **名称** -- **resourceGroupName**
        * **类型**--字符串
        * **必需** -- 
        * **默认值** -- **自定义**
        * 自定义默认值-包含 Vm 的资源组的名称

5. 查看输入和输出控件中的参数。 
6. 再次单击 **"确定"** ，然后单击 "**保存**"。
7. 单击 "**发布**" 以发布 runbook。

### <a name="configure-input-parameters-in-python-runbooks"></a>在 Python Runbook 中配置输入参数

与 PowerShell、PowerShell 工作流和图形 runbook 不同，Python runbook 不使用命名参数。 Runbook 编辑器将所有输入参数分析为参数值的数组。 可以通过将**sys**模块导入 Python 脚本，然后使用**sys.databases**数组来访问数组。 请务必注意，数组的第一个元素 `sys.argv[0]`是脚本的名称。 因此，第一个实际输入参数为*sys.databases [1]* 。

若要获取如何在 Python Runbook 中使用输入参数的示例，请参阅[我在 Azure 自动化中的第一个 Python Runbook](automation-first-runbook-textual-python2.md)。

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>向 runbook 中的输入参数赋值

本部分介绍将值传递到 runbook 中的输入参数的几种方法。 可以在以下情况中分配参数值：

* [启动 Runbook](#start-a-runbook-and-assign-parameters)
* [测试 Runbook](#test-a-runbook-and-assign-parameters)
* [链接 runbook 的计划](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [为 runbook 创建 webhook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>启动 Runbook 并分配参数

可以通过多种方式启动 runbook：通过 Azure 门户和 webhook，使用 PowerShell cmdlet、REST API 或 SDK。 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>使用 Azure 门户启动已发布的 runbook 并分配参数

在 Azure 门户中[启动 runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal)时，"**启动 runbook** " 边栏选项卡会打开，你可以为已创建的参数输入值。

![使用门户启动](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

在输入框下面的标签中，可以看到已设置为定义参数属性的属性，例如，必需或可选，类型，默认值。 参数名称旁边的帮助气球还定义了确定参数输入值所需的关键信息。 

> [!NOTE]
> 字符串参数支持 String 类型的空值。 在输入参数框中输入 **[EmptyString]** 将向参数传递空字符串。 而且，字符串参数不支持 Null。 如果未向字符串参数传递任何值，PowerShell 会将其解释为 Null。

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>使用 PowerShell cmdlet 启动已发布的 runbook 并分配参数

* **Azure 资源管理器 cmdlet：** 可以使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)启动在资源组中创建的自动化 runbook。

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure 经典部署模型 cmdlet**：可以使用 [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook) 启动在默认资源组中创建的自动化 Runbook。
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> 使用 PowerShell cmdlet 启动 runbook 时，将使用值**PowerShell**创建默认参数*将 microsoftapplicationmanagementstartedby*。 您可以在 "作业详细信息" 窗格中查看此参数。  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>使用 SDK 启动 runbook 并分配参数

* **Azure 资源管理器方法：** 可以使用编程语言的 SDK 启动 runbook。 以下 C# 代码段用于在自动化帐户中启动 Runbook。 可以在 [GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)中查看完整代码。  

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Azure 经典部署模型方法：** 可使用编程语言的 SDK 启动 Runbook。 以下 C# 代码段用于在自动化帐户中启动 Runbook。 可以在 [GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)中查看完整代码。

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   若要启动此方法，请创建一个字典来存储 runbook 参数*VMName*和*resourceGroupName*及其值。 然后启动 Runbook。 以下 C# 代码段用于调用上面定义的方法。

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>使用 REST API 启动 runbook 并分配参数

你可以通过使用**PUT**方法和以下请求 URI 来创建和启动 runbook 作业： Azure 自动化 REST API： `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

在请求 URI 中替换以下参数：

* *subscriptionId*：你的 AZURE 订阅 ID。  
* *resourceGroupName*：自动化帐户的资源组的名称。
* *automationAccountName*：托管在指定云服务中的自动化帐户的名称。  
* *jobName*：作业的 GUID。 可以使用 `[GUID]::NewGuid().ToString()*`创建 PowerShell 中的 Guid。

若要将参数传递到 runbook 作业，请使用请求正文。 它采用 JSON 格式提供的以下信息：

* Runbook name：必需。 作业要启动的 Runbook 的名称。  
* Runbook 参数：可选。 In （name，value）格式的参数列表的字典，其中名称为字符串类型，值可以是任何有效的 JSON 值。

如果要启动前面使用*VMName*和*ResourceGroupName*创建的**get-azurevmtextual** runbook 作为参数，请对请求正文使用以下 JSON 格式。

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

如果成功创建了作业，将返回 HTTP 状态代码 201。 有关响应标头和响应正文的详细信息，请参阅[使用 REST API 创建 runbook 作业](/rest/api/automation/job/create)。

### <a name="test-a-runbook-and-assign-parameters"></a>测试 Runbook 并分配参数

使用测试选项[测试 runbook 的草稿版本](automation-testing-runbook.md)时，将打开 "**测试**" 页。 使用此页可以配置已创建的参数的值。

![测试并分配参数](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>将计划链接到 Runbook 并分配参数

可[将计划链接](automation-schedules.md)到 Runbook，以便在特定的时间启动 Runbook。 创建计划时将指定输入参数，Runbook 在按计划启动时，将使用这些值。 在提供所有必需的参数值之前，无法保存计划。

![计划并分配参数](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>创建 Runbook 的 Webhook 并分配参数

可为 Runbook 创建 [Webhook](automation-webhooks.md) 并配置 Runbook 输入参数。 在提供所有必需的参数值之前，无法保存 webhook。

![创建 webhook 并分配参数](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

使用 webhook 执行 runbook 时，会发送预定义的输入参数 *[WebhookData](automation-webhooks.md#details-of-a-webhook)* 和你定义的输入参数。 

![WebhookData 参数](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>将 JSON 对象传递到 runbook

它可用于存储想要传递到 JSON 文件中的 Runbook 的数据。 例如，你可以创建一个 JSON 文件，其中包含要传递给 runbook 的所有参数。 为此，必须将 JSON 代码转换为字符串，然后将该字符串转换为 PowerShell 对象，然后再将其传递给 runbook。

本部分使用一个示例，其中 PowerShell 脚本调用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)来启动 PowerShell runbook，并将 JSON 文件的内容传递到 runbook。 PowerShell runbook 通过从 JSON 对象检索 VM 的参数来启动 Azure VM。

### <a name="create-the-json-file"></a>创建 JSON 文件

在文本文件中键入以下代码，并将其保存为 `test.json` 本地计算机上的某个位置。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>创建 Runbook

在 Azure Automation 中创建一个名为 "**测试-Json** " 的新 PowerShell runbook。 请参阅[我的第一个 PowerShell runbook](automation-first-runbook-textual-powershell.md)。

要接受 JSON 数据，Runbook 必须将一个对象作为输入参数。 然后，runbook 可以使用 JSON 文件中定义的属性。

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

在自动化帐户中保存并发布此 Runbook。

### <a name="call-the-runbook-from-powershell"></a>通过 PowerShell 调用 Runbook

现在可以通过 Azure PowerShell 从本地计算机调用 Runbook。 

1. 按照所示登录到 Azure。 之后，系统会提示输入 Azure 凭据。

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >对于 PowerShell runbook， **AzAccount**和**add-azurermaccount**是**AzAccount**的别名。 请注意，这些别名不可用于图形 runbook。 图形 runbook 只能使用**AzAccount**本身。

1. 获取已保存 JSON 文件的内容，并将其转换为字符串。 `JsonPath` 是保存 JSON 文件的位置路径。

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. 将 `$json` 的字符串内容转换为 PowerShell 对象。

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. 为**AzAutomationRunbook**的参数创建哈希表。 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   请注意，你要将*参数*的值设置为包含 JSON 文件中的值的 PowerShell 对象。
1. 启动 runbook。

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>后续步骤

* 有关以不同方式启动 Runbook 的详细信息，请参阅[启动 Runbook](automation-starting-a-runbook.md)。
* 若要编辑文本 Runbook，请参阅[编辑文本 Runbook](automation-edit-textual-runbook.md)。
* 若要编辑图形 Runbook，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。
