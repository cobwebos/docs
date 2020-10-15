---
title: 通过 Webhook 启动 Azure 自动化 Runbook
description: 本文介绍如何使用 Webhook 通过 HTTP 调用在 Azure 自动化中启动 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: db4f49c1b788cd7a55fd6fbbd48f845f2c94d757
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073523"
---
# <a name="start-a-runbook-from-a-webhook"></a>从 Webhook 启动 Runbook

使用 Webhook 可使外部服务在 Azure 自动化中通过单个 HTTP 请求启动特定的 Runbook。 外部服务包括 Azure DevOps Services、GitHub、Azure Monitor 日志和自定义应用程序。 此类服务可以使用 Webhook 启动 Runbook，而无需实现完整的 Azure 自动化 API。 可以将 Webhook 与[在 Azure 自动化中启动 Runbook](./start-runbooks.md) 中其他启动 Runbook 的方法进行比较。

> [!NOTE]
> 不支持使用 Webhook 来启动 Python Runbook。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

若要了解支持 Webhook 的 TLS 1.2 的客户端要求，请参阅[强制 Azure 自动化执行 TLS 1.2](automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

## <a name="webhook-properties"></a>Webhook 属性

下表介绍了必须为 webhook 配置的属性。

| 属性 | 说明 |
|:--- |:--- |
| 名称 |Webhook 的名称。 可以提供任何名称，因为该名称不会公开给客户端。 它只用来标识 Azure 自动化中的 Runbook。 最好是为 Webhook 提供一个名称，该名称需要与使用它的客户端相关。 |
| URL |Webhook 的 URL。 这是客户端通过 HTTP POST 来调用的唯一地址，用于启动链接到 Webhook 的 Runbook。 它是在创建 Webhook 时自动生成的。 无法指定自定义 URL。 <br> <br> URL 包含一个允许第三方系统调用 Runbook 的安全令牌，不需要进一步进行身份验证。 因此，应该将 URL 视为密码。 出于安全原因，只能在创建 Webhook 时通过 Azure 门户查看该 URL。 请将保存在安全位置的 URL 记下来，供将来使用。 |
| 到期日期 | Webhook 的到期日期，该日期之后不能再使用它。 创建 Webhook 后，只要它没有到期，就可以修改到期日期。 |
| 已启用 | 指示 Webhook 是否在创建后默认启用的设置。 如果将此属性设置为“禁用”，则任何客户端都无法使用 Webhook。 可以在创建 Webhook 或 Webhook 创建后的任何其他时间设置此属性。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook 启动 Runbook 时使用的参数

Webhook 可以定义 Runbook 参数的值，当 Runbook 启动时会用到这些值。 Webhook 必须包含 Runbook 的任何必需参数的值，可以包含可选参数的值。 即使在创建 Webhook 后，也可以修改为 Webhook 配置的参数值。 链接到单个 Runbook 的多个 Webhook 可以使用不同的 Runbook 参数值。 客户端在使用 Webhook 启动 Runbook 时，无法重写在 Webhook 中定义的参数值。

若要从客户端接收数据，Runbook 支持一个名为 `WebhookData` 的参数。 此参数定义包含客户端在 POST 请求中包含的数据的对象。

![WebhookData 属性](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 参数具有以下属性：

| 属性 | 说明 |
|:--- |:--- |
| `WebhookName` | Webhook 的名称。 |
| `RequestHeader` | 包含传入 POST 请求标头的哈希表。 |
| `RequestBody` | 传入 POST 请求的主体。 此主体保留任何数据格式，如字符串、JSON、XML 或编码的表单。 编写的 Runbook 必须能够与预期的数据格式配合工作。 |

无需配置 Webhook 即可支持 `WebhookData` 参数，也不需要 Runbook 来接受它。 如果 Runbook 没有定义该参数，则会忽略从客户端发送的请求的任何详细信息。

> [!NOTE]
> 调用 Webhook 时，客户端应始终存储任何参数值，以防调用失败。 如果存在网络中断或连接问题，应用程序无法检索失败的 Webhook 调用。

如果在 Webhook 创建时为 `WebhookData` 指定值，则当 Webhook 使用客户端 POST 请求中的数据启动 Runbook 时，将覆盖该值。 即使应用程序不包含请求主体中的任何数据，也是如此。 

如果启动使用 Webhook 以外的机制定义 `WebhookData` 的 Runbook，则可以为 Runbook 识别的 `WebhookData` 提供值。 此值应该是与 `WebhookData` 参数具有相同[属性](#webhook-properties)的对象，这样 Runbook 就可以使用它，如同使用 Webhook 所传递的实际 `WebhookData` 对象一样。

例如，如果要从 Azure 门户启动以下 Runbook，并想要传递一些示例 Webhook 数据进行测试，则必须在用户界面中以 JSON 格式传递数据。

![UI 中的 WebhookData 参数](media/automation-webhooks/WebhookData-parameter-from-UI.png)

在下一个 Runbook 示例中，让我们为 `WebhookData` 定义以下属性：

* **WebhookName**：MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

现在我们在 UI 中为 `WebhookData` 参数传递以下 JSON 对象。 下面的示例带有回车符和换行符，与从 Webhook 中传递的格式相匹配。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI 中的启动 WebhookData 参数](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure 自动化通过 Runbook 作业记录所有输入参数的值。 因此，客户端在 Webhook 请求中提供的任何输入都将记录下来，并可供有权访问自动化作业的任何人使用。 因此，在 Webhook 调用中包括敏感信息时，应该特别小心。

## <a name="webhook-security"></a>Webhook 安全性

Webhook 的安全性取决于其 URL 的私密性，可以通过 URL 中包含的安全令牌来调用 Webhook。 如果请求是向正确的 URL 发出的，Azure 自动化不会对请求进行任何身份验证。 因此，客户端不应将 Webhook 用于执行高敏感度操作却不使用用于验证请求的替代方法的 Runbook。

请考虑以下策略：

* 可以在 Runbook 中包括逻辑，以确定它是否由 Webhook 调用。 让 Runbook 检查 `WebhookData` 参数的 `WebhookName` 属性。 Runbook 还可以执行进一步的验证，只需在 `RequestHeader` 和 `RequestBody` 属性中查找特定信息即可。

* 让 Runbook 在收到 Webhook 请求时对外部条件执行某种验证。 例如，当有新的内容提交到 GitHub 存储库时，可通过 GitHub 调用 Runbook。 Runbook 在继续之前，可能会连接到 GitHub 来验证是否有新的提交内容。

* Azure 自动化支持 Azure 虚拟网络服务标记，尤其是 [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)。 可以在[网络安全组](../virtual-network/network-security-groups-overview.md#security-rules)或 [Azure 防火墙](../firewall/service-tags.md)中使用服务标记来定义网络访问控制并触发 Webhook。 创建安全规则时，可以使用服务标记代替特定 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 GuestAndHybridManagement），可以允许或拒绝自动化服务的流量。 此服务标记不支持通过将 IP 范围限制到特定区域来实现更精细的控制。

## <a name="create-a-webhook"></a>创建 Webhook

在 Azure 门户中使用以下过程来创建新的链接到 Runbook 的 Webhook。

1. 在 Azure 门户的“Runbook”页中，单击需要通过 Webhook 来启动以查看 Runbook 详细信息页的 Runbook。 确保 Runbook“状态”字段设置为“已发布” 。
2. 单击页顶部的“Webhook”以打开“添加 Webhook”页。
3. 单击“新建 Webhook”，打开“创建 Webhook”页。
4. 填写 Webhook 的“名称”和“到期日期”字段，并指定是否应启用此功能 。 如需详细了解 Webhook 属性，请参阅 [Webhook 属性](#webhook-properties)。
5. 单击复制图标，并按 Ctrl+C 以复制 Webhook 的 URL。 然后，将其记录在某个安全的位置。 

    > [!NOTE]
    > 一旦创建 Webhook，就不能再次检索该 URL。

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. 单击“参数”为 Runbook 参数提供值。 如果 Runbook 包含必需的参数，除非你提供值，否则无法创建 Webhook。

2. 单击“创建”以创建 Webhook。

## <a name="use-a-webhook"></a>使用 Webhook

若要在创建 Webhook 后使用该 Webhook，客户端必须发出带 Webhook URL 的 HTTP `POST` 请求。 语法为：

```http
http://<Webhook Server>/token?=<Token Value>
```

客户端收到从 `POST` 请求返回的以下代码之一。

| 代码 | 文本 | 说明 |
|:--- |:--- |:--- |
| 202 |已接受 |已接受该请求，并已成功将 Runbook 排队。 |
| 400 |错误的请求 |出于以下原因之一，未接受该请求： <ul> <li>Webhook 已过期。</li> <li>Webhook 已禁用。</li> <li>URL 中的令牌无效。</li>  </ul> |
| 404 |未找到 |出于以下原因之一，未接受该请求： <ul> <li>找不到 Webhook。</li> <li>找不到 Runbook。</li> <li>找不到帐户。</li>  </ul> |
| 500 |内部服务器错误 |URL 有效，但出现了错误。 请重新提交请求。 |

假设请求成功，Webhook 响应将包含 JSON 格式的作业 ID，如下所示。 它包含单个作业 ID，但 JSON 格式允许将来可能的增强功能。

```json
{"JobIds":["<JobId>"]}
```

客户端无法从 Webhook 确定 Runbook 的作业何时完成或其完成状态。 可以使用作业 ID 并配合其他机制（例如 [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) 或 [Azure 自动化 API](/rest/api/automation/job)）来了解此信息。

## <a name="renew-a-webhook"></a>续订 Webhook

创建 Webhook 时，其有效期为 10 年，此日期之后它会自动过期。 Webhook 过期后，你无法重新激活它。 只能删除并重新创建它。 

可以扩展尚未达到过期时间的 Webhook。 扩展 Webhook：

1. 请导航到包含 Webhook 的 Runbook。 
2. 选择“资源”****下的“Webhook”****。 
3. 单击要扩展的 Webhook。 
4. 在 Webhook 页中，选择新的到期日期和时间，然后单击“保存”。

## <a name="sample-runbook"></a>示例 Runbook

以下示例 Runbook 将接受 Webhook 数据，并启动请求正文中指定的虚拟机。 若要在“Runbook”下的自动化帐户中测试此 Runbook，请单击“创建 Runbook” 。 如果不知道如何创建 Runbook，请参阅[创建 Runbook](automation-quickstart-create-runbook.md)。

> [!NOTE]
> 对于非图形 PowerShell runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) 的别名。 可以使用这些 cmdlet，也可以在自动化帐户中[将模块更新](automation-update-azure-modules.md)为最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新你的模块。

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

## <a name="test-the-sample"></a>测试示例

以下示例使用 Windows PowerShell 并配合 Webhook 来启动 Runbook。 任何可以发出 HTTP 请求的语言都可以使用 Webhook。 此处使用 Windows PowerShell 作为示例。

Runbook 预期请求的正文中包含 JSON 格式的虚拟机列表。 Runbook 还将验证标头是否包含用于验证 Webhook 调用方是否有效的已定义消息。

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

下面的示例显示请求主体，可在 `WebhookData` 的 `RequestBody` 属性中提供给 Runbook 使用。 此值格式化为 JSON，以与请求正文中包含的格式兼容。

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

* 若要使用警报触发 Runbook，请参阅[使用警报触发 Azure 自动化 Runbook](automation-create-alert-triggered-runbook.md)。