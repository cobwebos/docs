<properties
    pageTitle="配置 Azure 运行方式帐户 | Azure"
    description="本教程引导你在 Azure 自动化中创建、测试安全主体，并示范如何使用安全主体进行身份验证。"
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.date="03/31/2016"
    wacn.date="05/13/2016"/>

# 使用 Azure 运行方式帐户进行 Runbook 身份验证
本主题说明如何从 Azure 门户使用新的运行方式帐户功能（也名为服务主体）配置自动化帐户，以使用自动化 Runbook 访问订阅中的 Azure Resource Manager (ARM) 资源。当你在 Azure 门户中创建新的自动化帐户时，默认情况下将自动创建新的服务主体，并将其分配给订阅中参与者的基于角色的访问控制 (RBAC) 角色。这样可以简化操作过程，并帮助你快速开始构建和部署 Runbook 来支持自动化需求。

使用服务主体可以：

* 在使用 Runbook 管理 Azure ARM 资源时，提供标准化方法在 Azure 上进行身份验证
* 自动使用 Azure 警报中配置的全局 Runbook


>[AZURE.NOTE] 包含自动化全局 Runbook 的 Azure [警报集成功能](/documentation/articles/insights-receive-alert-notifications)需要使用服务主体配置一个自动化帐户。你可以选择用户定义的包含服务主体的自动化帐户，也可以创建新的自动化帐户。



我们将说明如何从 Azure 门户创建自动化帐户、使用 Azure PowerShell 以运行方式帐户更新帐户，以及在 Runbook 中使用服务主体进行身份验证。

## 从 Azure 门户创建新的自动化帐户
在本部分中，你将执行以下步骤以从 Azure 门户创建新的 Azure 自动化帐户和服务主体。

>[AZURE.NOTE] 执行这些步骤的用户*必须*是订阅管理员角色的成员。

1. 以你要管理的 Azure 订阅的服务管理员身份登录到 Azure 门户。
2. 选择“自动化帐户”。
3. 在“自动化帐户”边栏选项卡中，单击“添加”。<br>![添加自动化帐户](./media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. 在“添加自动化帐户”边栏选项卡的“名称”框中，键入新自动化帐户的名称。
5. 如果有多个订阅，请为新帐户指定一个订阅，并指定一个新的或现有的**资源组**以及 Azure 数据中心的**位置**。
6. 确认为“创建 Azure 运行方式帐户”选项选择了“是”值，然后单击“创建”按钮。  

    ![添加自动化帐户警报](./media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] 如果选择选项“否”以选择不创建运行方式帐户，则“添加自动化帐户”边栏选项卡中会出现一条警告消息。尽管这会创建帐户并将其分配到订阅中的“参与者”角色，但该帐户在订阅目录服务中没有相应的身份验证标识，因此无法访问订阅中的资源。这将导致引用此帐户的任何 Runbook 都无法进行身份验证并针对 ARM 资源执行任务。

    ![添加自动化帐户警报](./media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] 如果在单击“创建”按钮后收到权限被拒绝的错误消息，则原因是你的帐户不是订阅管理员角色的成员。

7. 在 Azure 创建自动化帐户时，可以在菜单的“通知”下面跟踪进度。

完成后，将使用名为 **AzureRunAsCertificate** 的证书资产（有效期为一年）和名为 **AzureRunAsConnection** 的连接资产创建自动化帐户。

## 使用 PowerShell 更新自动化帐户
以下过程使用 PowerShell 更新现有的自动化帐户并创建服务主体。如果你创建了一个帐户但拒绝创建运行方式帐户，则必须执行此过程。

在继续之前，请确认以下事项：

1. 已下载并安装[适用于 Windows PowerShell 的 Azure Active Directory 模块（64 位版本）](http://go.microsoft.com/fwlink/p/?linkid=236297)。
2. 已创建自动化帐户。以下脚本中的 -AutomationAccountName 和 -ApplicationDisplayName 参数值将引用此帐户。
3. 已安装 [Azure 自动化创作工具包](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.2)

```
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser
```

PowerShell 脚本将配置以下项：

* 一个 Azure AD 应用程序，它可以使用自签名证书以及此应用程序的服务主体帐户在 Azure AD 中进行身份验证，并在当前订阅中为此帐户分配参与者角色（可以将此角色更改为所有者或任何其他角色）。有关更多信息，请检阅 [Role-based access control in Azure Automation（Azure 自动化中基于角色的访问控制）](/documentation/articles/automation-role-based-access-control)一文。  
* 指定的自动化帐户中名为 **AzureRunAsCertificate** 的自动化证书资产，用于保存服务主体中使用的证书。
* 指定的自动化帐户中名为 **AzureRunAsConnection** 的自动化连接资产，用于保存 applicationId、tenantId、subscriptionId 和证书指纹。  


### 运行 PowerShell 脚本

1. 将以下脚本保存到计算机。在本示例中，请使用文件名 **New-AzureServicePrincipal.ps1** 保存。  

    ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,

    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )

    Login-AzureRmAccount -EnvironmentName AzureChinaCloud
    Import-Module AzureRM.Resources

    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

    $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

    $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
    $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

    $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
    $KeyCredential.StartDate = $CurrentDate
    $KeyCredential.EndDate= $EndDate
    $KeyCredential.KeyId = $KeyId
    $KeyCredential.Type = "AsymmetricX509Cert"
    $KeyCredential.Usage = "Verify"
    $KeyCredential.Value = $KeyValue

    # Use Key credentials
    $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ServicePrincipalDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. 在计算机上，从“开始”屏幕以提升的用户权限启动 **Windows PowerShell**。
3. 从提升权限的 PowerShell 命令行 shell 导航到包含步骤 1 所创建脚本的文件夹，并执行脚本来更改 *–ResourceGroup*、*-AutomationAccountName*、*-ApplicationDisplayName* 和 *-CertPlainPassword* 参数的值。<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> `
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] 执行脚本后，系统将提示你在 Azure 上进行身份验证。你*必须*使用充当订阅中服务管理员的帐户登录。  
<br>
4. 脚本成功完成后，请转到下一部分，以测试并验证新的凭据配置。

### 验证身份验证
接下来，我们将执行一个小测试，以确认你是否能够使用新的服务主体成功进行身份验证。如果无法成功完成身份验证，请返回步骤 1 并再次确认前面的每个步骤是否正确。

1. 在 Azure 门户中，打开前面创建的自动化帐户。  
2. 单击“Runbook”磁贴打开 Runbook 列表。
3. 单击“添加 Runbook”按钮创建新的 Runbook，然后在“添加 Runbook”边栏选项卡中选择“创建新 Runbook”。
4. 将 Runbook 命名为 *Test-SecPrin-Runbook*，然后选择“PowerShell”作为“Runbook 类型”。单击“创建”以创建 Runbook。
5. 在“编辑 PowerShell Runbook”边栏选项卡中，将以下代码粘贴到画布上：<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection `
     Add-AzureRMAccount -EnvironmentName AzureChinaCloud -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```  
<br>
6. 单击“保存”以保存 Runbook。
7. 单击“测试窗格”打开“测试”边栏选项卡。
8. 单击“启动”以启动测试。
9. 会创建一个“[Runbook 作业](/documentation/articles/automation-runbook-execution)”并且在窗格中显示其状态。  
10. 作业状态最初为“排队”，表示它正在等待云中的 Runbook 辅助角色可用。在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。  
11. Runbook 作业完成后，会显示其输出。在本例中，我们应会看到状态为“已完成”。<br>![安全主体 Runbook 测试](./media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. 关闭“测试”边栏选项卡返回到画布。
13. 关闭“编辑 PowerShell Runbook”边栏选项卡。
14. 关闭“Test-SecPrin-Runbook”边栏选项卡。

上面用于验证是否已正确设置新帐户的代码，就是你要在 PowerShell Runbook 中使用的、为了管理 ARM 资源而在 Azure 自动化中进行身份验证的代码。当然，你也可以继续使用目前所用的自动化帐户进行身份验证。

## 后续步骤
- 有关服务主体的详细信息，请参阅 [Application Objects and Service Principal Objects（应用程序对象和服务主体对象）](/documentation/articles/active-directory-application-objects)。
- 有关 Azure 自动化中基于角色的访问控制的详细信息，请参阅 [Role-based access control in Azure Automation（Azure 自动化中基于角色的访问控制）](/documentation/articles/automation-role-based-access-control)。

<!---HONumber=Mooncake_0516_2016-->