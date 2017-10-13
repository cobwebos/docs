---
title: "Azure 自动化中的连接资产 | Microsoft Docs"
description: "Azure 自动化中的连接资产包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 本文介绍了有关连接的详细信息，以及如何在文本和图形创作中使用连接。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: f0239017-5c66-4165-8cca-5dcb249b8091
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/13/2017
ms.author: magoedte; bwren
ms.openlocfilehash: f1746f4f6706835d43edc171b03d4ececfa3560c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connection-assets-in-azure-automation"></a>Azure 自动化中的连接资产

自动化连接资产包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的信息。 除 URL 和端口等连接信息外，还包括身份验证所需的信息，如用户名和密码。 使用连接的值用于连接一个特定应用程序的所有属性保留在一个资产中，而不是创建多个变量。 用户可以从一个位置编辑连接的值，并且可以在单个参数中将连接名称传递给 Runbook 或 DSC 配置。 可在 Runbook 或 DSC 配置中使用 **Get-AutomationConnection** 活动访问连接的属性。 

创建连接时，必须指定“连接类型”。 连接类型是定义了一组属性的模板。 连接为其连接类型中定义的每个属性定义值。 连接类型通过集成模块添加到 Azure 自动化，或使用 [Azure 自动化 API](http://msdn.microsoft.com/library/azure/mt163818.aspx) 进行创建，前提是集成模块包含连接类型，并且已导入到自动化帐户中。 否则，需创建指定自动化连接类型的元数据文件。  此方面的详细信息，请参阅[集成模块](automation-integration-modules.md)。  

>[!NOTE] 
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥由主证书加密，并存储在 Azure 自动化中。 在存储安全资产之前，会先使用主证书来解密自动化帐户的密钥，然后使用该密钥来加密资产。

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet

下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化连接。 可在自动化 Runbook 和 DSC 配置中使用的 [Azure PowerShell 模块](/powershell/azure/overview)已随附了这些 cmdlet。

|Cmdlet|说明|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|检索连接。 包括一个哈希表，其中包括连接的字段的值。|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|创建新连接。|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|删除现有连接。|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|设置现有连接的一个特定字段的值。|

## <a name="activities"></a>活动

下表中的活动用于在 Runbook 或 DSC 配置中访问连接。

|活动|说明|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|获取要使用的连接。 返回包括该连接属性的哈希表。|

>[!NOTE] 
>应避免在 **Get- AutomationConnection** 的 -Name 参数中使用变量，因为这可能会使设计时发现 Runbook 或 DSC 配置与连接资产之间的依赖关系变得复杂化。

 
## <a name="python2-functions"></a>Python2 函数 
下表中的函数用于在 Python2 Runbook 中访问连接。 

| 函数 | 说明 | 
|:---|:---| 
| automationassets.get_automation_connection | 检索连接。 返回包括该连接属性的字典。 | 

> [!NOTE] 
> 必须在 Python Runbook 顶部导入“automationassets”模块才能访问资产函数。

## <a name="creating-a-new-connection"></a>创建新连接

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>使用 Azure 门户创建新连接

1. 在自动化帐户中，单击“资产”部分以打开“资产”边栏选项卡。
2. 单击“连接”部分以打开“连接”边栏选项卡。
3. 单击边栏选项卡顶部的“添加连接”。
4. 在“类型”下拉列表中，选择想要创建的连接类型。 表单会显示该特定类型的属性。
5. 完成该表单，并单击“创建”以保存新连接。

### <a name="to-create-a-new-connection-with-the-azure-classic-portal"></a>使用 Azure 经典门户创建新连接

1. 在自动化帐户中，单击窗口顶部的“资产”。
2. 在窗口底部，单击“添加设置”。
3. 单击“添加连接”。
4. 在“连接类型”下拉列表中，选择想要创建的连接类型。  向导会显示该特定类型的属性。
5. 完成该向导并单击该复选框以保存新连接。

### <a name="to-create-a-new-connection-with-windows-powershell"></a>使用 Windows PowerShell 创建新连接

使用 Windows PowerShell 通过 [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) cmdlet 创建新连接。 此 cmdlet 有一个名为 **ConnectionFieldValues** 的参数，预期为一个[哈希表](http://technet.microsoft.com/library/hh847780.aspx)，用于为连接类型定义的每个属性定义值。

如果熟悉自动化的[运行方式帐户](automation-sec-configure-azure-runas-account.md)（可使用服务主体对 Runbook 进行身份验证），可以使用 PowerShell 脚本（在从门户创建运行方式帐户时作为替代方法提供）通过以下示例命令创建新的连接资产。  

    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 

可以使用脚本创建连接资产，因为在创建自动化帐户时，该帐户默认情况下会在使用连接类型 **AzurServicePrincipal** 时自动包括多个全局模块，以便创建 **AzureRunAsConnection** 连接资产。  牢记这一点很重要，因为如果尝试使用其他身份验证方法创建新的连接资产来连接到服务或应用程序，则会失败，原因在于连接类型尚未在自动化帐户中定义。  如需详细了解如何通过 [PowerShell 库](https://www.powershellgallery.com)为自定义模块创建自己的连接类型，请参阅[集成模块](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 配置中使用连接

请使用 **Get-AutomationConnection** cmdlet 检索 Runbook 或 DSC 配置中的连接。  不能使用 [Get-AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn) 活动。  此活动检索连接中的不同字段的值，并将它们作为[哈希表](http://go.microsoft.com/fwlink/?LinkID=324844)返回，该哈希表随后可用于 Runbook 或 DSC 配置中的相应命令。

### <a name="textual-runbook-sample"></a>文本 Runbook 示例

以下示例命令演示如何使用前述的运行方式帐户在 Runbook 中通过 Azure Resource Manager 资源进行身份验证。  此示例使用代表运行方式帐户的连接资产，该帐户引用基于证书的服务主体而非凭据。  

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint 

### <a name="graphical-runbook-samples"></a>图形 Runbook 示例

在图形编辑器的“库”窗格中，右键单击连接，并选择“添加到画布”将 **Get-AutomationConnection** 活动添加到图形 Runbook。

![](media/automation-connections/connection-add-canvas.png)

下图显示了在图形 Runbook 中使用连接的示例。  这是上面显示的同一示例，可以使用运行方式帐户通过文本 Runbook 进行身份验证。  此示例使用**常量值**数据集执行**获取 RunAs 连接**活动，该活动使用连接对象进行身份验证。  此处使用了一个[管道链接](automation-graphical-authoring-intro.md#links-and-workflow)，因为 ServicePrincipalCertificate 参数集需要单个对象。

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 Runbook 示例
下图演示了如何在 Python2 Runbook 中使用运行方式连接进行身份验证。

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
        pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

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
    runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
    azure_credential = get_automation_runas_credential(runas_connection)

## <a name="next-steps"></a>后续步骤

- 查看[图形创作中的链接](automation-graphical-authoring-intro.md#links-and-workflow)，了解如何引导和控制 Runbook 中的逻辑流。  

- 要详细了解 Azure 自动化如何使用 PowerShell 模块，以及如何根据最佳实践创建自己的 PowerShell 模块（充当 Azure 自动化中的集成模块），请参阅[集成模块](automation-integration-modules.md)。  
