---
title: 在 Azure 自动化中配置 runbook 输入参数
description: 本文介绍如何配置 runbook 输入参数，通过这些参数可以在启动 runbook 时将数据传递给它。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 84e2eaf71326f59102800428479768aeba9ef9ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87042159"
---
# <a name="configure-runbook-input-parameters"></a>配置 Runbook 输入参数

Runbook 输入参数允许在启动 Runbook 时向它传递数据，从而增加 Runbook 的灵活性。 这些参数可让 Runbook 操作以特定方案和环境为目标。 本文介绍如何在 runbook 中配置和使用输入参数。

可以为 PowerShell、PowerShell 工作流、图形和 Python runbook 配置输入参数。 一个 Runbook 可以包含具有不同数据类型的多个参数，或者不包含任何参数。 输入参数可以是必需的或可选的，可以对可选参数使用默认值。

在启动 Runbook 时分配 Runbook 的输入参数值。 可以从 Azure 门户、Web 服务或 PowerShell 启动 Runbook。 还可以启动一个 Runbook 作为另一个 Runbook 中内联调用的子 Runbook。

### <a name="configure-input-parameters-in-powershell-runbooks"></a>在 PowerShell Runbook 中配置输入参数

Azure 自动化中的 PowerShell 和 PowerShell 工作流 Runbook 支持通过以下属性定义的输入参数。 

| **属性** | **说明** |
|:--- |:--- |
| 类型 |必需。 参数值所需的数据类型。 任何 .NET 类型均有效。 |
| 名称 |必需。 参数的名称。 此名称在 Runbook 中必须唯一，必须以字母开头，并且只能包含字母、数字或下划线字符。 |
| 必需 |可选。 指定参数是否需要值的布尔值。 如果将此值设置为 True，则启动 Runbook 时必须提供一个值。 如果将此值设置为 False，则值是可选的。 如果没有为 `Mandatory` 属性指定值，则默认情况下，PowerShell 会将输入参数视为可选参数。 |
| 默认值 |可选。 一个值，在 Runbook 启动时未传入输入值的情况下用于参数。 Runbook 可以为任何参数设置默认值。 |

Windows PowerShell 支持的输入参数属性比上面所列的多，例如验证、别名和参数集。 但是，Azure 自动化目前仅支持列出的输入参数属性。

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

现在，让我们为输出有关虚拟机（可以是单个 VM 或资源组中的所有 VM）的详细信息的 PowerShell 工作流 Runbook 配置输入参数。 如以下屏幕截图中所示，此 Runbook 有两个参数：虚拟机的名称 (`VMName`) 和资源组的名称 (`resourceGroupName`)。

![自动化 PowerShell 工作流](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

在此参数定义中，输入参数是字符串类型的简单参数。

请注意，PowerShell 和 PowerShell 工作流 Runbook 对于输入参数支持所有简单类型和复杂类型，例如 `Object` 或 `PSCredential`。 如果 Runbook 有对象输入参数，则必须使用包含名称/值对的 PowerShell 哈希表来传入值。 例如，Runbook 中有以下参数。

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

在此情况下，可将以下值传递到该参数。

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> 未将值传递到带有 null 默认值的可选字符串参数时，参数的值会为空字符串而不是 Null。

### <a name="configure-input-parameters-in-graphical-runbooks"></a>在图形 Runbook 中配置输入参数

为了说明图形 Runbook 的输入参数配置，让我们创建输出有关虚拟机（可以是单个 VM 或资源组中的所有 VM）的详细信息的 Runbook。 有关详细信息，请参阅[我的第一个图形 Runbook](./learn/automation-tutorial-runbook-graphical.md)。

图形 runbook 使用以下主要 runbook 活动：

* 配置 Azure 运行方式帐户，以便与 Azure 进行身份验证。 
* 定义 [Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet 以获取 VM 属性。
* 使用 [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) 活动输出 VM 名称。 

`Get-AzVM` 活动定义了两个输入：VM 名称和资源组名称。 由于这些名称在每次启动 runbook 时可能不同，因此必须将输入参数添加到 runbook 才能接受这些输入。 请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。

遵循以下步骤配置输入参数。

1. 从“Runbook”页选择图形 Runbook，然后单击“编辑”。
2. 在图形编辑器中单击“输入和输出”按钮，然后单击“添加输入”打开“Runbook 输入参数”窗格。

   ![自动化图形 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. “输入和输出”控件显示针对 Runbook 定义的输入参数列表。 在此处可以添加新的输入参数，或编辑现有输入参数的配置。 若要为 Runbook 添加新参数，请单击“添加输入”以打开“Runbook 输入参数”边栏选项卡，在其中可以使用 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)中定义的属性来配置参数。

    ![添加新输入](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 使用以下属性创建供 `Get-AzVM` 活动使用的两个参数，然后单击“确定”。

   * 参数 1：
        * 名称 -- VMName
        * 类型 -- 字符串
        * 必需 -- 否

   * 参数 2：
        * 名称 -- resourceGroupName
        * 类型 -- 字符串
        * 必需 -- 否
        * 默认值 -- 自定义
        * 自定义默认值 - 包含 VM 的资源组的名称

5. 查看“输入和输出”控件中的参数。 
6. 再次单击“确定”，然后单击“保存”。
7. 单击“发布”以发布 runbook。

### <a name="configure-input-parameters-in-python-runbooks"></a>在 Python Runbook 中配置输入参数

与 PowerShell、PowerShell 工作流和图形 Runbook 不同，Python Runbook 不使用命名参数。 Runbook 编辑器将所有输入参数分析为参数值的数组。 可以通过将 `sys` 模块导入 Python 脚本，然后使用 `sys.argv` 数组来访问数组。 请务必注意，数组的第一个元素 `sys.argv[0]` 是脚本的名称。 因此第一个实际输入参数是 `sys.argv[1]`。

若要获取如何在 Python Runbook 中使用输入参数的示例，请参阅[我在 Azure 自动化中的第一个 Python Runbook](./learn/automation-tutorial-runbook-textual-python2.md)。

## <a name="assign-values-to-input-parameters-in-runbooks"></a>为 Runbook 中的输入参数赋值

此部分介绍将值传递给 Runbook 中的输入参数的多种方法。 可以在以下情况中分配参数值：

* [启动 Runbook](#start-a-runbook-and-assign-parameters)
* [测试 Runbook](#test-a-runbook-and-assign-parameters)
* [链接 Runbook 的计划](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [为 runbook 创建 Webhook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>启动 Runbook 并分配参数

Runbook 有多种启动方式：通过 Azure 门户、Webhook、PowerShell cmdlet、REST API 或 SDK。 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>使用 Azure 门户启动已发布的 Runbook 并分配参数

在 Azure 门户中[启动 Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) 时，“启动 Runbook”边栏选项卡会打开，可以为所创建的参数输入值。

![使用门户启动](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

在输入框下面的标签中，可以查看为定义参数特性（例如必需或可选、类型和默认值）而设置的属性。 参数名称旁边的帮助气球也定义了做出参数输入值相关决策时所需的关键信息。 

> [!NOTE]
> 字符串参数支持字符串类型的空值。 在输入参数框中输入 `[EmptyString]` 将向参数传递空字符串。 此外，字符串参数不支持 Null。 如果未向字符串参数传递任何值，PowerShell 会将值解释为 Null。

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>使用 PowerShell cmdlet 启动已发布的 Runbook 并分配参数

* **Azure 资源管理器 cmdlet：** 可使用 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0) 启动在资源组中创建的自动化 Runbook。

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure 经典部署模型 cmdlet：** 可使用 [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook) 启动在默认资源组中创建的自动化 Runbook。
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> 使用 PowerShell cmdlet 启动 runbook 时，将创建默认参数 `MicrosoftApplicationManagementStartedBy`，其值为 `PowerShell`。 可在“作业详细信息”窗格中查看此参数。  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>使用 SDK 启动 Runbook 并分配参数

* **Azure 资源管理器方法：** 可使用编程语言的 SDK 来启动 Runbook。 以下 C# 代码段用于在自动化帐户中启动 Runbook。 可以在 [GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)中查看完整代码。

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

* **Azure 经典部署模型方法：** 可使用编程语言的 SDK 来启动 Runbook。 以下 C# 代码段用于在自动化帐户中启动 Runbook。 可以在 [GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)中查看完整代码。

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

   若要启动此方法，请创建一个字典来存储 Runbook 参数 `VMName` 和 `resourceGroupName` 及其值。 然后启动 Runbook。 以下 C# 代码段用于调用上面定义的方法。

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>使用 REST API 启动 Runbook 并分配参数

可在以下请求 URI 中使用 `PUT` 方法通过 Azure 自动化 REST API 来创建并启动 Runbook 作业：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

在请求 URI 中替换以下参数：

* `subscriptionId`：Azure 订阅 ID。  
* `resourceGroupName`：自动化帐户的资源组的名称。
* `automationAccountName`：托管在指定云服务中的自动化帐户的名称。  
* `jobName`：作业的 GUID。 可以使用 `[GUID]::NewGuid().ToString()*` 创建 PowerShell 中的 GUID。

若要将参数传递到 Runbook 作业，请使用请求正文。 它采用以 JSON 格式提供的以下信息：

* Runbook 名称：必需。 作业要启动的 Runbook 的名称。  
* Runbook 参数：可选。 采用 (名称, 值) 格式的参数列表的字典，其中的名称应为字符串类型，值可以是任何有效的 JSON 值。

如果想要启动之前以 `VMName` 和 `resourceGroupName` 作为参数创建的 Get-AzureVMTextual Runbook，请将以下 JSON 格式用于请求正文。

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

如果成功创建了作业，将返回 HTTP 状态代码 201。 有关响应标头和响应正文的详细信息，请参阅[使用 REST API 创建 Runbook 作业](/rest/api/automation/job/create)。

### <a name="test-a-runbook-and-assign-parameters"></a>测试 Runbook 并分配参数

使用测试选项[测试 runbook 的草稿版本](./manage-runbooks.md)时，将打开“测试”页。 使用此页可为已创建的参数配置值。

![测试并分配参数](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>将计划链接到 Runbook 并分配参数

可[将计划链接](./shared-resources/schedules.md)到 Runbook，以便在特定的时间启动 Runbook。 创建计划时将指定输入参数，Runbook 在按计划启动时，将使用这些值。 只有在提供所有必需参数值之后，才可以保存计划。

![计划并分配参数](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>创建 Runbook 的 Webhook 并分配参数

可为 Runbook 创建 [Webhook](automation-webhooks.md) 并配置 Runbook 输入参数。 只有在提供所有必需参数值之后，才可以保存 Webhook。

![创建 webhook 并分配参数](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

当使用 Webhook 执行 Runbook 时，将连同定义的输入参数一起发送预定义的输入参数 `[WebhookData](automation-webhooks.md)`。 

![WebhookData 参数](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>将 JSON 对象传递给 runbook

它可用于存储想要传递到 JSON 文件中的 Runbook 的数据。 例如，你可能会创建包含所有想要传递给 Runbook 的参数的 JSON 文件。 为此，必须先将 JSON 代码转换为字符串，将字符串转换为 PowerShell 对象，然后再将其传递到 Runbook。

此部分使用一个示例，其中 PowerShell 脚本会调用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) 来启动 PowerShell Runbook，将 JSON 文件的内容传递给 Runbook。 该 PowerShell Runbook 通过从 JSON 对象检索 Azure VM 的参数，来启动该 VM。

### <a name="create-the-json-file"></a>创建 JSON 文件

在文本文件中键入以下代码，并在本地计算机上将其保存为 test.json。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>创建 Runbook

在 Azure 自动化中创建名为“Test-Json”的新 PowerShell Runbook。 请参阅[我的第一个 PowerShell Runbook](./learn/automation-tutorial-runbook-textual-powershell.md)。

要接受 JSON 数据，Runbook 必须将一个对象作为输入参数。 然后，Runbook 可以使用 JSON 文件中定义的属性。

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

1. 如图所示登录 Azure。 之后，系统会提示输入 Azure 凭据。

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >对于 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的别名。 请注意，这些别名不适用于图形 Runbook。 图形 Runbook 只能使用 `Connect-AzAccount` 本身。

1. 获取已保存 JSON 文件的内容并将其转换为字符串。 `JsonPath` 指示保存 JSON 文件的位置路径。

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. 将 `$json` 的字符串内容转换为 PowerShell 对象。

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. 为 `Start-AzAutomationRunbook` 的参数创建哈希表。 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   请注意，你正在将 `Parameters` 的值设置为包含 JSON 文件中的值的 PowerShell 对象。
1. 启动 Runbook。

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>后续步骤

* 若要准备文本 runbook，请参阅[在 Azure 自动化中编辑文本 runbook](automation-edit-textual-runbook.md)。
* 若要准备图形 runbook，请参阅[在 Azure 自动化中创作图形 runbook](automation-graphical-authoring-intro.md)。
