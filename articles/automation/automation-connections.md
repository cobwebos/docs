---
title: 在 Azure 自动化中管理连接
description: 本文介绍如何管理与外部服务或应用程序之间的 Azure 自动化连接，以及如何在 runbook 中使用它们。
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 0a3cff616f814b8e5209b15f9d3f7439533452ca
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071755"
---
# <a name="manage-connections-in-azure-automation"></a>在 Azure 自动化中管理连接

Azure 自动化连接资产包含下面列出的信息。 从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 

* 身份验证所需的信息，如用户名和密码
* 连接信息，如 URL 或端口

连接资产将用于与特定应用程序进行连接的所有属性汇集在一起，这样就不必创建多个变量。 你可以从一个位置编辑连接的值，并且可以在单个参数中将连接名称传递给 Runbook 或 DSC 配置。 runbook 或配置会使用内部 `Get-AutomationConnection`cmdlet 访问连接的属性。

创建连接时，必须指定“连接类型”。 连接类型是定义了一组属性的模板。 可以使用带有元数据文件的集成模块向 Azure 自动化添加连接类型。 如果集成模块包含连接类型并导入到自动化帐户中，也可以使用 [Azure 自动化 API](/previous-versions/azure/reference/mt163818(v=azure.100)) 创建连接类型。 

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥进行加密并存储在 Azure 自动化中。 Azure 自动化将密钥存储在系统管理的 Key Vault 中。 在存储安全资产之前，自动化会从 Key Vault 加载密钥，然后使用该密钥加密资产。 

## <a name="connection-types"></a>连接类型

Azure 自动化提供了以下内置连接类型：

* `Azure` - 表示用于管理经典资源的连接。
* `AzureServicePrincipal` -表示 Azure 运行方式帐户使用的连接。
* `AzureClassicCertificate` -表示经典 Azure 运行方式帐户使用的连接。

在大多数情况下不需要创建连接资源，因为在创建 [RunAs 帐户](manage-runas-account.md)时已经创建了该连接。

## <a name="powershell-cmdlets-to-access-connections"></a>用于访问连接的 PowerShell cmdlet

下表中的 cmdlet 使用 PowerShell 创建和管理自动化连接。 它们作为 [Az 模块](shared-resources/modules.md#az-modules)的一部分提供。

|Cmdlet|说明|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|检索有关连接的信息。|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|创建新连接。|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|删除现有连接。|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|设置现有连接的一个特定字段的值。|

## <a name="internal-cmdlets-to-access-connections"></a>用于访问连接的内部 cmdlet

下表中的内部 cmdlet 用于访问 runbook 和 DSC 配置中的连接。 此 cmdlet 附带全局模块 `Orchestrator.AssetManagement.Cmdlets`。 有关详细信息，请参阅[内部 cmdlet](shared-resources/modules.md#internal-cmdlets)。

|内部 Cmdlet|说明|
|---|---|
|`Get-AutomationConnection` | 检索连接中不同字段的值，并将其作为[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)返回。 然后，可以将此哈希表与 runbook 或 DSC 配置中的相应命令一起使用。|

>[!NOTE]
>避免将变量与 `Get-AutomationConnection` 的 `Name` 参数一起使用。 如果这样使用变量，可能会导致在设计时尝试发现 runbook 或 DSC 配置与连接资产之间的依赖关系变得复杂。

## <a name="python-2-functions-to-access-connections"></a>用于访问连接的 Python 2 函数

下表中的函数用于在 Python2 Runbook 中访问连接。

| 函数 | 说明 |
|:---|:---|
| `automationassets.get_automation_connection` | 检索连接。 返回包括该连接属性的字典。 |

> [!NOTE]
> 必须在 Python Runbook 顶部导入 `automationassets` 模块才能访问资产函数。

## <a name="create-a-new-connection"></a>创建新连接

### <a name="create-a-new-connection-with-the-azure-portal"></a>使用 Azure 门户创建新连接

使用 Azure 门户创建新连接：

1. 在自动化帐户中，单击“共享资源”下的“连接” 。
2. 单击“连接”页上的“+ 添加连接”。
4. 在“新建连接”窗格的“类型”字段中，选择要创建的连接类型。 你的选择是 `Azure`、`AzureServicePrincipal` 和 `AzureClassicCertificate`。 
5. 该窗体显示所选连接类型的属性。 完成该表单，并单击“创建”以保存新连接。

### <a name="create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 创建新连接

使用 `New-AzAutomationConnection` cmdlet 通过 Windows PowerShell 创建新连接。 此 cmdlet 有一个 `ConnectionFieldValues` 参数，预期值为哈希表，用于为连接类型定义的每个属性定义值。

可以使用以下示例命令作为“从门户创建运行方式帐户”的替代方法，以创建新的连接资产。

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

创建自动化帐户时，默认情况下，它包括几个全局模块，以及用于创建 `AzureRunAsConnection` 连接资产的连接类型 `AzureServicePrincipal`。 如果尝试使用其他身份验证方法创建新的连接资产来连接到服务或应用程序，操作将失败，原因在于连接类型尚未在自动化帐户中定义。 有关为自定义模块创建自己的连接类型的详细信息，请参阅[添加连接类型](#add-a-connection-type)。

## <a name="add-a-connection-type"></a>添加连接类型

如果 runbook 或 DSC 配置连接到外部服务，需要在名为集成模块的[自定义模块](shared-resources/modules.md#custom-modules)中定义连接类型。 此模块包含一个元数据文件，该文件名为“&lt;ModuleName&gt;-Automation.json”，用于指定连接类型属性，位于 .zip 压缩文件的模块文件夹中 。 此文件包含连接到模块所代表的系统或服务所需的连接的字段。 使用此文件，可以设置连接类型的字段名、数据类型、加密状态和可选状态。 

以下示例是“.json”文件格式的模板，该模板定义名为 `MyModuleConnection` 的自定义连接类型的用户名和密码属性：

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 配置中获取连接

请使用 `Get-AutomationConnection` cmdlet 检索 Runbook 或 DSC 配置中的连接。 此 cmdlet 优先于 `Get-AzAutomationConnection` cmdlet，因为它检索连接值而不是有关连接的信息。 

### <a name="textual-runbook-example"></a>文本 runbook 示例

以下示例演示如何使用运行方式帐户在 runbook 中通过 Azure 资源管理器资源进行身份验证。 此示例使用代表运行方式帐户的连接资产，该帐户引用基于证书的服务主体。

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>图形 runbook 示例

可以为内部 `Get-AutomationConnection` cmdlet 添加一个活动到图形 runbook 中。 右键单击图形编辑器库窗格中的连接，然后选择“添加到画布”。

![添加到画布](media/automation-connections/connection-add-canvas.png)

下图显示了在图形 Runbook 中使用连接对象的示例。 此示例使用 `Constant value` 数据集执行 `Get RunAs Connection` 活动，该活动使用连接对象进行身份验证。 此处使用了一个[管道链接](automation-graphical-authoring-intro.md#use-links-for-workflow)，因为 `ServicePrincipalCertificate` 参数需要单个对象。

![获取连接](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Python 2 runbook 示例

下图演示了如何在 Python 2 Runbook 中使用运行方式连接进行身份验证。

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

* 若要了解有关用于访问连接的 cmdlet 的详细信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。
* 有关 runbook 的常规信息，请参阅[在 Azure 自动化中执行 Runbook](automation-runbook-execution.md)。
* 有关 DSC 配置的详细信息，请参阅[状态配置概述](automation-dsc-overview.md)。