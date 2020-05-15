---
title: 从 Azure 自动化 runbook 发送电子邮件
description: 了解如何使用 SendGrid 从 runbook 中发送电子邮件。
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 4d825dee469497cbb56a91c913ff3ac51963058b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855684"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>教程：从 Azure 自动化 runbook 发送电子邮件

可以使用 PowerShell 通过 [SendGrid](https://sendgrid.com/solutions) 从 runbook 发送电子邮件。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 创建 Azure 密钥保管库。
> * 将 `SendGrid` API 密钥存储在密钥保管库中。
> * 创建一个可重用 runbook，该 runbook 检索你的 API 密钥并使用存储在 [Azure Key Vault](/azure/key-vault/) 中的 API 密钥发送电子邮件。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="prerequisites"></a>先决条件

完成本教程需要以下各项：

* Azure 订阅：如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [创建 SendGrid 帐户](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account)。
* [自动化帐户](automation-offering-get-started.md)和 **Az** 模块以及[运行方式连接](automation-create-runas-account.md)，用于存储和执行 runbook。

## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

可以使用以下 PowerShell 脚本创建 Azure Key Vault。 将变量值替换为特定于环境的值。 单击代码块右上角的**试运行**按钮，使用嵌入的 Azure Cloud Shell。 也可复制代码并在本地运行它，前提是已在本地计算机上安装 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

> [!NOTE]
> 若要检索 API 密钥，请使用在[查找 SendGrid API 密钥](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)中发现的步骤。

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

若要通过其他方式创建 Azure Key Vault 并存储机密，请参阅 [Key Vault 快速入门](/azure/key-vault/)。

## <a name="import-required-modules-to-your-automation-account"></a>将所需模块导入自动化帐户

若要在 runbook 中使用 Azure Key Vault，自动化帐户需要以下模块：

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

在“Azure 自动化”选项卡上的“安装选项”  下，单击“部署到 Azure 自动化”  。 此操作会打开 Azure 门户。 在“导入”页上，选择你的自动化帐户并单击“确定”  。

如需通过其他方法来添加所需模块，请参阅[导入模块](/azure/automation/shared-resources/modules#importing-modules)。

## <a name="create-the-runbook-to-send-an-email"></a>创建用于发送电子邮件的 runbook

在创建密钥保管库并存储 `SendGrid` API 密钥后，即可创建 runbook 来检索 API 密钥和发送电子邮件。

此 Runbook 使用 `AzureRunAsConnection` 以[运行方式帐户](automation-create-runas-account.md)身份通过 Azure 进行身份验证，以便从 Azure Key Vault 检索机密。

使用此示例创建名为 **Send-GridMailMessage** 的 Runbook。 可以修改此 PowerShell 脚本，并将其重用于不同的方案。

1. 转到 Azure 自动化帐户。
2. 在“过程自动化”下，选择“Runbook”。  
3. 在 Runbook 列表的顶部选择“+ 创建 Runbook”。 
4. 在“添加 Runbook”  页上，输入 **Send-GridMailMessage** 作为 Runbook 名称。 对于 runbook 类型，选择“PowerShell”  。 然后选择“创建”  。
   ![创建 Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. 此时会创建 Runbook 并打开“编辑 PowerShell Runbook”页面。 
   ![编辑 Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. 将以下 PowerShell 示例复制到“编辑”页中。  确保 `$VaultName` 是你在创建密钥保管库时指定的名称。

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. 选择“发布”  以保存并发布 Runbook。

若要验证 runbook 是否成功执行，可以按[测试 runbook](manage-runbooks.md#test-a-runbook) 或[启动 runbook](start-runbooks.md) 下的步骤操作。
如果一开始看不到测试电子邮件，请检查 **Junk** 和 **Spam** 文件夹。

## <a name="clean-up"></a>清理

不再需要 Runbook 时，即可将其删除。 为此，请在 Runbook 列表中选择 Runbook，然后单击“删除”。 

使用 [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) cmdlet 删除密钥保管库。

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

* 有关如何创建或启动 Runbook 的问题，请参阅[排查 Runbook 错误](./troubleshoot/runbooks.md)。
* 若要更新自动化帐户中的模块，请参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)。
* 若要监视 runbook 执行，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。
* 若要使用警报触发 Runbook，请参阅[使用警报触发 Azure 自动化 Runbook](automation-create-alert-triggered-runbook.md)。
