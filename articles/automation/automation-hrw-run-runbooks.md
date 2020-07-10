---
title: 在混合 Runbook 辅助角色上运行 Azure 自动化 Runbook
description: 本文介绍如何使用混合 Runbook 辅助角色在本地数据中心或云提供程序的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 22ab982abe9f73aa77cb9bb2c8d3eaa383bc42fb
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186208"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色中运行 Runbook

在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行的 Runbook 通常用于管理本地计算机上的资源，或部署了辅助角色的本地环境中的资源。 Azure 自动化中的 runbook 通常管理 Azure 云中的资源。 即使使用方式不同，在 Azure 自动化中运行的 Runbook 和在混合 Runbook 辅助角色上运行的 Runbook 结构上是相同的。

创建 Runbook 以在混合 Runbook 辅助角色上运行时，应当在承载辅助角色的计算机内编辑并测试 Runbook。 主机具有管理本地资源时所需的所有 PowerShell 模块和网络访问权限。 在混合 Runbook 辅助角色计算机上测试 Runbook 后，可以将它上传到 Azure 自动化环境，用于在混合辅助角色中运行。 

## <a name="plan-runbook-job-behavior"></a>计划 Runbook 作业行为

Azure 自动化处理混合 Runbook 辅助角色上的作业，这与 Azure 沙箱中运行的作业稍有不同。 对于长时间运行的 runbook，请确保它能在重启后复原。 有关作业行为的详细信息，请参阅[混合 Runbook 辅助角色作业](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)。

请记住，混合 Runbook 辅助角色的作业在 Windows 上的本地 System 帐户下运行，或在 Linux 上的 nxautomation 帐户下运行。 对于 Linux，请确保 nxautomation 帐户有权访问存储 Runbook 模块的位置。 使用 [Install-Module](/powershell/module/powershellget/install-module) cmdlet 时，请确保为 `Scope` 参数指定 AllUsers，以确保 nxautomation 帐户具有访问权限。 有关 Linux 上的 PowerShell 的详细信息，请参阅[非 Windows 平台上的 PowerShell 的已知问题](/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)。

## <a name="set-up-runbook-permissions"></a>设置 Runbook 权限

通过以下方式定义 Runbook 的权限以在混合 Runbook 辅助角色上运行：

* 让 Runbook 向本地资源提供自己的身份验证。
* 配置使用 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)进行身份验证。 
* 还可以指定运行方式帐户，为所有 Runbook 提供用户上下文。

## <a name="use-runbook-authentication-to-local-resources"></a>对本地资源使用 Runbook 身份验证

如果准备向资源提供其身份验证的 Runbook，请在 Runbook 中使用[凭据](./shared-resources/credentials.md)和[证书](./shared-resources/certificates.md)资产。 可以通过多个 cmdlet 来指定凭据，以便 Runbook 可以对不同资源进行身份验证。 下面的示例显示了用于重新启动计算机的 Runbook 的一部分。 它从凭据资产检索凭据，从变量资产检索计算机的名称，并将这些值用于 `Restart-Computer` cmdlet。

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

还可以使用 [InlineScript](automation-powershell-workflow.md#use-inlinescript) 活动。 `InlineScript` 允许你在具有凭据的另一台计算机上运行代码块。

## <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>将 Runbook 身份验证与托管标识结合使用

Azure 虚拟机上的混合 Runbook 辅助角色可以使用托管标识来向 Azure 资源进行身份验证。 使用 Azure 资源的托管标识（而不是运行方式帐户）有一些好处，因为无需执行以下操作：

* 导出运行方式证书，再将其导入到混合 Runbook 辅助角色。
* 续订运行方式帐户使用的证书。
* 在 Runbook 代码中处理运行方式连接对象。

遵循以下步骤，在混合 Runbook 辅助角色上使用 Azure 资源的托管标识：

1. 创建 Azure VM。
2. 在 VM 上配置 Azure 资源的托管标识。 请参阅[使用 Azure 门户在 VM 上配置 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。
3. 授予 VM 对资源管理器中资源组的访问权限。 请参见[使用 Windows VM 系统分配的托管标识访问资源管理器](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)。
4. 在 VM 上安装混合 Runbook 辅助角色。 请参阅[部署 Windows 混合 Runbook 辅助角色](automation-windows-hrw-install.md)或[部署 Linux 混合 Runbook 辅助角色](automation-linux-hrw-install.md)。
5. 更新 Runbook，将 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet 与 `Identity` 参数一起使用，以便对 Azure 资源进行身份验证。 此配置减少了使用运行方式帐户以及执行关联帐户管理的需求。

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` 适用于使用系统分配的标识和单一用户分配的标识的混合 Runbook 辅助角色。 如果在混合 Runbook 辅助角色上使用多个用户分配的标识，Runbook 必须为 `Connect-AzAccount` 指定 `AccountId` 参数，以选择特定的用户分配的标识。

## <a name="use-runbook-authentication-with-run-as-account"></a>将 Runbook 身份验证与运行方式帐户结合使用

不需要让 Runbook 将自身的身份验证提供给本地资源，但可以针对混合 Runbook 辅助角色组指定运行方式帐户。 为此，你必须定义有权访问本地资源的[凭据资产](./shared-resources/credentials.md)。 这些资源包括证书存储，所有 Runbook 在组中的混合 Runbook 辅助角色上使用这些凭据运行。

凭据的用户名必须采用以下格式之一：

* 域\用户名
* username@domain
* 用户名（适用于本地计算机的本地帐户）

使用以下过程针对混合 Runbook 辅助角色组指定运行方式帐户：

1. 创建具有本地资源访问权限的[凭据资产](./shared-resources/credentials.md)。
2. 在 Azure 门户中打开自动化帐户。
3. 选择“混合辅助角色组”，并选择特定组。
4. 选择“所有设置”，然后选择“混合辅助角色组设置”。
5. 将“运行方式”的值从“默认”更改为“自定义”。
6. 选择凭据，并单击“保存”。

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>安装运行方式帐户证书

在 Azure 中部署资源时，可能需要在自动生成过程中访问本地系统以支持部署过程中的某个任务或某组步骤。 若要使用运行方式帐户针对 Azure 进行身份验证，必须安装运行方式帐户证书。

以下 PowerShell Runbook 称为 Export-RunAsCertificateToHybridWorker，其从 Azure 自动化帐户导出运行方式证书。 Runbook 下载证书并将其导入到连接到同一帐户的混合 Runbook 辅助角色上的本地计算机证书存储中。 完成该步骤后，Runbook 会验证辅助角色能否成功地使用运行方式帐户对 Azure 进行身份验证。

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>对于 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的别名。 搜索库项时，如果未看到 `Connect-AzAccount`，可以使用 `Add-AzAccount`，或者在自动化帐户中更新模块。

若要完成运行方式帐户的准备工作：

1. 以 .ps1 扩展名将 Export-RunAsCertificateToHybridWorker Runbook 保存到计算机。
2. 将其导入自动化帐户。
3. 编辑 Runbook，将 `Password` 变量的值更改为你自己的密码。 
4. 发布 Runbook。
5. 运行 Runbook，以混合 Runbook 辅助角色组为目标，该组使用运行方式帐户运行 Runbook 并对其进行身份验证。 
6. 检查作业流，以了解其报告某个操作尝试将证书导入本地计算机存储，且后跟多个行。 此行为取决于订阅中定义的自动化帐户数以及身份验证的成功程度。

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>在 Windows 混合 Runbook 辅助角色上使用已签名 Runbook

你可以将 Windows 混合 Runbook 辅助角色配置为仅运行已签名 Runbook。 

> [!IMPORTANT]
> 将混合 Runbook 辅助角色配置为仅运行已签名 Runbook 后，未签名的 Runbook 将无法在该辅助角色上执行。

### <a name="create-signing-certificate"></a>创建签名证书

以下示例创建可用于对 Runbook 签名的自签名证书。 此代码会创建证书并将其导出，使混合 Runbook 辅助角色以后可以导入它。 还会返回指纹，以备以后用于引用证书。

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>导入证书并配置辅助角色以进行签名验证

将创建的证书复制到组中的每个混合 Runbook 辅助角色。 运行以下脚本，以导入证书并将辅助角色配置为在 Runbook 上使用签名验证。

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>使用证书对 Runbook 签名

将混合 Runbook 辅助角色配置为仅使用已签名 Runbook 后，必须对要在混合 Runbook 辅助角色上使用的 Runbook 签名。 使用以下示例 PowerShell 代码对这些 Runbook 签名。

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

对 Runbook 签名后，必须将其导入自动化帐户并与签名块一起发布。 若要了解如何导入 Runbook，请参阅[导入 Runbook](manage-runbooks.md#import-a-runbook)。

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>在 Linux 混合 Runbook 辅助角色上使用已签名 Runbook

为了能够使用签名的 Runbook，Linux 混合 Runbook 辅助角色必须在本地计算机上具有 [GPG](https://gnupg.org/index.html) 可执行文件。

> [!IMPORTANT]
> 将混合 Runbook 辅助角色配置为仅运行已签名 Runbook 后，未签名的 Runbook 将无法在该辅助角色上执行。

### <a name="create-a-gpg-keyring-and-keypair"></a>创建 GPG keyring 和密钥对

若要创建 GPG keyring 和密钥对，请使用混合 Runbook 辅助角色 [nxautomation account](automation-runbook-execution.md#log-analytics-agent-for-linux)。

1. 使用 sudo 应用程序以 nxautomation 帐户登录。

    ```bash
    sudo su – nxautomation
    ```

2. 使用 nxautomation 时，将生成 GPG 密钥对。 GPG 将引导你完成每个步骤。 必须提供姓名、电子邮件地址、过期时间和密码。 然后等待，直到计算机上有足够的熵用于生成密钥。

    ```bash
    sudo gpg --generate-key
    ```

3. 由于 GPG 目录使用 sudo 生成，需要使用以下命令将其所有者更改为 nxautomation。

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>使 keyring 可供混合 Runbook 辅助角色使用

创建 keyring 后，需要使其可供混合 Runbook 辅助角色使用。 修改设置文件 /var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf 以在文件部分 `[worker-optional]` 下包含以下示例代码。

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>验证签名验证是否已打开

如果已在计算机上禁用签名验证，则必须通过运行以下 sudo 命令将其打开。 使用工作区 ID 替换 `<LogAnalyticsworkspaceId>`。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>对 runbook 签名

配置签名验证后，即可使用以下 GPG 命令对 Runbook 签名。

```bash
gpg –-clear-sign <runbook name>
```

签名的 Runbook 称为 <runbook name>asc。

已签名 Runbook 现在可上传到 Azure 自动化中，并且可以像常规 Runbook 一样执行。

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色中启动 Runbook

[在 Azure 自动化中启动 Runbook](start-runbooks.md) 介绍了用于启动 Runbook 的不同方法。 在混合 Runbook 辅助角色上启动 Runbook 将使用“运行位置”选项，该选项允许你指定混合 Runbook 辅助角色组的名称。 指定组时，该组中的其中一个辅助角色检索和运行 Runbook。 如果 Runbook 未指定此选项，Azure 自动化会照常运行 Runbook。

在 Azure 门户中启动 Runbook 时，会看到一个“运行位置”选项，可以在其中选择“Azure”或“混合辅助角色”  。 如果选择“混合辅助角色”，则可以从下拉列表中选择混合 Runbook 辅助角色组。

使用 PowerShell 启动 Runbook 时，请将 `RunOn` 参数与 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) cmdlet 一起使用。 以下示例使用 Windows PowerShell 在名为 MyHybridGroup 的混合 Runbook 辅助角色组中启动名为 Test-Runbook 的 Runbook。

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="next-steps"></a>后续步骤

* 如果 Runbook 未成功完成，请查看 [Runbook 执行失败](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)相关故障排除指南。
* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅 [PowerShell 文档](/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
