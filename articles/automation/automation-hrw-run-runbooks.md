---
title: 在 Azure 自动化混合 Runbook 辅助角色上运行 runbook
description: 本文介绍如何使用混合 Runbook 辅助角色在本地数据中心或云提供商的计算机上运行 runbook。
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: b65c72e0c65cf9aa84cb614478fbdf78258f3054
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405831"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>在混合 Runbook 辅助角色上运行 runbook

以混合 Runbook 工作线程为目标的 Runbook 通常管理本地计算机上的资源，或针对部署该辅助角色的本地环境中的资源。 Azure 自动化中的 runbook 通常管理 Azure 云中的资源。 尽管它们使用方式不同，但在 Azure 自动化中运行的 Runbook 和在混合 Runbook 工作线程上运行的 Runbook 在结构上是相同的。

当您创作要在混合 Runbook 工作线程上运行的 Runbook 时，应在承载该辅助角色的计算机上编辑和测试 Runbook。 主机具有管理和访问本地资源所需的所有 PowerShell 模块和网络访问。 在混合 Runbook 工作计算机上测试 Runbook 后，可以将其上载到 Azure 自动化环境，可在该工作线程上运行。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合 Runbook 工作人员的 Runbook 权限

由于它们正在访问非 Azure 资源，因此在混合 Runbook 工作线程上运行的 Runbook 无法使用 Runbook 身份验证到 Azure 资源通常使用的身份验证机制。 Runbook 要么向本地资源提供自己的身份验证，要么使用[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)配置身份验证。 您还可以指定"运行为"帐户，以便为所有 Runbook 提供用户上下文。

### <a name="runbook-authentication"></a>Runbook 身份验证

默认情况下，运行簿在本地计算机上运行。 对于 Windows，它们在本地**系统**帐户的上下文中运行。 对于 Linux，它们在特殊用户帐户**nx 自动化**的上下文中运行。 在这两种情况下，Runbook 都必须向它们访问的资源提供自己的身份验证。

您可以在 Runbook 中使用[凭据](automation-credentials.md)和[证书](automation-certificates.md)资产，其中可以使用 cmdlet，以便 runbook 可以对不同的资源进行身份验证。 下面的示例显示了用于重新启动计算机的 Runbook 的一部分。 它将从凭据资产检索凭据和计算机名称从变量资产中检索，然后将这些值与`Restart-Computer`cmdlet 一起使用。

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

您还可以使用[内联脚本](automation-powershell-workflow.md#inlinescript)活动。 `InlineScript`允许您在另一台计算机上运行代码块，其凭据由[PS凭据通用参数](/powershell/module/psworkflow/about/about_workflowcommonparameters)指定。

### <a name="run-as-account"></a>运行方式帐户

您可以为混合 Runbook 工作组指定"运行为帐户"，而不是让 Runbook 向本地资源提供自己的身份验证。 为此，必须定义有权访问本地资源的[凭据资产](automation-credentials.md)。 这些资源包括证书存储和在组中的混合 Runbook 辅助角色上在这些凭据下运行的所有 Runbook。

凭据的用户名必须采用以下格式之一：

* 域\用户名
* username@domain
* 用户名（适用于本地计算机的本地帐户）

使用以下过程为混合 Runbook 工作组指定"运行为帐户"科目。

1. 创建具有本地资源访问权限的[凭据资产](automation-credentials.md)。
2. 在 Azure 门户中打开自动化帐户。
3. 选择“混合辅助角色组”**** 磁贴，并选择组。
4. 选择 **"所有"设置**，后跟**混合辅助角色组设置**。
5. 将 **"运行为默认**"的值**更改为****"自定义**"。
6. 选择凭据，并单击“保存”****。

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Azure 资源的托管标识

Azure 虚拟机上的混合 Runbook 工作人员可以使用 Azure 资源的托管标识对 Azure 资源进行身份验证。 对 Azure 资源使用托管标识而不是运行"作为帐户"可提供好处，因为您不需要：

* 导出"运行为证书"，然后将其导入混合 Runbook 辅助角色。
* 续订"运行为"帐户使用的证书。
* 在 Runbook 代码中处理"作为连接"运行对象。

按照后续步骤在混合 Runbook 工作线程上为 Azure 资源使用托管标识。

1. 创建 Azure VM。
2. 为 VM 上的 Azure 资源配置托管标识。 请参阅[使用 Azure 门户 在 VM 上配置 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。
3. 授予 VM 对资源管理器中的资源组的访问权限。 请参阅[使用 Windows VM 系统分配的托管标识访问资源管理器](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)。
4. 在 VM 上安装混合 Runbook 工作线程。 请参阅[部署 Windows 混合 Runbook 工作线程](automation-windows-hrw-install.md)。
5. 更新 Runbook 以使用带有参数的连接`Identity`[-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet 对 Azure 资源进行身份验证。 此配置减少了使用"运行为"帐户并执行关联帐户管理的需要。

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`使用系统分配的标识和单个用户分配的标识为混合 Runbook 工作线程工作。 如果在混合 Runbook 工作线程上使用多个用户分配标识，则 Runbook 必须指定`AccountId`用于`Connect-AzAccount`选择特定用户分配的标识的参数。

### <a name="automation-run-as-account"></a><a name="runas-script"></a>自动化运行方式帐户

作为在 Azure 中部署资源的自动生成过程的一部分，您可能需要访问本地系统以支持部署序列中的任务或步骤集。 要使用"运行为"帐户针对 Azure 提供身份验证，必须安装"运行为帐户"证书。

以下 PowerShell 运行簿称为 **"导出-运行证书到混合辅助角色**"，从 Azure 自动化帐户导出"作为运行证书"。 Runbook 会下载证书并将其导入连接到同一帐户的混合 Runbook 辅助器上的本地计算机证书存储。 完成此步骤后，Runbook 将验证工作人员是否可以使用"运行 As"帐户成功向 Azure 进行身份验证。

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
>对于 PowerShell 运行`Add-AzAccount`簿`Add-AzureRMAccount`，并且是`Connect-AzAccount`的别名。 搜索库项目时，如果看不到`Connect-AzAccount`，则可以使用`Add-AzAccount`，也可以在自动化帐户中更新模块。

要完成"运行为帐户"的准备：

1. 使用 **.ps1**扩展将**导出运行证书保存到混合辅助程序**运行簿到您的计算机。
2. 将其导入您的自动化帐户。
3. 编辑 Runbook，将`Password`变量的值更改为您自己的密码。 
4. 发布 Runbook。
5. 运行 Runbook，以使用"运行即"帐户运行和验证 Runbook 的混合 Runbook 工作组为目标。 
6. 检查作业流以查看它报告尝试将证书导入本地计算机存储，并遵循多行。 此行为取决于您在订阅中定义的自动化帐户数以及身份验证的成功程度。

## <a name="job-behavior-on-hybrid-runbook-workers"></a>混合 Runbook 工作簿上的作业行为

Azure 自动化处理混合 Runbook 工作簿上的作业与 Azure 沙盒中运行的作业略有不同。 一个关键区别是 Runbook 工作人员的作业持续时间没有限制。 由于[共享公平](automation-runbook-execution.md#fair-share)，在 Azure 沙盒中运行的 Runbook 限制为三个小时。

对于长时间运行的运行手册，您希望确保它具有对可能重新启动的弹性，例如，如果承载辅助角色的计算机重新启动。 如果混合 Runbook 工作线程主机重新启动，则任何正在运行的 Runbook 作业将从头开始重新启动，或从 PowerShell 工作流运行簿的最后一个检查点重新启动。 运行簿作业重新启动三次以上后，它将挂起。

请记住，混合 Runbook 辅助角色的工作在 Windows 上的本地系统帐户或 Linux 上的**nx 自动化**帐户下运行。 对于 Linux，必须确保 nx**自动化**帐户有权访问 Runbook 模块的存储位置。 使用[安装模块](/powershell/module/powershellget/install-module)cmdlet 时，请确保为`Scope`参数指定 AllUser，以确保 nx**自动化**帐户具有访问权限。 有关 Linux 上 PowerShell 的详细信息，请参阅[非 Windows 平台上 PowerShell 的已知问题](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)。

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>在混合 Runbook 工作线程上启动 Runbook

[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md)描述了启动 Runbook 的不同方法。 在混合 Runbook 工作线程上启动 Runbook 使用"**运行"** 选项，该选项允许您指定混合 Runbook 工作群体的名称。 指定组时，该组中的一个工作人员将检索并运行 Runbook。 如果 Runbook 未指定此选项，Azure 自动化将照常运行 Runbook。

在 Azure 门户中启动 Runbook 时，将显示"**在上运行**"选项，并为此选择**Azure**或**混合辅助角色**。 如果选择**混合辅助角色**，则可以从下拉列表中选择混合 Runbook 工作群体。

将`RunOn`参数与["开始-阿兹自动化 Runbook"cmdlet](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0)一起使用。 下面的示例使用 Windows PowerShell 在名为 MyHybridGroup 的混合 Runbook 工作组上启动名为 **"测试运行簿"的 Runbook。**

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> 如果您安装了较早的版本[，则应下载最新的 PowerShell 版本](https://azure.microsoft.com/downloads/)。 仅在从 PowerShell 启动 Runbook 的工作站上安装此版本。 除非打算从该计算机开始运行手册，否则无需将其安装在混合 Runbook 辅助计算机计算机上。

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>在 Windows 混合 Runbook 工作线程上使用签名的 Runbook

您可以将 Windows 混合 Runbook 辅助角色配置为仅运行已签名的 Runbook。

> [!IMPORTANT]
> 将混合 Runbook 辅助角色配置为仅运行已签名 Runbook 后，未签名的 Runbook 将无法在该辅助角色上执行。

### <a name="create-signing-certificate"></a>创建签名证书

以下示例创建可用于对 Runbook 签名的自签名证书。 此代码创建证书并导出证书，以便混合 Runbook 辅助角色以后可以导入它。 还会返回指纹，以便以后用于引用证书。

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>导入证书并配置签名验证的辅助人员

将创建的证书复制到组中的每个混合 Runbook 工作人员。 运行以下脚本以导入证书并将辅助程序配置为在 Runbook 上使用签名验证。

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>使用证书对 Runbook 进行签名

将混合 Runbook 辅助角色配置为仅使用已签名的 Runbook 时，必须对要在混合 Runbook 辅助角色上使用的 Runbook 进行签名。 使用以下示例 PowerShell 代码对这些 Runbook 进行签名。

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

签署 Runbook 后，必须将其导入自动化帐户，然后使用签名块进行发布。 若要了解如何导入 Runbook，请参阅[将 Runbook 从文件导入 Azure 自动化](manage-runbooks.md#importing-a-runbook)。

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>在 Linux 混合 Runbook 工作线程上使用签名的 Runbook

为了能够使用签名的 Runbook，Linux 混合 Runbook 工作线程必须在本地计算机上具有[GPG](https://gnupg.org/index.html)可执行文件。

> [!IMPORTANT]
> 将混合 Runbook 辅助角色配置为仅运行已签名 Runbook 后，未签名的 Runbook 将无法在该辅助角色上执行。

### <a name="create-a-gpg-keyring-and-keypair"></a>创建 GPG keyring 和密钥对

要创建 GPG 密钥环和密钥对，请使用混合 Runbook 辅助角色**nx 自动化**帐户。

1. 使用 sudo 应用程序作为 nx**自动化**帐户登录。

    ```bash
    sudo su – nxautomation
    ```

2. 使用 nx**自动化**后，生成 GPG 密钥对。 GPG 将指导您完成这些步骤。 您必须提供姓名、电子邮件地址、过期时间和密码。 然后等待，直到机器上有足够的熵来生成密钥。

    ```bash
    sudo gpg --generate-key
    ```

3. 由于 GPG 目录是使用 sudo 生成的，因此必须使用以下命令将其所有者更改为**nx 自动化**。

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>使密钥环可供混合 Runbook 工作线程使用

创建密钥环后，使其可供混合 Runbook 辅助角色使用。 修改设置文件 **/var/opt/microsoft/omsagent/状态/自动化工作人员/diy/worker.conf，** 以在文件部分`[worker-optional]`下包括以下示例代码。

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>验证签名验证是否打开

如果计算机上禁用了签名验证，则必须通过运行以下 sudo 命令将其打开。 替换为`<LogAnalyticsworkspaceId>`工作区 ID。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>对 runbook 签名

配置签名验证后，请使用以下 GPG 命令对 Runbook 进行签名。

```bash
gpg –-clear-sign <runbook name>
```

签名的运行手册称为**<runbook name>.asc**。

现在，您可以将签名的 Runbook 上载到 Azure 自动化，并像常规 Runbook 一样执行它。

## <a name="next-steps"></a>后续步骤

* 要了解有关启动 Runbook 的方法的更多信息，请参阅[在 Azure 自动化 中启动 Runbook。](automation-starting-a-runbook.md)
* 要了解如何使用文本编辑器在 Azure 自动化中使用 PowerShell Runbook，请参阅在[Azure 自动化中编辑 Runbook](automation-edit-textual-runbook.md)。
* 如果 Runbook 未成功完成，请查看[Runbook 执行失败的](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)故障排除指南。
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
