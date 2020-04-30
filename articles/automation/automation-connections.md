---
title: 在 Azure 自动化中管理连接
description: Azure 自动化中的连接资产包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 本文介绍了有关连接的详细信息，以及如何在文本和图形创作中使用连接。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097109"
---
# <a name="manage-connections-in-azure-automation"></a>在 Azure 自动化中管理连接

自动化连接资产包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 除 URL 和端口等连接信息外，还包括身份验证所需的信息，如用户名和密码。 使用连接的值用于连接一个特定应用程序的所有属性保留在一个资产中，而不是创建多个变量。 用户可以从一个位置编辑连接的值，并且可以在单个参数中将连接名称传递给 Runbook 或 DSC 配置。 可以通过`Get-AutomationConnection`活动在 RUNBOOK 或 DSC 配置中访问连接的属性。

创建连接时，必须指定连接类型。 连接类型是定义了一组属性的模板。 连接为其连接类型中定义的每个属性定义值。 连接类型通过集成模块添加到 Azure 自动化，或使用 [Azure 自动化 API](/previous-versions/azure/reference/mt163818(v=azure.100)) 进行创建，前提是集成模块包含连接类型，并且已导入到自动化帐户中。 否则，必须创建元数据文件来指定自动化连接类型。 有关此内容的详细信息，请参阅[集成模块](automation-integration-modules.md)。

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产使用为每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥存储在系统托管的密钥保管库中。 在存储安全资产之前，从密钥保管库加载密钥，然后使用该密钥加密资产。 此过程由 Azure 自动化管理。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="connection-types"></a>连接类型

Azure 自动化中提供了三种类型的内置连接：

* **Azure** - 此连接可以用于管理经典资源。
* **AzureClassicCertificate** - AzureClassicRunAs 帐户使用此连接****。
* **AzureServicePrincipal** - AzureRunAs 帐户使用此连接****。

在大多数情况下，不需要创建连接资源，因为它是在创建[运行方式帐户](manage-runas-account.md)时创建的。

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell cmdlet

下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化连接。 它们作为[Azure PowerShell 模块](/powershell/azure/overview)的一部分附带，可在自动化 RUNBOOK 和 DSC 配置中使用。

|Cmdlet|说明|
|---|---|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|检索连接。 包含具有连接字段的值的哈希表。|
|[新-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|创建新连接。|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|删除现有连接。|
|[AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|设置现有连接的一个特定字段的值。|

## <a name="activities"></a>活动

下表中的活动用于在 Runbook 或 DSC 配置中访问连接。

|活动|说明|
|---|---|
|`Get-AutomationConnection` | 获取要使用的连接。 返回包含连接的属性的哈希表。|

>[!NOTE]
>避免将`Name`变量与的参数一起`Get-AutomationConnection`使用。 使用此参数可以在设计时对 runbook 或 DSC 配置与连接资产之间的依赖关系的发现复杂化。

## <a name="python-2-functions"></a>Python 2 功能

下表中的函数用于访问 Python 2 runbook 中的连接。

| 函数 | 说明 |
|:---|:---|
| `automationassets.get_automation_connection` | 检索连接。 返回包括该连接属性的字典。 |

> [!NOTE]
> 必须将`automationassets`模块导入 Python runbook 的顶部，才能访问资产功能。

## <a name="creating-a-new-connection"></a>创建新连接

### <a name="create-a-new-connection-with-the-azure-portal"></a>使用 Azure 门户创建新连接

1. 在自动化帐户中，单击 "**资产**" 部分以打开 "**资产**" 边栏选项卡。
2. 单击“连接”**** 部分以打开“连接”**** 边栏选项卡。
3. 单击边栏选项卡顶部的“添加连接”****。
4. 在“类型”**** 下拉列表中，选择想要创建的连接类型。 表单会显示该特定类型的属性。
5. 完成该表单，并单击“创建”**** 以保存新连接。

### <a name="create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 创建新连接

使用`New-AzAutomationConnection` cmdlet 创建与 Windows PowerShell 的新连接。 此 cmdlet 有一个名为`ConnectionFieldValues`的参数，该参数需要[哈希表](https://technet.microsoft.com/library/hh847780.aspx)为连接类型定义的每个属性定义值。

你可以使用以下示例命令作为从门户创建运行方式帐户的替代方法，以创建新的连接资产。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

创建自动化帐户时，默认情况下它包含多个全局模块以及用于创建`AzureServicePrincipal` `AzureRunAsConnection`连接资产的连接类型。 如果尝试使用其他身份验证方法来创建新的连接资产以连接到服务或应用程序，则操作将失败，因为你的自动化帐户中尚未定义连接类型。 有关为自定义[PowerShell 库](https://www.powershellgallery.com)模块创建自己的连接类型的详细信息，请参阅[集成模块](automation-integration-modules.md)。

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 配置中使用连接

使用`Get-AutomationConnection` cmdlet 检索 RUNBOOK 或 DSC 配置中的连接。 不能使用该`Get-AzAutomationConnection`活动。 此活动检索连接中不同字段的值，并将它们作为[哈希表](https://go.microsoft.com/fwlink/?LinkID=324844)返回。 此哈希表随后可用于 runbook 或 DSC 配置中的相应命令。

### <a name="textual-runbook-sample"></a>文本 Runbook 示例

以下示例命令演示如何使用前述的运行方式帐户在 Runbook 中通过 Azure 资源管理器资源进行身份验证。 此示例使用代表运行方式帐户的连接资产，该帐户引用基于证书的服务主体而非凭据。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> 对于非图形 PowerShell runbook， `Add-AzAccount`和`Add-AzureRMAccount`是[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的别名。 可以使用这些 cmdlet，也可以在自动化帐户中[将模块更新](automation-update-azure-modules.md)为最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新你的模块。

### <a name="graphical-runbook-samples"></a>图形 Runbook 示例

通过在图形`Get-AutomationConnection`编辑器的 "库" 窗格中右键单击该连接，然后选择 "**添加到画布**"，可以向图形 runbook 添加活动。

![添加到画布](media/automation-connections/connection-add-canvas.png)

下图显示了在图形 Runbook 中使用连接的示例。 此示例与上述示例相同，可使用带有文本 runbook 的运行方式帐户进行身份验证。 此示例将使用`Constant value`连接对象的`Get RunAs Connection`活动的数据集用于身份验证。 此处使用[管道链接](automation-graphical-authoring-intro.md#links-and-workflow)，因为`ServicePrincipalCertificate`参数集需要单个对象。

![获取连接](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2 runbook 示例

下面的示例演示如何使用 Python 2 runbook 中的运行方式连接进行身份验证。

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>后续步骤

- 查看[图形创作中的链接](automation-graphical-authoring-intro.md#links-and-workflow)，了解如何引导和控制 Runbook 中的逻辑流。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 要详细了解 Azure 自动化如何使用 PowerShell 模块，以及如何根据最佳实践创建自己的 PowerShell 模块（充当 Azure 自动化中的集成模块），请参阅[集成模块](automation-integration-modules.md)。