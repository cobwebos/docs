---
title: "Azure 自动化中的证书资产 | Microsoft Docs"
description: "可以安全地将证书存储在 Azure 自动化中，以便可以通过 Runbook 或 DSC 配置访问这些证书，对 Azure 和第三方资源进行身份验证。  本文介绍了有关证书的详细信息，以及如何在文本和图形创作中使用证书。"
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: b6a5ff4fa3fd0084fd910968651c6ae0fefaf2cf
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="certificate-assets-in-azure-automation"></a>Azure 自动化中的证书资产

可以安全地将证书存储在 Azure 自动化中，以便可以使用 Azure 资源管理器资源的 Get-AzureRmAutomationCertificate 活动通过 Runbook 或 DSC 配置访问这些证书。 这样，便可以创建使用证书进行身份验证的 Runbook 和 DSC 配置，或者将证书添加到 Azure 或第三方资源。

> [!NOTE] 
> Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥由主证书加密，并存储在 Azure 自动化中。 在存储安全资产之前，会先使用主证书来解密自动化帐户的密钥，然后使用该密钥来加密资产。
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet

下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化证书资产。 可在自动化 Runbook 和 DSC 配置中使用的 [Azure PowerShell 模块](../powershell-install-configure.md)已随附了这些 cmdlet。

|Cmdlet|说明|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-4.3.1)|检索有关要在 Runbook 或 DSC 配置中使用的证书的信息。 只能从 Get-AutomationCertificate 活动中检索证书本身。|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate?view=azurermps-4.3.1)|将新证书创建到 Azure 自动化中。|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate?view=azurermps-4.3.1)|从 Azure自动化中删除证书。|将新证书创建到 Azure 自动化中。
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate?view=azurermps-4.3.1)|设置现有证书的属性，包括上传证书文件和设置 .pfx 的密码。|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|为指定的云服务上传服务证书。|


## <a name="python2-functions"></a>Python2 函数

下表中的函数用于在 Python2 Runbook 中访问证书。

| 函数 | 说明 |
|:---|:---|
| automationassets.get_automation_certificate | 检索有关证书资产的信息。 |

> [!NOTE]
> 必须在 Python Runbook 开头部分导入 automationassets 模块才能访问资产函数。


## <a name="creating-a-new-certificate"></a>创建新证书

创建新证书时，需要将 .cer 或 .pfx 文件上传到 Azure 自动化。 将证书标记为可导出后，可以将其转出 Azure 自动化证书存储区。 如果证书不可导出，则它只可用于在 Runbook 或 DSC 配置中签名。


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 门户创建新证书

1. 在自动化帐户中，单击“资产”磁贴以打开“资产”边栏选项卡。
1. 单击“证书”磁贴打开“证书”边栏选项卡。
1. 单击边栏选项卡顶部的“添加证书”。
2. 在“名称”框中键入证书的名称。
2. 单击“上传证书文件”下面的“选择文件”，并浏览到 .cer 或.pfx 文件。  如果选择了 .pfx 文件，请指定密码，以及它是否允许导出。
1. 单击“创建”以保存新的证书资产。


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>使用 Windows PowerShell 创建新证书

以下示例演示了如何创建新的自动化证书并将其标记为可导出。 这会导入现有的 .pfx 文件。

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>使用证书

必须通过 **Get-AutomationCertificate** 活动来使用证书。 不能使用 [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) cmdlet，因为它返回有关证书资产的信息，而不是证书本身的信息。

### <a name="textual-runbook-sample"></a>文本 Runbook 示例

以下示例代码演示了如何将证书添加到 Runbook 中的云服务。 在此示例中，已从加密的自动化变量检索了密码。

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>图形 Runbook 示例

通过在图形编辑器的“库”窗格中右键单击证书并选择“添加到画布”，将 **Get-AutomationCertificate** 添加到图形 Runbook。

![将证书添加到画布](media/automation-certificates/automation-certificate-add-to-canvas.png)

下图显示了在图形 Runbook 中使用证书的示例。  这与上面演示的从文本 Runbook 向云服务添加证书的示例相同。

![示例图形创作 ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 示例
以下示例演示了如何在 Python2 Runbook 中访问证书。

    # get a reference to the Azure Automation certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    
    # returns the binary cert content  
    print cert 

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何使用链接控制 runbook 旨在执行的活动的逻辑流，请参阅[图形创作中的链接](automation-graphical-authoring-intro.md#links-and-workflow)。 
