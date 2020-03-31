---
title: 通过 Webhook 启动 Azure 自动化 Runbook
description: 一个可供客户端通过 HTTP 调用在 Azure 自动化中启动 Runbook 的 Webhook。  本文介绍了如何创建 Webhook，以及如何通过调用 Webhook 来启动 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367017"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>通过 Webhook 启动 Azure 自动化 Runbook

Webhook 允许外部服务通过单个 HTTP 请求在 Azure 自动化中启动特定 Runbook。 外部服务包括 Azure DevOps 服务、GitHub、Azure 监视器日志和自定义应用程序。 此类服务可以使用 Webhook 启动 Runbook，而无需使用 Azure 自动化 API 实现完整解决方案。 您可以将 Webhook 与在[Azure 自动化 中启动 Runbook](automation-starting-a-runbook.md)中启动 Runbook 的其他方法进行比较。

> [!NOTE]
> 不支持使用 Webhook 启动 Python runbook。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="webhook-properties"></a>网钩属性

下表介绍了必须为 webhook 配置的属性。

| properties | 描述 |
|:--- |:--- |
| “属性” |网钩的名称。 您可以提供所需的任何名称，因为它不会暴露给客户端。 它只用来标识 Azure 自动化中的 Runbook。 最好是为 Webhook 提供一个名称，该名称需要与使用它的客户端相关。 |
| 代码 |网址的网址。 这是客户端使用 HTTP POST 调用的唯一地址，用于启动链接到 Webhook 的 Runbook。 它是在创建 Webhook 时自动生成的。 无法指定自定义 URL。 <br> <br> 该 URL 包含一个安全令牌，允许第三方系统调用 Runbook，无需进一步身份验证。 因此，您应该将 URL 视为密码。 出于安全原因，您只能在创建 Webhook 时在 Azure 门户中查看 URL。 请将保存在安全位置的 URL 记下来，供将来使用。 |
| 到期日期 | Webhook 的到期日期，之后不能再使用它。 只要 Webhook 尚未过期，就可以在创建 Webhook 后修改到期日期。 |
| 已启用 | 设置，指示创建 Webhook 时是否默认启用。 如果将此属性设置为"已禁用"，则任何客户端都无法使用 Webhook。 您可以在创建 Webhook 或 Webhook 创建后的任何其他时间设置此属性。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook 启动 Runbook 时使用的参数

Webhook 可以为 Runbook 开始时使用的 Runbook 参数定义值。 Webhook 必须包含任何必需 Runbook 参数的值，并且可以包含可选参数的值。 即使在创建 Webhook 后，也可以修改配置为 Webhook 的参数值。 链接到单个 Runbook 的多个 Webhook 可以每个 Webbook 参数值不同。 客户端在使用 Webhook 启动 Runbook 时，无法重写在 Webhook 中定义的参数值。

要从客户端接收数据，Runbook 支持名为 的`WebhookData`单个参数。 此参数定义包含客户端在 POST 请求中包含的数据的对象。

![Webhook 数据属性](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 参数具有以下属性：

| properties | 描述 |
|:--- |:--- |
| `WebhookName` | 网钩的名称。 |
| `RequestHeader` | 包含传入 POST 请求标头的哈希表。 |
| `RequestBody` | 传入 POST 请求的正文。 此正文保留任何数据格式，如字符串、JSON、XML 或表单编码。 编写的 Runbook 必须能够与预期的数据格式配合工作。 |

不支持`WebhookData`该参数不需要 Webhook 的配置，并且运行簿不需要接受它。 如果 Runbook 未定义参数，则忽略从客户端发送的请求的任何详细信息。

> [!NOTE]
> 调用 Webhook 时，客户端应始终存储任何参数值，以防调用失败。 如果存在网络中断或连接问题，应用程序无法检索失败的 Webhook 调用。

如果在 Webhook 创建`WebhookData`时指定值，则当 Webhook 使用客户端 POST 请求中的数据启动 Runbook 时，将重写该值。 即使应用程序不包含请求正文中的任何数据，也会发生这种情况。 

如果启动`WebhookData`使用 Webhook 以外的机制定义的 Runbook，则可以为`WebhookData`Runbook 识别的值。 此值应是具有与`WebhookData`参数相同的[属性](#webhook-properties)的对象，以便 Runbook 可以处理它，就像它处理 Webhook 传递的实际`WebhookData`对象一样。

例如，如果要从 Azure 门户启动以下 Runbook，并且想要传递一些示例 Webhook 数据进行测试，则必须在用户界面中传递 JSON 中的数据。

![UI 中的 WebhookData 参数](media/automation-webhooks/WebhookData-parameter-from-UI.png)

对于下一个 Runbook 示例，让我们为`WebhookData`定义以下属性：

* **网钩名称**： 我的网络钩
* **请求正文**：`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

现在我们在 UI 中传递以下 JSON 对象`WebhookData`。 此示例使用回车符和换行符，与从 Webhook 传入的格式匹配。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI 中的启动 WebhookData 参数](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure 自动化使用 Runbook 作业记录所有输入参数的值。 因此，客户端在 Webhook 请求中提供的任何输入都记录下来，并且可供有权访问自动化作业的任何人使用。 因此，在 Webhook 调用中包括敏感信息时，应该特别小心。

## <a name="webhook-security"></a>网络钩安全性

Webhook 的安全性依赖于其 URL 的隐私，该 URL 包含允许调用 Webhook 的安全令牌。 Azure 自动化不对请求执行任何身份验证，只要对正确的 URL 进行身份验证。 因此，如果不使用验证请求的替代方法，客户端不应对执行高度敏感操作的 Runbook 使用 Webhook。

可以在 Runbook 中包含逻辑，以确定它是否由 Webhook 调用。 让 Runbook 检查`WebhookName`参数的属性`WebhookData`。 Runbook 可以通过在`RequestHeader`和`RequestBody`属性中查找特定信息来执行进一步验证。

另一种策略是让 Runbook 在收到 Webhook 请求时对外部条件执行某些验证。 例如，在 GitHub 向 GitHub 存储库提交新时，请考虑 GitHub 调用的运行手册。 Runbook 可能会连接到 GitHub 以验证在继续之前是否发生了新的提交。

## <a name="creating-a-webhook"></a>创建 Webhook

在 Azure 门户中使用以下过程来创建新的链接到 Runbook 的 Webhook。

1. 在 Azure 门户中的 Runbook 页中，单击 Webhook 开始查看 Runbook 详细信息的 Runbook。 确保 Runbook**状态**字段设置为 **"已发布**"。
2. 单击页顶部的“Webhook”以打开“添加 Webhook”页****。
3. 单击 **"创建新网页钩子**"以打开"创建网页"
4. 填写 Webhook**的名称**和**到期日期**字段，并指定是否应启用该字段。 有关这些属性的详细信息，请参阅[Webhook 属性](#webhook-properties)。
5. 单击复制图标，并按 Ctrl+C 以复制 Webhook 的 URL。 然后，将其记录在某个安全的位置。 

    > [!NOTE]
    > 一旦创建 Webhook，你就不能再次检索该 URL。

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. 单击“参数”**** 为 Runbook 参数提供值。 如果 Runbook 具有必需的参数，则除非提供值，否则无法创建 Webhook。
1. 单击“创建”**** 以创建 Webhook。

## <a name="using-a-webhook"></a>使用 Webhook

要在 Webhook 创建后使用它，客户端必须发出带有 Webhook URL 的 HTTP`POST`请求。 语法为：

```http
http://<Webhook Server>/token?=<Token Value>
```

客户端从请求接收以下返回代码之一`POST`。

| 代码 | Text | 描述 |
|:--- |:--- |:--- |
| 202 |已接受 |已接受该请求，并已成功将 Runbook 排队。 |
| 400 |错误的请求 |出于以下原因之一，未接受该请求： <ul> <li>Webhook 已过期。</li> <li>Webhook 已禁用。</li> <li>URL 中的令牌无效。</li>  </ul> |
| 404 |未找到 |出于以下原因之一，未接受该请求： <ul> <li>找不到 Webhook。</li> <li>找不到 Runbook。</li> <li>找不到帐户。</li>  </ul> |
| 500 |内部服务器错误 |URL 有效，但出现了错误。 请重新提交请求。 |

假设请求成功，Webhook 响应将包含 JSON 格式的作业 ID，如下所示。 它包含单个作业 ID，但 JSON 格式允许将来进行可能的增强。

```json
{"JobIds":["<JobId>"]}
```

客户端无法从 Webhook 确定 Runbook 的作业何时完成或其完成状态。 它可以使用作业 ID 与其他机制（如[Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob)或[Azure 自动化 API）](/rest/api/automation/job)一起查找此信息。

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>续订网络钩子

创建 Webhook 时，其有效期为 10 年，之后它会自动过期。 网钩过期后，无法重新激活它。 您只能删除并重新创建它。 

您可以扩展尚未达到过期时间的 Webhook。 要扩展网络钩子：

1. 导航到包含 Webhook 的 Runbook。 
2. 选择“资源”****下的“Webhook”****。 
3. 单击要扩展的 Webhook。 
4. 在 Webhook 页中，选择新的到期日期和时间，然后单击"**保存**"。

## <a name="sample-runbook"></a>示例 Runbook

以下示例 Runbook 将接受 Webhook 数据，并启动请求正文中指定的虚拟机。 要测试此 Runbook，请在**Runbook**下的自动化帐户中单击"**创建运行簿**"。 如果不知道如何创建 Runbook，请参阅[创建 Runbook](automation-quickstart-create-runbook.md)。

> [!NOTE]
> 对于非图形 PowerShell 运行簿`Add-AzAccount`，`Add-AzureRMAccount`并且是[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的别名。 您可以使用这些 cmdlet，也可以将自动化帐户中的[模块更新](automation-update-azure-modules.md)到最新版本。 即使您刚刚创建了新的自动化帐户，您也可能需要更新模块。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>测试示例

以下示例使用 Windows PowerShell 并配合 Webhook 来启动 Runbook。 任何可以发出 HTTP 请求的语言都可以使用 Webhook。 此处使用 Windows PowerShell 作为示例。

Runbook 预期请求的正文中包含 JSON 格式的虚拟机列表。 Runbook 还验证标头是否包含已定义的消息，以验证 Webhook 调用方是否有效。

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

下面的示例显示 对`RequestBody`属性 中的 Runbook 可用的请求正文。 `WebhookData` 此值在 JSON 中格式化，以便与请求正文中包含的格式兼容。

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

下图显示了从 Windows PowerShell 发送的请求以及生成的响应。 作业 ID 从响应中提取，并转换为字符串。

![Webhook 按钮](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>后续步骤

* 要了解如何使用 Azure 自动化对 Azure 警报执行操作，请参阅[使用警报触发 Azure 自动化运行簿](automation-create-alert-triggered-runbook.md)。
