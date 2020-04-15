---
title: Azure 自动化中的连接资产
description: Azure 自动化中的连接资产包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 本文介绍了有关连接的详细信息，以及如何在文本和图形创作中使用连接。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 4840b135587ae776cfb80258ce513a48a79efa43
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383335"
---
# <a name="connection-assets-in-azure-automation"></a>Azure 自动化中的连接资产

自动化连接资产包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 除 URL 和端口等连接信息外，还包括身份验证所需的信息，如用户名和密码。 使用连接的值用于连接一个特定应用程序的所有属性保留在一个资产中，而不是创建多个变量。 用户可以从一个位置编辑连接的值，并且可以在单个参数中将连接名称传递给 Runbook 或 DSC 配置。 可以在 Runbook 或 DSC 配置中访问与`Get-AutomationConnection`活动的连接属性。

创建连接时，必须指定*连接类型*。 连接类型是定义了一组属性的模板。 连接为其连接类型中定义的每个属性定义值。 连接类型通过集成模块添加到 Azure 自动化，或使用 [Azure 自动化 API](/previous-versions/azure/reference/mt163818(v=azure.100)) 进行创建，前提是集成模块包含连接类型，并且已导入到自动化帐户中。 否则，必须创建元数据文件以指定自动化连接类型。 有关此的详细信息，请参阅[集成模块](automation-integration-modules.md)。

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产使用为每个自动化帐户生成的唯一密钥在 Azure 自动化中加密和存储。 此密钥存储在系统托管的密钥保管库中。 在存储安全资产之前，从密钥保管库加载密钥，然后使用该密钥加密资产。 此过程由 Azure 自动化管理。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="connection-types"></a>连接类型

Azure 自动化中有三种类型的内置连接：

* **Azure** - 此连接可以用于管理经典资源。
* **AzureClassicCertificate** - AzureClassicRunAs 帐户使用此连接****。
* **AzureServicePrincipal** - AzureRunAs 帐户使用此连接****。

在大多数情况下，您不需要创建连接资源，因为它是在创建["运行方式"帐户](manage-runas-account.md)时创建的。

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell cmdlet

下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化连接。 它们作为[Azure PowerShell 模块](/powershell/azure/overview)的一部分发货，可用于自动化运行簿和 DSC 配置。

|Cmdlet|描述|
|---|---|
|[获取自动化连接](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|检索连接。 包括具有连接字段值的哈希表。|
|[新-阿兹自动化连接](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|创建新连接。|
|[删除-阿兹自动化连接](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|删除现有连接。|
|[设置-阿兹自动化连接场值](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|设置现有连接的一个特定字段的值。|

## <a name="activities"></a>活动

下表中的活动用于在 Runbook 或 DSC 配置中访问连接。

|活动|描述|
|---|---|
|`Get-AutomationConnection` | 获取要使用的连接。 返回包含连接的属性的哈希表。|

>[!NOTE]
>避免将变量与`Name`参数一`Get-AutomationConnection`起使用。 使用此参数会使 Runbook 或 DSC 配置与设计时的连接资产之间的依赖项发现复杂化。

## <a name="python-2-functions"></a>Python 2 函数

下表中的函数用于访问 Python 2 运行簿中的连接。

| 函数 | 说明 |
|:---|:---|
| `automationassets.get_automation_connection` | 检索连接。 返回包括该连接属性的字典。 |

> [!NOTE]
> 您必须在`automationassets`Python runbook 顶部导入模块才能访问资产函数。

## <a name="creating-a-new-connection"></a>创建新连接

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>使用 Azure 门户创建新连接

1. 从自动化帐户中，单击 **"资产**"部分以打开 **"资产**"边栏选项卡。
2. 单击“连接”**** 部分以打开“连接”**** 边栏选项卡。
3. 单击边栏选项卡顶部的“添加连接”****。
4. 在“类型”**** 下拉列表中，选择想要创建的连接类型。 表单会显示该特定类型的属性。
5. 完成该表单，并单击“创建”**** 以保存新连接。

### <a name="to-create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 创建新连接

使用`New-AzAutomationConnection`cmdlet 与 Windows PowerShell 创建新连接。 此 cmdlet 具有一`ConnectionFieldValues`个名为 的参数，该参数需要一个[可哈希值](https://technet.microsoft.com/library/hh847780.aspx)，用于定义由连接类型定义的每个属性的值。

可以使用以下示例命令作为从门户创建"运行方式"帐户以创建新连接资产的替代方法。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

您可以使用该脚本创建连接资产，因为当您创建自动化帐户时，默认情况下它会自动包含多个全局模块以及创建`AzureServicePrincipal``AzureRunAsConnection`连接资产的连接类型。 牢记这一点很重要，因为如果尝试使用其他身份验证方法创建新的连接资产来连接到服务或应用程序，则会失败，原因在于连接类型尚未在自动化帐户中定义。 有关如何从[PowerShell 库](https://www.powershellgallery.com)为自定义或模块创建自己的连接类型的详细信息，请参阅[集成模块](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 配置中使用连接

使用 cmdlet 检索 Runbook 或`Get-AutomationConnection`DSC 配置中的连接。 不能使用活动`Get-AzAutomationConnection`。 此活动检索连接中不同字段的值，并将它们作为[可哈希值](https://go.microsoft.com/fwlink/?LinkID=324844)返回。 然后，此哈希表可用于 Runbook 或 DSC 配置中的相应命令。

### <a name="textual-runbook-sample"></a>文本 Runbook 示例

以下示例命令演示如何使用前述的运行方式帐户在 Runbook 中通过 Azure 资源管理器资源进行身份验证。 此示例使用代表运行方式帐户的连接资产，该帐户引用基于证书的服务主体而非凭据。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> 对于非图形 PowerShell 运行簿`Add-AzAccount`，`Add-AzureRMAccount`并且是[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)的别名。 您可以使用这些 cmdlet，也可以将自动化帐户中的[模块更新](automation-update-azure-modules.md)到最新版本。 即使您刚刚创建了新的自动化帐户，您也可能需要更新模块。

### <a name="graphical-runbook-samples"></a>图形 Runbook 示例

通过右键`Get-AutomationConnection`单击图形编辑器的"库"窗格中的连接并选择"**添加到画布**"，将活动添加到图形 Runbook。

![添加到画布](media/automation-connections/connection-add-canvas.png)

下图显示了在图形 Runbook 中使用连接的示例。 这是上面显示的同一示例，可以使用运行方式帐户通过文本 Runbook 进行身份验证。 本示例使用`Constant value`使用连接对象进行身份验证`Get RunAs Connection`的活动的数据集。 此处使用[管道链接](automation-graphical-authoring-intro.md#links-and-workflow)，`ServicePrincipalCertificate`因为参数集需要单个对象。

![获取连接](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2 运行簿示例

下面的示例演示如何在 Python 2 runbook 中使用"运行作为"连接进行身份验证。

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
* 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 要详细了解 Azure 自动化如何使用 PowerShell 模块，以及如何根据最佳实践创建自己的 PowerShell 模块（充当 Azure 自动化中的集成模块），请参阅[集成模块](automation-integration-modules.md)。