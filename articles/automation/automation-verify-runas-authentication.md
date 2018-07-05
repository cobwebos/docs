---
title: 验证 Azure 自动化帐户配置
description: 本文介绍如何确认自动化帐户的配置设置是正确的。
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: af1d05c171eb5544104b12aebb6c7be937061f6a
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437172"
---
# <a name="test-azure-automation-run-as-account-authentication"></a>测试 Azure 自动化运行方式身份验证
成功创建自动化帐户以后，即可执行一个简单的测试，确认你能够成功地在 Azure 资源管理器部署或 Azure 经典部署中使用新创建的或更新的自动化运行方式帐户进行身份验证。    

## <a name="automation-run-as-authentication"></a>自动化运行方式身份验证
使用以下示例代码[创建 PowerShell runbook](automation-creating-importing-runbook.md)，以便使用运行方式帐户来验证身份，并在自定义 runbook 中使用自动化帐户来验证和管理资源管理器资源。

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Connect-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

请注意，Runbook (Connect-AzureRmAccount) 中用于身份验证的 cmdlet 使用 ServicePrincipalCertificate 参数集。  它使用服务主体证书（而不是凭据）进行身份验证。  

> [!IMPORTANT]
> Add-AzureRmAccount 现在是 Connect-AzureRMAccount 的别名。 搜索库项时，如果未看到 Connect-AzureRMAccount，可以使用 Add-AzureRmAccount，或更新自动化帐户中的模块。

通过[运行 Runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) 来验证运行方式帐户时，会创建一个 [Runbook 作业](automation-runbook-execution.md)并显示“作业”页，然后在“作业摘要”磁贴中显示作业状态。 作业状态最初为“排队”，表示它正在等待云中的 Runbook 辅助角色变为可用  。 在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。  Runbook 作业完成时，应会看到状态变为“已完成”。

若要查看 Runbook 的详细结果，请单击“输出”磁贴。  在“输出”页中，会看到帐户已成功完成身份验证并返回一个列表，其中包含订阅的所有资源组中的所有资源。  

将代码重用于 runbook 时，只需记住删除以注释 `#Get all ARM resources from all resource groups` 开头的代码块即可。

## <a name="classic-run-as-authentication"></a>经典运行方式身份验证
使用以下示例代码[创建 PowerShell runbook](automation-creating-importing-runbook.md)，以便使用经典运行方式帐户来验证身份，并在自定义 runbook 中使用自动化帐户来验证和管理经典部署模型中的资源。  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

通过[运行 Runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) 来验证运行方式帐户时，会创建一个 [Runbook 作业](automation-runbook-execution.md)并显示“作业”页，然后在“作业摘要”磁贴中显示作业状态。 作业状态最初为“排队”，表示它正在等待云中的 Runbook 辅助角色变为可用  。 在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。  Runbook 作业完成时，应会看到状态变为“已完成”。

若要查看 Runbook 的详细结果，请单击“输出”磁贴。  在“输出”页中，会看到帐户已成功完成身份验证并返回一个列表，其中包含部署在订阅中且按 VMName 列出的所有 Azure VM。  

将代码重用于 runbook 时，只需记住删除 cmdlet Get-AzureVM 即可。

## <a name="next-steps"></a>后续步骤
* 若要开始使用 PowerShell Runbook，请参阅[我的第一个 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要了解有关图形创作的详细信息，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。
