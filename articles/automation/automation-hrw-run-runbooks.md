---
title: 在 Azure 自动化混合 Runbook 辅助角色上运行 runbook
description: 本文介绍如何使用混合 Runbook 辅助角色在本地数据中心或云提供商的计算机上运行 runbook。
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 9b9196cde45686e42d1baf7faedf94bdb73acccc
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367051"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色上运行 runbook

面向混合 Runbook 辅助角色的 runbook 通常会管理本地计算机上的资源，或针对部署辅助角色的本地环境中的资源管理资源。 Azure 自动化中的 runbook 通常管理 Azure 云中的资源。 即使使用方式不同，在 Azure 自动化中运行的 runbook 和运行在混合 Runbook 辅助角色上的 runbook 也在结构上完全相同。

编写要在混合 Runbook 辅助角色上运行的 runbook 时，应在承载辅助角色的计算机上编辑和测试 runbook。 主机具有管理和访问本地资源所需的所有 PowerShell 模块和网络访问权限。 在混合 Runbook 辅助角色计算机上测试 runbook 后，你可以将其上传到 Azure 自动化环境，在此环境中，可以在辅助角色上运行它。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的 Runbook 权限

在访问非 Azure 资源时，在混合 Runbook 辅助角色上运行的 runbook 不能使用通常由对 Azure 资源进行身份验证的 runbook 所使用的身份验证机制。 Runbook 提供自己的本地资源身份验证，或使用[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)配置身份验证。 还可以指定运行方式帐户，为所有 runbook 提供用户上下文。

### <a name="runbook-authentication"></a>Runbook 身份验证

默认情况下，runbook 在本地计算机上运行。 对于 Windows，它们会在本地系统帐户的上下文中运行。 对于 Linux，它们在特殊用户帐户**nxautomation**的上下文中运行。 在任一方案中，runbook 都必须为他们访问的资源提供自己的身份验证。

你可以在 runbook 中使用[凭据](automation-credentials.md)和[证书](automation-certificates.md)资产，这些 cmdlet 可用于指定凭据，以使 runbook 可以对不同的资源进行身份验证。 下面的示例显示了用于重新启动计算机的 Runbook 的一部分。 它从凭据资产检索凭据，从变量资产检索计算机的名称，然后将这些值与 `Restart-Computer` cmdlet 一起使用。

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

你还可以使用[InlineScript](automation-powershell-workflow.md#inlinescript)活动。 `InlineScript` 允许你使用[PSCredential 通用参数](/powershell/module/psworkflow/about/about_workflowcommonparameters)指定的凭据在另一台计算机上运行代码块。

### <a name="run-as-account"></a>运行方式帐户

你可以为混合 Runbook 辅助角色组指定运行方式帐户，而不是让你的 runbook 向本地资源提供自己的身份验证。 为此，你必须定义有权访问本地资源的[凭据资产](automation-credentials.md)。 这些资源包括证书存储，所有 runbook 在组中的混合 Runbook 辅助角色上以这些凭据运行。

凭据的用户名必须采用以下格式之一：

* 域\用户名
* username@domain
* 用户名（适用于本地计算机的本地帐户）

使用以下过程为混合 Runbook 辅助角色组指定运行方式帐户。

1. 创建具有本地资源访问权限的[凭据资产](automation-credentials.md)。
2. 在 Azure 门户中打开自动化帐户。
3. 选择“混合辅助角色组”磁贴，并选择组。
4. 选择 "**所有设置**"，然后选择 "**混合辅助角色组设置**"。
5. 将 "**运行方式**" 的值从 "**默认**" 更改为 "**自定义**"。
6. 选择凭据，并单击“保存”。

### <a name="managed-identities-for-azure-resources"></a>Azure 资源的托管标识

Azure 虚拟机上的混合 Runbook 辅助角色可以使用 Azure 资源的托管标识对 Azure 资源进行身份验证。 使用 Azure 资源的托管标识（而不是运行方式帐户）提供了好处，因为无需执行以下操作：

* 导出运行方式证书，然后将其导入到混合 Runbook 辅助角色中。
* 续订运行方式帐户使用的证书。
* 在 runbook 代码中处理运行方式连接对象。

按照后续步骤在混合 Runbook 辅助角色上使用 Azure 资源的托管标识。

1. 创建 Azure VM。
2. 在虚拟机上配置 Azure 资源的托管标识。 请参阅[使用 Azure 门户在虚拟机上配置 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。
3. 授予 VM 对资源管理器中资源组的访问权限。 请参阅[使用 WINDOWS VM 系统分配的托管标识访问资源管理器](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)。
4. 在 VM 上安装混合 Runbook 辅助角色。 请参阅[部署 Windows 混合 Runbook 辅助角色](automation-windows-hrw-install.md)。
5. 更新 runbook，将[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet 与 `Identity` 参数一起使用，以便对 Azure 资源进行身份验证。 此配置可减少使用运行方式帐户和执行关联的帐户管理的需要。

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` 使用系统分配的标识和单一用户分配的标识来处理混合 Runbook 辅助角色。 如果在混合 Runbook 辅助角色上使用多个用户分配的标识，Runbook 必须为 `Connect-AzAccount` 指定*AccountId*参数，以选择特定的用户分配的标识。

### <a name="runas-script"></a>自动化运行方式帐户

作为在 Azure 中部署资源的自动生成过程的一部分，你可能需要访问本地系统，以支持部署序列中的一项任务或一组步骤。 若要使用运行方式帐户针对 Azure 提供身份验证，必须安装运行方式帐户证书。

以下称为**export-runascertificatetohybridworker**的 PowerShell runbook 从你的 Azure 自动化帐户导出运行方式证书。 Runbook 下载证书并将其导入到连接到同一帐户的混合 Runbook 辅助角色上的本地计算机证书存储中。 完成该步骤后，runbook 将验证辅助角色是否可以使用运行方式帐户成功地向 Azure 进行身份验证。

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
>对于 PowerShell runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount`的别名。 搜索库项时，如果看不到 `Connect-AzAccount`，则可以使用 `Add-AzAccount`，也可以在自动化帐户中更新模块。

若要完成运行方式帐户的准备工作：

1. 使用 ps1 扩展名将**export-runascertificatetohybridworker** runbook 保存到计算机 **。**
2. 将其导入自动化帐户。
3. 编辑 runbook，更改 `Password` 变量的值 o 你自己的密码。 
4. 发布 runbook。
5. 运行 runbook，将运行的混合 Runbook 辅助角色组作为目标，并使用运行方式帐户对 runbook 进行身份验证。 
6. 检查作业流以查看它是否报告了将证书导入到本地计算机存储区中的尝试，以及是否有多个行。 此行为取决于你在你的订阅中定义的自动化帐户数量以及身份验证的成功程度。

## <a name="job-behavior-on-hybrid-runbook-workers"></a>混合 Runbook 辅助角色上的作业行为

Azure 自动化处理混合 Runbook 辅助角色上的作业，不同于 Azure 沙箱中运行的作业。 一个关键区别在于 runbook worker 上的作业持续时间没有限制。 由于[公平共享](automation-runbook-execution.md#fair-share)，在 Azure 沙盒中运行的 runbook 限制为3小时。

对于长时间运行的 runbook，你需要确保它可以恢复可能的重新启动，例如，如果托管辅助角色的计算机重新启动。 如果混合 Runbook 辅助角色主机重新启动，则任何正在运行的 runbook 作业将从开始重新启动，或者从 PowerShell 工作流 runbook 的最后一个检查点重新启动。 在 runbook 作业重启三次后，它会被挂起。

请记住，混合 Runbook 辅助角色的作业在 Windows 上的本地系统帐户下运行，或在 Linux 上的**nxautomation**帐户下运行。 对于 Linux，必须确保**nxautomation**帐户有权访问存储 runbook 模块的位置。 使用[Install-Module](/powershell/module/powershellget/install-module) cmdlet 时，请确保为 `Scope` 参数指定 AllUsers，以确保**nxautomation**帐户具有访问权限。 有关 Linux 上的 PowerShell 的详细信息，请参阅[非 Windows 平台上的 powershell 的已知问题](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)。

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色上启动 runbook

[在 Azure 自动化中启动 runbook](automation-starting-a-runbook.md)介绍了用于启动 runbook 的不同方法。 在混合 Runbook 辅助角色上启动 runbook 使用 **"运行方式"** 选项，该选项允许你指定混合 Runbook 辅助角色组的名称。 指定组后，该组中的一个工作线程会检索并运行 runbook。 如果 runbook 未指定此选项，Azure Automation 会照常运行 runbook。

在 Azure 门户中启动 runbook 时，会显示 "**运行**方式" 选项，可在其中选择 " **Azure** " 或 "**混合辅助角色**"。 如果选择 "**混合辅助角色**"，则可以从下拉列表中选择混合 Runbook 辅助角色组。

将 `RunOn` 参数与 `Start-AzureAutomationRunbook` cmdlet 一起使用。 以下示例使用 Windows PowerShell 在名为通过 myhybridgroup 的混合 Runbook 辅助角色组上启动名为**Test-runbook**的 runbook。

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> `RunOn` 参数已添加到 Microsoft Azure PowerShell 版本0.9.1 的 `Start-AzureAutomationRunbook` 中。 如果安装的是旧版，则应[下载最新版本](https://azure.microsoft.com/downloads/)。 仅在从 PowerShell 启动 runbook 的工作站上安装此版本。 不需要在混合 Runbook 辅助角色计算机上安装它，除非要从该计算机启动 runbook。

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>使用 Windows 混合 Runbook 辅助角色上的签名 runbook

你可以将 Windows 混合 Runbook 辅助角色配置为仅运行已签名的 runbook。

> [!IMPORTANT]
> 将混合 Runbook 辅助角色配置为仅运行签名 runbook 后，未签名的 runbook 将无法在该辅助角色上执行。

### <a name="create-signing-certificate"></a>创建签名证书

以下示例创建可用于对 Runbook 签名的自签名证书。 此代码会创建证书并将其导出，使混合 Runbook 辅助角色可以在以后导入它。 还会返回指纹，以备以后用于引用证书。

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

将已创建的证书复制到组中的每个混合 Runbook 辅助角色。 运行以下脚本以导入证书，并将辅助角色配置为在 runbook 上使用签名验证。

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>使用证书对 runbook 进行签名

将混合 Runbook 辅助角色配置为仅使用已签名的 runbook 时，必须对要在混合 Runbook 辅助角色上使用的 runbook 进行签名。 使用以下示例 PowerShell 代码对这些 runbook 进行签名。

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

对 runbook 进行签名后，必须将其导入到自动化帐户，并使用签名块将其发布。 若要了解如何导入 Runbook，请参阅[将 Runbook 从文件导入 Azure 自动化](manage-runbooks.md#import-a-runbook)。

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>使用 Linux 混合 Runbook 辅助角色上的签名 runbook

为了能够使用签名的 runbook，Linux 混合 Runbook 辅助角色必须具有本地计算机上的[GPG](https://gnupg.org/index.html)可执行文件。

> [!IMPORTANT]
> 将混合 Runbook 辅助角色配置为仅运行签名 runbook 后，未签名的 runbook 将无法在该辅助角色上执行。

### <a name="create-a-gpg-keyring-and-keypair"></a>创建 GPG keyring 和密钥对

若要创建 GPG keyring 和密钥对，请使用混合 Runbook 辅助角色**nxautomation**帐户。

1. 使用 sudo 应用程序以**nxautomation**帐户身份登录。

    ```bash
    sudo su – nxautomation
    ```

2. 使用**nxautomation**后，生成 GPG 密钥对。 GPG 指导你完成这些步骤。 必须提供姓名、电子邮件地址、过期时间和密码。 然后，等到计算机上有足够的熵用于生成密钥。

    ```bash
    sudo gpg --generate-key
    ```

3. 由于 GPG 目录是使用 sudo 生成的，因此必须使用以下命令将其所有者更改为**nxautomation** 。

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>使 keyring 可用于混合 Runbook 辅助角色

创建 keyring 后，将其提供给混合 Runbook 辅助角色。 修改设置文件 **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** ，将以下示例代码包含在 `[worker-optional]`的 "文件" 部分下。

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>验证签名验证是否已打开

如果已在计算机上禁用签名验证，则必须通过运行以下 sudo 命令将其打开。 将 `<LogAnalyticsworkspaceId>` 替换为你的工作区 ID。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>对 runbook 签名

配置签名验证后，请使用以下 GPG 命令对 runbook 进行签名。

```bash
gpg –-clear-sign <runbook name>
```

签名 runbook `<runbook name>.asc`调用。

你现在可以将签名的 runbook 上传到 Azure 自动化，并像常规 runbook 一样执行它。

## <a name="next-steps"></a>后续步骤

* 若要了解有关启动 runbook 的方法的详细信息，请参阅[在 Azure 自动化中启动 runbook](automation-starting-a-runbook.md)。
* 若要了解如何使用文本编辑器在 Azure 自动化中使用 PowerShell runbook，请参阅[在 Azure 自动化中编辑 Runbook](automation-edit-textual-runbook.md)。
* 如果 runbook 没有成功完成，请查看故障排除指南以了解[runbook 执行失败](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)。
* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅[Powershell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
