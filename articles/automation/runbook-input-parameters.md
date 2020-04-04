---
title: Runbook 输入参数
description: Runbook 输入参数可让你将数据传递到启动的 Runbook，以增加 Runbook 的弹性。 本文介绍在 Runbook 中使用输入参数的不同方案。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656032"
---
# <a name="runbook-input-parameters"></a>Runbook 输入参数

Runbook 输入参数允许在启动时将数据传递给 Runbook，从而提高了 Runbook 的灵活性。 这些参数允许针对特定方案和环境将 Runbook 操作作为目标。 本文介绍了 Runbook 中输入参数的配置和使用。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="configuring-input-parameters"></a>配置输入参数

您可以为 PowerShell、PowerShell 工作流、图形和 Python Runbook 配置输入参数。 一个 Runbook 可以包含具有不同数据类型的多个参数，或者不包含任何参数。 输入参数可以是必需的，也可以是可选的，也可以对可选参数使用默认值。

启动 Runbook 时，会将值分配给 Runbook 的输入参数。 可以从 Azure 门户、Web 服务或 PowerShell 启动 Runbook。 还可以启动一个 Runbook 作为另一个 Runbook 中内联调用的子 Runbook。

### <a name="configure-input-parameters-in-powershell-runbooks"></a>在 PowerShell Runbook 中配置输入参数

Azure 自动化中的 PowerShell 和 PowerShell 工作流运行簿支持通过以下属性定义的输入参数。 

| **属性** | **说明** |
|:--- |:--- |
| 类型 |必需。 参数值所需的数据类型。 任何 .NET 类型均有效。 |
| 名称 |必需。 参数的名称。 此名称在 Runbook 中必须是唯一的，必须以字母开头，并且只能包含字母、数字或下划线字符。 |
| 必需 |可选。 布尔值指定参数是否需要值。 如果将此值设置为 True，则在启动 Runbook 时必须提供值。 如果将此值设置为 False，则值是可选的。 如果不为`Mandatory`属性指定值，PowerShell 会考虑默认情况下输入参数可选。 |
| 默认值 |可选。 在 Runbook 启动时没有传入输入值时用于参数的值。 Runbook 可以为任何参数设置默认值。 |

Windows PowerShell 支持的输入参数属性比上面列出的属性多，例如验证、别名和参数集。 但是，Azure 自动化目前仅支持列出的输入参数属性。

例如，让我们在 PowerShell 工作流运行簿中查看参数定义。 此定义具有以下常规形式，其中多个参数用逗号分隔。

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

现在，让我们为 PowerShell 工作流运行簿配置输入参数，该运行簿输出有关虚拟机的详细信息，即单个 VM 或资源组中的所有 VM。 此 Runbook 有两个参数，如以下屏幕截图所示：虚拟机的名称 （`VMName`） 和资源组的名称 （`resourceGroupName`）。

![自动化 PowerShell 工作流](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

在此参数定义中，输入参数是类型字符串的简单参数。

请注意，PowerShell 和 PowerShell 工作流运行簿支持所有简单类型和复杂类型`Object`，`PSCredential`例如 或用于输入参数。 如果 Runbook 具有对象输入参数，则必须使用具有名称值对的 PowerShell 哈希表来传递值。 例如，Runbook 中具有以下参数。

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

在这种情况下，您可以将以下值传递给参数。

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> 如果不将值传递给具有 null 默认值的可选 String 参数，则该参数的值是空字符串而不是 Null。

### <a name="configure-input-parameters-in-graphical-runbooks"></a>在图形 Runbook 中配置输入参数

为了说明图形 Runbook 的输入参数配置，让我们创建一个 Runbook，用于输出有关虚拟机的详细信息，其中包括单个 VM 或资源组中的所有 VM。 有关详细信息，请参阅[我的第一个图形运行簿](automation-first-runbook-graphical.md)。

图形运行簿使用这些主要 Runbook 活动：

* 配置 Azure 运行作为帐户，以便使用 Azure 进行身份验证。 
* [获取 AVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet 以获取 VM 属性的定义。
* 使用[写入输出](/powershell/module/microsoft.powershell.utility/write-output)活动输出 VM 名称。 

活动`Get-AzVM`定义两个输入：VM 名称和资源组名称。 由于每次 Runbook 启动时这些名称可能不同，因此必须向 Runbook 添加输入参数以接受这些输入。 请参阅[Azure 自动化 中的图形创作](automation-graphical-authoring-intro.md)。

按照以下步骤配置输入参数。

1. 从 Runbook 页面中选择图形运行簿，然后单击"**编辑**"。
2. 在图形编辑器中，单击 **"输入"和"输出**"按钮，然后**添加输入**以打开 Runbook 输入参数窗格。

   ![自动化图形 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. "输入"和"输出"控件显示为 Runbook 定义的输入参数列表。 在这里，您可以添加新的输入参数或编辑现有输入参数的配置。 要为 Runbook 添加新参数，请单击"**添加输入**"以打开**Runbook 输入参数**边栏选项卡，您可以在其中使用[Azure 自动化 中的图形创作中](automation-graphical-authoring-intro.md)定义的属性配置参数。

    ![添加新输入](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 创建两个参数，具有活动要使用`Get-AzVM`的属性，然后单击 **"确定**"。

   * 参数 1：
        * **名称** -- **VM 名称**
        * **类型**-- 字符串
        * **强制** -- **否**

   * 参数 2：
        * **命名** -- **资源组名称**
        * **类型**-- 字符串
        * **强制** -- **否**
        * **默认值** -- **自定义**
        * 自定义默认值 -- 包含 VM 的资源组的名称

5. 查看输入和输出控件中的参数。 
6. 再次单击 **"确定**"，然后单击"**保存**"。
7. 单击 **"发布**"以发布 Runbook。

### <a name="configure-input-parameters-in-python-runbooks"></a>在 Python Runbook 中配置输入参数

与 PowerShell、PowerShell 工作流和图形运行簿不同，Python Runbook 不采用命名参数。 Runbook 编辑器将所有输入参数解析为参数值数组。 您可以通过将`sys`模块导入 Python 脚本，然后使用数组来`sys.argv`访问阵列。 请务必注意，数组`sys.argv[0]`的第一个元素是脚本的名称。 因此，第一个实际输入`sys.argv[1]`参数是 。

若要获取如何在 Python Runbook 中使用输入参数的示例，请参阅[我在 Azure 自动化中的第一个 Python Runbook](automation-first-runbook-textual-python2.md)。

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>在 Runbook 中为输入参数分配值

本节介绍将值传递给 Runbook 中的输入参数的几种方法。 您可以在以下时间分配参数值：

* [启动 Runbook](#start-a-runbook-and-assign-parameters)
* [测试 Runbook](#test-a-runbook-and-assign-parameters)
* [链接运行簿的计划](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [为 runbook 创建 Webhook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>启动 Runbook 并分配参数

运行簿可以通过多种方式启动：通过 Azure 门户、Webhook、使用 PowerShell cmdlet、REST API 或 SDK。 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>使用 Azure 门户启动已发布的 Runbook 并分配参数

在 Azure 门户中[启动 Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal)时，**将打开"开始运行簿"** 边栏选项卡，您可以输入已创建的参数的值。

![使用门户启动](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

在输入框下方的标签中，您可以看到已设置为定义参数属性的属性，例如，必需或可选的类型、默认值。 参数名称旁边的帮助气球还定义了对参数输入值做出决策所需的关键信息。 

> [!NOTE]
> 字符串参数支持字符串类型的空值。 输入`[EmptyString]`参数框将空字符串传递给该参数。 此外，字符串参数不支持 Null。 如果不将任何值传递给字符串参数，PowerShell 会将其解释为 Null。

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>使用 PowerShell cmdlet 启动已发布的 Runbook 并分配参数

* **Azure 资源管理器 cmdlet：** 可以使用["开始-Az自动化 Runbook"](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)启动在资源组中创建的自动化 Runbook。

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
> 当您使用 PowerShell cmdlet 启动 Runbook 时，`MicrosoftApplicationManagementStartedBy`将使用 值`PowerShell`创建默认参数 。 您可以在"作业详细信息"窗格中查看此参数。  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>使用 SDK 启动运行簿并分配参数

* **Azure 资源管理器方法：** 您可以使用编程语言的 SDK 启动 Runbook。 以下 C# 代码段用于在自动化帐户中启动 Runbook。 可以在 [GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)中查看完整代码。

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

* **Azure 经典部署模型方法：** 可使用编程语言的 SDK 启动 Runbook。 以下 C# 代码段用于在自动化帐户中启动 Runbook。 可以在 [GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)中查看完整代码。

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

   要启动此方法，请创建一个字典来存储 Runbook`VMName`参数`resourceGroupName`及其值。 然后启动 Runbook。 以下 C# 代码段用于调用上面定义的方法。

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>使用 REST API 启动运行簿并分配参数

可以使用 Azure 自动化 REST API 创建和启动 Runbook 作业`PUT`，方法是将 方法与以下请求 URI 一起使用：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

在请求 URI 中替换以下参数：

* `subscriptionId`：您的 Azure 订阅 ID。  
* `resourceGroupName`：自动化帐户的资源组的名称。
* `automationAccountName`：在指定的云服务中托管的自动化帐户的名称。  
* `jobName`：作业的 GUID。 PowerShell 中的 GUID 可以使用`[GUID]::NewGuid().ToString()*`创建。

要将参数传递给 Runbook 作业，请使用请求正文。 它采用以下信息，以 JSON 格式提供：

* 名称：必需。 作业要启动的 Runbook 的名称。  
* Runbook 参数：可选。 参数列表的字典（名称、值）格式，其中名称为字符串类型，值可以是任何有效的 JSON 值。

如果要启动之前创建的**获取 AzureVMText_runbook，**`VMName`请使用 和`resourceGroupName`作为参数，请对请求正文使用以下 JSON 格式。

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

如果成功创建了作业，将返回 HTTP 状态代码 201。 有关响应标头和响应正文的详细信息，请参阅使用 REST [API 创建 Runbook 作业](/rest/api/automation/job/create)。

### <a name="test-a-runbook-and-assign-parameters"></a>测试 Runbook 并分配参数

使用测试选项[测试 Runbook 的草稿版本](automation-testing-runbook.md)时，将打开"测试"页。 使用此页面可配置已创建的参数的值。

![测试并分配参数](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>将计划链接到 Runbook 并分配参数

可[将计划链接](automation-schedules.md)到 Runbook，以便在特定的时间启动 Runbook。 创建计划时将指定输入参数，Runbook 在按计划启动时，将使用这些值。 只有在提供所有必需参数值之后，才可以保存计划。

![计划并分配参数](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>创建 Runbook 的 Webhook 并分配参数

可为 Runbook 创建 [Webhook](automation-webhooks.md) 并配置 Runbook 输入参数。 只有在提供所有必需参数值之后，才可以保存 Webhook。

![创建 webhook 并分配参数](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

使用 Webhook 执行 Runbook 时，将发送预定义的输入`[WebhookData](automation-webhooks.md)`参数以及定义的输入参数。 

![WebhookData 参数](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>将 JSON 对象传递到 Runbook

它可用于存储想要传递到 JSON 文件中的 Runbook 的数据。 例如，您可以创建一个 JSON 文件，该文件包含要传递给 Runbook 的所有参数。 为此，必须将 JSON 代码转换为字符串，然后将字符串转换为 PowerShell 对象，然后再将其传递到 Runbook。

本节使用一个示例，其中 PowerShell 脚本调用[Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)来启动 PowerShell 运行簿，将 JSON 文件的内容传递到 Runbook。 PowerShell 运行簿通过从 JSON 对象检索 VM 的参数来启动 Azure VM。

### <a name="create-the-json-file"></a>创建 JSON 文件

在文本文件中键入以下代码，并将其另存为本地计算机上的**test.json。**

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>创建 Runbook

在 Azure 自动化中创建新的 PowerShell 运行簿，名为**Test-Json。** 请参阅[我的第一个 PowerShell 运行簿](automation-first-runbook-textual-powershell.md)。

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

1. 如图所示登录到 Azure。 之后，系统会提示您输入 Azure 凭据。

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >对于 PowerShell 运行`Add-AzAccount`簿`Add-AzureRMAccount`，并且是`Connect-AzAccount`的别名。 请注意，这些别名不适用于图形运行簿。 图形运行簿只能使用`Connect-AzAccount`自身。

1. 获取保存的 JSON 文件的内容并将其转换为字符串。 `JsonPath`指示保存 JSON 文件的路径。

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. 将 的`$json`字符串内容转换为 PowerShell 对象。

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. 为 的参数创建哈希表`Start-AzAutomationRunbook`。 

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

* 有关以不同方式启动 Runbook 的详细信息，请参阅[启动 Runbook](automation-starting-a-runbook.md)。
* 若要编辑文本 Runbook，请参阅[编辑文本 Runbook](automation-edit-textual-runbook.md)。
* 若要编辑图形 Runbook，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。
