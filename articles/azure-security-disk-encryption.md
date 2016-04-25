<properties
   pageTitle="适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密 | Microsoft Azure"
   description="本文概述适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密。"
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="devtiw"/>


#适用于 Windows 和 Linux IaaS VM 预览版的 Azure 磁盘加密

> [AZURE.NOTE] 本文档中的信息适用于 Azure 磁盘加密预览版。

Microsoft Azure 坚决承诺确保数据隐私权和数据所有权，并通过各种先进技术来加密、控制和管理加密密钥以及控制和审核对数据的访问，使你能够控制 Azure 托管的数据。这样，Azure 客户便可以灵活选择最符合其业务需求的解决方案。在本文中，我们将会介绍新的技术解决方案“适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密”，以帮助你保护数据，使你的组织能够信守在安全性与合规性方面所做的承诺。本文提供有关如何使用 Azure 磁盘加密功能的详细指导，包括支持的方案和用户体验。

**注意**：本文包含的特定建议可能会导致数据、网络或计算资源使用量增加，从而产生额外的许可或订阅成本。

## 概述

Azure 磁盘加密是用于加密 Windows 和 Linux IaaS 虚拟机磁盘的新功能。Azure 磁盘加密利用 Windows 的行业标准 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，为 OS 和数据磁盘提供卷加密。该解决方案与 [Azure 密钥](https://azure.microsoft.com/documentation/services/key-vault/)保管库集成，可帮助你控制和管理密钥保管库订阅中的磁盘加密密钥和机密，同时确保虚拟机磁盘中的所有数据可在 Azure 存储空间中静态加密。

### 加密方案

Azure 磁盘加密解决方案支持以下 3 种客户加密方案：

- 在通过客户加密 VHD 和加密密钥创建的新 IaaS VM 上启用加密

- 在通过 Azure 库创建的新 IaaS VM 上启用加密

- 在 Azure 中已运行的现有 IaaS VM 上启用加密

在 Microsoft Azure 中启用时，该解决方案支持以下适用于公共预览版的 IaaS VM：

- 与 Azure 密钥保管库集成

- 标准 [A、D 和 G 系列 IaaS VM](/home/features/virtual-machines/#price)

- 在使用 [Azure Resource Manager](/documentation/articles/resource-group-overview) 模型创建的 IaaS VM 上启用加密



该解决方案不支持公共预览版中的以下方案、功能和技术：

- 基本 VM 和标准 DS（高级存储）系列 IaaS VM

- 使用经典 VM 创建方法创建的 IaaS VM

- 可以禁用通过 Azure 磁盘加密启用的 IaaS VM 上的加密功能

- 与本地密钥管理服务集成

- Windows Server Technical Preview 3

- Red Hat Enterprise Linux

- Azure 文件（Azure 文件共享）、网络文件系统 (NFS)、动态卷、基于软件的 RAID 系统


### 加密功能

为 Azure IaaS VM 启用并部署 Azure 磁盘加密后，将会根据提供的配置启用以下功能：

- 加密 OS 卷以轻松保护客户存储中的引导卷

- 加密数据卷以轻松保护客户存储中的数据卷

- 保护客户 Azure 密钥保管库订阅中的加密密钥和机密

- 报告已加密 IaaS VM 的加密状态

- 从 IaaS 虚拟机中删除磁盘加密配置设置

适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密解决方案包括适用于 Windows 的磁盘加密扩展、适用于 Linux 的磁盘加密扩展、磁盘加密 PowerShell cmdlet、磁盘加密 CLI cmdlet 和磁盘加密 Azure Resource Manager 模板。在运行 Windows 或 Linux OS 的 IaaS VM 上支持 Azure 磁盘加密解决方案。有关支持的操作系统的详细信息，请参阅以下先决条件部分。

在公共预览期间，可以免费使用 Azure 磁盘加密来加密 VM 磁盘。我们预期在磁盘加密正式版推出后仍然保持免费。不过，价格可能会根据市场和竞争局势而变化。

### 价值主张

Azure 磁盘加密管理解决方案可以解决云中的以下业务需求：

-   使用行业标准的加密技术轻松保护 IaaS VM，满足组织的安全性与合规性要求。

-   IaaS VM 将会根据客户控制的密钥和策略启动，客户可以在密钥保管库中审核密钥和策略的使用方式。


### 加密工作流
为 Windows 和 Linux VM 启用磁盘加密所需的高级步骤如下：

1. 客户从上述 3 种加密方案中选择加密方案

2. 客户选择通过 Azure 磁盘加密 ARM 模板、PS cmdlet 或 CLI 命令启用磁盘加密，并指定加密配置

    - 针对客户加密的 VHD 方案，客户将加密的 VHD 上载到其存储帐户，将加密密钥材料上载到其密钥保管库，并提供加密配置以便在新 IaaS VM 上启用加密

    - 针对通过 Azure 库和已在 Azure 中运行的现有 VM 创建新的 VM，客户将提供加密配置以便在 IaaS VM 上启用加密

3. 客户向 Azure 平台授予访问权限，使其能够从密钥保管库中读取加密密钥数据（Windows 系统的 BitLocker 加密密钥和 Linux 的通行短语），从而在 IaaS VM 上启用加密

4. 客户提供 Azure AD 标识以便将加密密钥材料写入其密钥保管库，从而在上述方案 2 和 3 的 IaaS VM 上启用加密。

5.  Azure 服务管理使用加密和密钥保管库配置更新 VM 服务模型，并为客户预配加密的 VM

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.JPG)

## 先决条件

以下是针对概述部分提到支持的方案，在 Azure IaaS VM 上启用 Azure 磁盘加密所要满足的先决条件

- 用户必须拥有有效的活动 Azure 订阅才能在 Azure 支持的区域中创建资源

- 以下 Windows 服务器 SKU 支持 Azure 磁盘加密 - Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2。Windows Server 2008 操作系统不支持该解决方案。公共预览版不支持 Windows Server Technical Preview。

**注意**：对于 Windows Server 2008 R2，必须先安装 .NET Framework 4.5 才能在 Azure 中启用加密。你可以通过安装可选更新“适用于 Windows Server 2008 R2 x64 系统的 Microsoft .NET Framework 4.5.2 ([KB2901983](https://support.microsoft.com/kb/2901983))”，从 Windows 更新安装它。

- 以下 Linux 服务器 SKU 支持 Azure 磁盘加密 - Ubuntu、CentOS、SUSE 和 SUSE Linux Enterprise Server (SLES)。公共预览版不支持 Red Hat Enterprise Linux。

- 所有资源（例如密钥保管库、存储帐户、VM 等）必须属于同一 Azure 区域和订阅。

**注意** - Azure 磁盘加密要求密钥保管库和 VM 位于同一 Azure 区域。在不同区域中配置它们会导致启用 Azure 磁盘加密功能失败。

- 若要根据 Azure 磁盘加密的使用方式设置和配置 Azure 密钥保管库，请参阅本文先决条件中的**根据 Azure 磁盘加密的使用方式设置和配置 Azure 密钥保管库**部分。

- 若要根据 Azure 磁盘加密的使用方式在 Azure Active Directory 中设置和配置 Azure AD 应用程序，请参阅本文先决条件中的**在 Azure Active Directory 中设置 Azure AD 应用程序**部分。

- 若要设置和配置 Azure AD 应用程序的密钥保管库访问策略，请参阅本文先决条件中的**为 Azure AD 应用程序设置密钥保管库访问策略**部分。

- 若要准备预先加密的 Windows VHD，请参阅本文附录中的**准备已预先加密的 Windows VHD** 部分。

- 若要准备预先加密的 Linux VHD，请参阅本文附录中的**准备已预先加密的 Linux VHD** 部分。

- Azure 平台需要访问客户 Azure 密钥保管库中的加密密钥或机密，才能使这些密钥和机密可供 VM 用来启动和解密虚拟机 OS 卷。若要向 Azure 平台授予访问密钥保管库的权限，必须针对此要求在密钥保管库上设置 **enabledForDiskEncryption** 属性。有关详细信息，请参阅本文附录中的**根据 Azure 磁盘加密的使用方式设置和配置 Azure 密钥保管库**部分。

- 密钥保管库机密和密钥加密密钥 (KEK) URL 必须已设置版本。Azure 服务管理会强制实施这项版本控制限制。请参阅以下有效机密和 KEK URL 的示例：

	- 有效机密 URL 的示例：

		*https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- 有效 KRK KEK 的示例：

		*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Azure 磁盘加密不支持将端口号指定为密钥保管库机密和 KEK URL 的一部分。有关支持的密钥保管库 URL，请参阅以下示例：

 	- 不接受的密钥保管库 URL

		*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- 接受的密钥保管库 URL：

		*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- 若要启用 Azure 磁盘加密功能，IaaS VM 必须符合以下网络终结点配置要求：

	- IaaS VM 必须能够连接到 Azure Active Directory 终结点 [Login.windows.net]，以获取用于连接 Azure 密钥保管库的令牌

	- IaaS VM 必须能够连接到 Azure 密钥保管库终结点，以将加密密钥写入客户密钥保管库

	- IaaS VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户

**注意：**如果你的安全策略限制从 Azure VM 到 Internet 的访问，则你可以解析上述要连接到的 URI，并配置特定的规则以允许与这些 IP 建立出站连接。

- 若要运行任何 Azure 磁盘加密 PowerShell cmdlet，必须先安装 [Azure PowerShell 1.0.2](https://github.com/Azure/azure-powershell/releases/tag/v1.0.2-December2015)：

	- 若要安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](powershell-install-configure.md)。

	- 本文档假设你已了解基本概念，例如模块、cmdlet 和会话。有关详细信息，请参阅 [Windows PowerShell 入门](https://technet.microsoft.com/library/hh857337.aspx)

**注意：**[Azure PowerShell SDK 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016) 不支持 Azure 磁盘加密。

- 若要运行任何 Azure CLI 命令并将它与 Azure 订阅相关联，必须先安装 Azure CLI 版本：

	- 若要安装 Azure CLI 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure CLI](xplat-cli-install.md)

	- 将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure Resource Manager 配合使用，请参阅[此处](azure-cli-arm-commands.md)

- Azure 磁盘加密解决方案对 Windows IaaS VM 使用 BitLocker 外部密钥保护程序。如果 VM 已加入域，请不要推送会强制使用 TPM 保护程序的任何组策略。请参阅[此文](https://technet.microsoft.com/library/ee706521)，以了解“没有兼容的 TPM 时允许 BitLocker”组策略的详细信息。
- 用于创建 Azure AD 应用程序、创建新的密钥保管库或设置现有密钥保管库并启用加密的 Azure 磁盘加密先决条件 PowerShell 脚本位于[此处](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)。

### 根据 Azure 磁盘加密的使用方式设置和配置 Azure 密钥保管库

Azure 磁盘加密将保护 Azure 密钥保管库中的磁盘加密密钥和机密。请遵循以下每个部分中的步骤，根据 Azure 磁盘加密使用方式设置密钥保管库。

#### 创建新的密钥保管库
若要创建新的密钥保管库，请使用下列两个选项之一：

- 使用位于[此处](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)的“101-Create-KeyVault”ARM 模板
- 使用 Azure PowerShell 密钥保管库 cmdlet，如[此处](key-vault-get-started.md)所述

**注意：**如果你已为订阅设置密钥保管库，请转到下一部分。

#### 预配密钥加密密钥（可选）

如果你想要使用密钥加密密钥 (KEK) 获得附加的安全层以包装 BitLocker 加密密钥，你应将 KEK 添加到密钥保管库以便在预配过程中使用。使用 [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) cmdlet 可在密钥保管库中创建新的密钥加密密钥。有关详细信息，请参阅[密钥保管库文档](https://azure.microsoft.com/documentation/services/key-vault/)。

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

#### 设置密钥保管库权限以允许 Azure 平台访问密钥和机密

Azure 平台需要访问 Azure 密钥保管库中的加密密钥或机密，才能使这些密钥和机密可供 VM 用来启动和卷。若要向 Azure 平台授予权限以使其能够访问密钥保管库，必须在密钥保管库上设置 *enabledForDiskEncryption* 属性。可以使用密钥保管库 PS cmdlet 在密钥保管库上设置 enabledForDiskEncryption 属性：

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

必须如前所述在密钥保管库上设置 *enabledForDiskEncryption* 属性。可以通过访问 https://resources.azure.com 来设置该属性。确保正确设置上面详述的属性，否则部署将会失败。

#### 在 Azure Active Directory 中设置 Azure AD 应用程序

需要在 Azure 中正在运行的 VM 上启用加密时，Azure 磁盘加密将生成加密密钥并将其写入密钥保管库。在密钥保管库中管理加密密钥需要 Azure AD 身份验证。

为此，应该创建 Azure AD 应用程序。注册应用程序的详细步骤可在此[博客文章](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)的“获取应用程序的标识”部分中找到。这篇文章还包含一些有关预配和配置密钥保管库的有用示例。对于身份验证，可以使用基于客户端机密的身份验证或基于客户端证书的 Azure AD 身份验证。

##### Azure AD 的基于客户端机密的身份验证

以下部分提供了为 Azure AD 配置基于客户端机密的身份验证所要执行的步骤。

##### 使用 Azure PowerShell 创建新的 Azure AD 应用

使用以下 PowerShell cmdlet 来创建新的 Azure AD 应用：

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**注意：**$azureAdApplication.ApplicationId 是 Azure AD ClientID，$aadClientSecret 是客户端机密，稍后你应该使用这些信息来启用 ADE。你应该妥善保护 Azure AD 客户端机密。


##### 从 Azure 服务管理门户预配 Azure AD 客户端 ID 和机密

也可以使用 Azure 服务管理门户 (https://manage.windowsazure.com) 来预配 Azure AD 客户端 ID 和机密，请遵循以下步骤执行此任务：

1\. 单击“Active Directory”选项卡，如下图所示：

![Azure 磁盘加密](./media/azure-security-disk-encryption\disk-encryption-fig3.JPG)

2\. 单击“添加应用程序”，然后键入应用程序名称，如下所示：

![Azure 磁盘加密](./media/azure-security-disk-encryption\disk-encryption-fig4.JPG)

3\. 单击箭头按钮并配置应用程序的属性，如下所示：

![Azure 磁盘加密](./media/azure-security-disk-encryption\disk-encryption-fig5.JPG)

4\. 单击左下角的复选标记完成操作。此时将显示应用的配置页面。注意 Azure AD 客户端 ID 位于页面底部，如下图所示。

![Azure 磁盘加密](./media/azure-security-disk-encryption\disk-encryption-fig6.JPG)

5\. 单击“保存”按钮保存 Azure AD 客户端机密。单击“保存”按钮并记下“密钥”文本框中的机密，这是 Azure AD 客户端机密。你应该妥善保护 Azure AD 客户端机密。

![Azure 磁盘加密](./media/azure-security-disk-encryption\disk-encryption-fig7.JPG)


**注意：**门户不支持上述流程。

##### 使用现有的应用

若要执行以下命令，你需要 Azure AD PowerShell 模块，可从[此处](https://technet.microsoft.com/library/jj151815.aspx)获取该模块。

**注意：**必须在新的 PowerShell 窗口中执行以下命令。请不要使用 Azure PowerShell 或 Azure Resource Manager 窗口执行这些命令。提出这项建议的原因是这些 cmdlet 位于 MSOnline 模块或 Azure AD PowerShell 中。

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Azure AD 的基于证书的身份验证

以下部分提供了为 Azure AD 配置基于证书的身份验证所要执行的步骤。

##### 创建新的 Azure AD 应用

执行以下 PowerShell cmdlet 以创建新的 Azure AD 应用：

**注意：**请将“yourpassword”字符串替换为你的安全密码并保护该密码。

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

完成此步骤后，请将 .pfx 文件上载到密钥保管库，并启用将该证书部署到 VM 所需的访问策略。

##### 使用现有的 Azure AD 应用
如果要为现有应用配置基于证书的身份验证，请使用以下 PowerShell cmdlet。请务必在新的 PowerShell 窗口中执行这些 cmdlet。

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

完成此步骤后，请将 .pfx 文件上载到密钥保管库，并启用将该证书部署到 VM 所需的访问策略。

##### 将 PFX 文件上载到密钥保管库
有关如何运行此过程的详细说明，请阅读此[博客文章](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)。不过，只需执行以下 PowerShell cmdlet 即可完成此任务。请务必通过 Azure PowerShell 控制台执行这些 cmdlet：

**注意：**请将“yourpassword”字符串替换为你的安全密码并保护该密码。

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### 将密钥保管库中的证书部署到现有 VM
完成上载 PFX 之后，使用以下步骤将密钥保管库中的证书部署到现有 VM：

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### 为 Azure AD 应用程序设置密钥保管库访问策略

Azure AD 应用程序需有访问保管库中密钥或机密的权限。使用 [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) cmdlet，并将客户端ID（注册应用程序时所生成）用作 -ServicePrincipalName 参数值，即可向应用程序授予权限。你可以阅读此[博客文章](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)以获取一些相关的示例。下面还提供了有关如何通过 PowerShell 执行此任务的示例：

    $keyVaultName = ‘yourKeyVaultName’
    $aadClientID = '<youAadAppClientID>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys all -PermissionsToSecrets all

## 术语

参考该术语表来理解本技术所用的一些常见术语：


| 术语 | 定义 |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD | Azure AD 是 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) 的缩写。若要从密钥保管库进行身份验证以及存储和检索机密，必须有一个 Azure AD 帐户。 |
| Azure 密钥保管库 [AKV] | Azure 密钥保管库是基于 FIPS 验证的硬件安全模块的加密密钥管理服务，可以安全保护加密密钥和敏感机密。有关详细信息，请参阅[密钥保管库](https://azure.microsoft.com/services/key-vault/)文档。 |
| ARM | Azure Resource Manager |
| BitLocker | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) 是一种行业认可的 Windows 卷加密技术，用于在 Windows IaaS VM 上启用磁盘加密 |
| BEK | BitLocker 加密密钥用于加密 OS 引导卷和数据卷。BitLocker 密钥在客户的 Azure 密钥保管库中以机密形式进行保护。 |
| CLI | [Azure 命令行界面](xplat-cli-install.md) |
| DM-Crypt | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 是基于 Linux 的透明磁盘加密子系统，用于在 Linux IaaS VM 上启用磁盘加密 |
| KEK | 密钥加密密钥是非对称密钥 (RSA 2048)，用于在需要时保护或包装机密。你可以提供 HSM 保护的密钥或软件保护的密钥。有关详细信息，请参阅 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)文档 |
| PS cmdlet | [Azure PowerShell cmdlet](powershell-install-configure.md) |

## 磁盘加密部署方案和用户体验

你可以在许多方案中启用磁盘加密，具体步骤根据方案的不同而异。以下部分将会根据这些方案详细介绍如何操作。

### 在通过 Azure 库创建的新 IaaS VM 上启用加密

可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)发布的 ARM 模板，从 Azure 中的 Azure 库在新 IaaS Windows VM 上启用磁盘加密。单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入加密配置，然后单击“确定”。选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在新 IaaS VM 上启用加密。

**注意：**此模板使用 Windows Server 2012 库映像来创建新的加密 Windows VM。

你可以在下表中查看使用 Azure AD 客户端 ID 的、Azure 库方案中的新 VM 的 ARM 模板参数详细信息：

| 参数 | 说明|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName | 虚拟机的管理员用户名 |
| adminPassword | 虚拟机的管理员用户密码 |
| newStorageAccountName | 用于存储 OS 和数据 VHD 的存储帐户的名称 |
| vmSize | VM 的大小目前仅支持标准 A、D 和 G 系列 |
| virtualNetworkName | VM NIC 所属的 VNet 的名称。 |
| subnetName | VM NIC 所属的 vNet 中子网的名称 |
| AADClientID | 有权可将机密写入密钥保管库的 Azure AD 应用的客户端 ID |
| AADClientSecret | 有权可将机密写入密钥保管库的 Azure AD 应用的客户端机密 |
| keyVaultResourceID,ResourceID | 标识 ARM 中的密钥保管库资源。可以使用 PowerShell cmdlet 获取该 ID：(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).ResourceId |
| keyVaultURL | BitLocker 密钥应上载到的密钥保管库的 URL。可以使用 cmdlet 获取该 URL：(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL | 用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。这是可选的。 |
| vmName | 执行加密操作的 VM 的名称


**注意：**KeyEncryptionKeyURL 是可选参数。你可以使用自己的 KEK，在密钥保管库中进一步保护数据加密密钥（通行短语机密）。

### 在通过客户加密 VHD 和加密密钥创建的新 IaaS VM 上启用加密

在此方案中，你可以使用 ARM 模板、PowerShell cmdlet 或 CLI 命令启用加密。以下部分将详细介绍 ARM 模板和 CLI 命令。

#### 使用 ARM 模板

可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)发布的 ARM 模板，在客户加密的 VHD 上启用磁盘加密。单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入加密配置，然后单击“确定”。选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在新 IaaS VM 上启用加密。

下表描述了客户加密的 VHD 方案的 ARM 模板参数详细信息：

| 参数 | 说明|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | 用于存储加密 OS VHD 的存储帐户的名称。应该已在与 VM 相同的资源组和相同的位置中创建此存储帐户 |
| osVhdUri | 存储帐户中 OS VHD 的 URI |
| osType | OS 产品类型 (Windows/Linux) |
| virtualNetworkName | VM NIC 所属的 VNet 的名称。应该已在与 VM 相同的资源组和相同的位置中创建此项目 |
| subnetName | VM NIC 所属的 vNet 中子网的名称 |
| vmSize | VM 的大小目前仅支持标准 A、D 和 G 系列 |
| keyVaultResourceID | 用于标识 ARM 中密钥保管库资源的 ResourceID。可以使用 PowerShell cmdlet 获取该 ID：(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId |
| keyVaultSecretUrl | ​密钥保管库中预配的磁盘加密密钥的 URL |
| keyVaultKekUrl | 用于加密所生成磁盘加密密钥的密钥加密密钥的 URL |
| ​vmName | ​IaaS VM 的名称   



####使用 PowerShell cmdlet

可以使用[此处](https://msdn.microsoft.com/library/azure/mt603746.aspx)发布的 PS cmdlet，在客户加密的 VHD 上启用磁盘加密。

####使用 CLI 命令

遵循以下步骤使用 CLI 命令为此方案启用磁盘加密：

1. 设置对密钥保管库的访问策略：
	- 设置 ‘EnabledForDiskEncryption’ 标志：“azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- 向 Azure AD 应用设置将机密写入 KeyVault 的权限：“azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. 若要在现有/正在运行的 VM 上启用加密，请键入：
	*azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 获取加密状态：*“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. 若要从客户加密的 VHD 在新 VM 上启用加密，请将以下参数与“azure vm create”命令结合使用：
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### 在 Azure 中现有或正在运行的 IaaS Windows VM 上启用加密

在此方案中，你可以使用 ARM 模板、PowerShell cmdlet 或 CLI 命令启用加密。以下部分将详细介绍如何使用 ARM 模板和 CLI 命令启用加密。

#### 使用 ARM 模板

可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)发布的 ARM 模板，在 Azure 中现有/正在运行的 IaaS Windows VM 上启用磁盘加密。单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入加密配置，然后单击“确定”。选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在现有/正在运行的 IaaS VM 上启用加密。

下表中提供了使用 Azure AD 客户端 ID 的现有/正在运行的 VM 方案的 ARM 模板参数详细信息：

| 参数 | 说明|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​有权可将机密写入密钥保管库的 Azure AD 应用的客户端 ID |
| AADClientSecret | ​有权可将机密写入密钥保管库的 Azure AD 应用的客户端机密 |
| KeyVaultName | 应将 BitLocker 密钥上载到的密钥保管库的名称。可以使用 cmdlet 获取此名称：(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>)。Vaultname |
| ​ keyEncryptionKeyURL | 用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。如果在 UseExistingKek 下拉列表中选择“nokek”，则此参数为可选参数。如果在 UseExistingKek 下拉列表中选择“kek”，则必须输入 keyEncryptionKeyURL 值 |
| ​volumeType | ​执行加密操作的卷的类型。有效值为“OS”、“Data”、“All” |
| sequenceVersion | BitLocker 操作的序列版本。每当在同一个 VM 上执行磁盘加密操作时，此版本号便会递增 |
| ​vmName | ​执行加密操作的 VM 的名称


**注意：**KeyEncryptionKeyURL 是可选参数。你可以使用自己的 KEK，在密钥保管库中进一步保护数据加密密钥（BitLocker 加密机密）。

#### 使用 PowerShell cmdlet

有关如何使用 PS cmdlet 启用使用 Azure 磁盘加密的加密的详细信息，请参阅**探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密**博客文章[第 1 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)和[第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)。

#### 使用 CLI 命令

遵循以下步骤，使用 CLI 命令在 Azure 中现有/正在运行的 IaaS Windows VM 上启用加密：

1. 设置对密钥保管库的访问策略：
	- 设置 ‘EnabledForDiskEncryption’ 标志：“azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- 向 Azure AD 应用设置将机密写入 KeyVault 的权限：“azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. 若要在现有/正在运行的 VM 上启用加密，请键入：
	*azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 获取加密状态：*“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. 若要从客户加密的 VHD 在新 VM 上启用加密，请将以下参数与“azure vm create”命令结合使用：
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### 在 Azure 中现有或正在运行的 IaaS Linux VM 上启用加密

可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)发布的 ARM 模板，在 Azure 中现有/正在运行的 IaaS Linux VM 上启用磁盘加密。单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入加密配置，然后单击“确定”。选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在现有/正在运行的 IaaS VM 上启用加密。

下表描述了使用 Azure AD 客户端 ID 的现有/正在运行的 VM 方案的 ARM 模板参数详细信息：

| 参数 | 说明|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​有权可将机密写入密钥保管库的 Azure AD 应用的客户端 ID |
| AADClientSecret | ​有权可将机密写入密钥保管库的 Azure AD 应用的客户端机密 |
| KeyVaultName | 应将 BitLocker 密钥上载到的密钥保管库的名称。可以使用 cmdlet 获取此名称：(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>)。Vaultname |
| ​ keyEncryptionKeyURL | 用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。如果在 UseExistingKek 下拉列表中选择“nokek”，则此参数为可选参数。如果在 UseExistingKek 下拉列表中选择“kek”，则必须输入 keyEncryptionKeyURL 值 |
| ​volumeType | ​执行加密操作的卷的类型。支持的有效值为“Data”。Linux VM 不支持在运行 Linux VM 的 OS 卷上启用加密 |
| sequenceVersion | BitLocker 操作的序列版本。每当在同一个 VM 上执行磁盘加密操作时，此版本号便会递增 |
| ​vmName | ​执行加密操作的 VM 的名称
| passPhrase | 键入强通行短语作为数据加密密钥 |                                                                                                                                                                                                                                                      

**注意：**KeyEncryptionKeyURL 是可选参数。你可以使用自己的 KEK，在密钥保管库中进一步保护数据加密密钥（通行短语机密）。

#### CLI 命令

可以使用从[此处](xplat-cli-install.md)安装的 CLI 命令，在客户加密的 VHD 上启用磁盘加密。遵循以下步骤，使用 CLI 命令在 Azure 中现有/正在运行的 IaaS Linux VM 上启用加密：

1. 设置对密钥保管库的访问策略：
	- 设置 ‘EnabledForDiskEncryption’ 标志：“azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- 向 Azure AD 应用设置将机密写入 KeyVault 的权限：“azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. 若要在现有/正在运行的 VM 上启用加密，请键入：
	*azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 获取加密状态：“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”
4. 若要从客户加密的 VHD 在新 VM 上启用加密，请将以下参数与“azure vm create”命令结合使用：
	- *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
	- *disk-encryption-key-url <disk-encryption-key-url>*
	- *key-encryption-key-vault-id <key-encryption-key-vault-id>*
	- *key-encryption-key-url <key-encryption-key-url>*

### 获取已加密 IaaS VM 的加密状态

可以使用 Azure 管理门户、[PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt622700.aspx) 或 CLI 命令获取加密状态。以下部分将说明如何使用 Azure 管理（预览）门户和 CLI 命令来获取加密状态。

#### 使用 Azure 管理门户获取已加密 IaaS VM 的加密状态

可以从 Azure 管理门户获取 IaaS VM 的加密状态。在 https://portal.azure.com/ 处登录 Azure 门户，在左侧菜单中单击虚拟机链接以查看你的订阅中的虚拟机的摘要视图。可以从订阅下拉列表中选择订阅名称，以筛选虚拟机视图。单击位于虚拟机页面菜单顶部的列。从“选择列”边栏选项卡中选择“磁盘加密”列，然后单击“更新”。你应会看到，对于每个 VM，磁盘加密列会显示加密状态“已启用”或“未启用”，如下图所示。

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig2.JPG)

#### 使用磁盘加密 PS Cmdlet 获取已加密 IaaS VM 的加密状态
你可以使用磁盘加密 PS cmdlet“Get-AzureRmVMDiskEncryptionStatus”来获取 IaaS VM 的加密状态。若要获取 VM 的加密设置，请在 Azure PowerShell 会话中键入：

    PS C:\Windows\System32\WindowsPowerShell\v1.0> Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName <yourResourceGroupName> -VMName <yourVMName>

    OsVolumeEncrypted: True
    OsVolumeEncryptionSettings : {
      "DiskEncryptionKey": {
       SecretUrl":"https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            "SourceVault": {
            "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxxx/providers/Mi                            crosoft.KeyVault/vaults/xxxxxxx"
                                }
                            },
                    "KeyEncryptionKey": null
                             }
    DataVolumesEncrypted: True

OSVolumeEncrypted 和 DataVolumesEncrypted 设置值为“True”，表明这两个卷都已使用 Azure 磁盘加密进行加密。有关如何使用 PS cmdlet 启用使用 Azure 磁盘加密的加密的详细信息，请参阅**探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密**博客文章[第 1 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)和[第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)。

#### 通过磁盘加密 CLI 命令获取 IaaS VM 的加密状态。

你可以通过磁盘加密 CLI 命令 *azure vm show-disk-encryption-status* 获取 IaaS VM 的加密状态。若要获取 VM 的加密设置，请在 Azure CLI 会话中键入：

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  


## 附录

### 连接到订阅

在继续下一步之前，请务必查看本文档中的先决条件部分。确保已满足所有先决条件之后，请遵循以下步骤连接到你的订阅：

1\. 启动 Azure PowerShell 会话，然后使用以下命令登录你的 Azure 帐户：

    Login-AzureRmAccount -Environment $(GetRmAzureEnvironment -Name AzureChinaCloud)

2\. 如果你有多个订阅，并想要指定使用某个特定的订阅，请键入以下内容以查看帐户的订阅：

    Get-AzureRmSubscription

3\. 若要指定要使用的订阅，请键入：

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4\. 若要验证配置的订阅是否正确，请键入：

    Get-AzureRmSubscription

5\. 若要确认已安装 Azure 磁盘加密 cmdlet，请键入：

    Get-command *diskencryption*

6\. 你应会看到以下输出，其中确认已安装 Azure 磁盘加密 PowerShell：

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                               	 Version    Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus       1.1.0      AzureRM.Compute                                                    
    Cmdlet       Remove-AzureRmVMDiskEncryptionExtension 1.1.0      AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension    1.1.0      AzureRM.Compute                                                     

### 准备已预先加密的 Windows VHD
以下部分介绍了准备预先加密的 Windows VHD 以在 Azure IaaS 中部署为加密的 VHD 的必要操作。这些步骤用于在 Hyper-V 或 Azure 上准备和启动全新的 Windows VM (VHD)。

#### 更新组策略以允许使用非 TPM 保护 OS
你需要在“本地计算机策略”\\“计算机设置”\\“管理模板”\\“Windows 组件”下配置名为“BitLocker 驱动器加密”的 BitLocker 组策略设置。如下图所示，将此设置更改为“操作系统驱动器 - 启动时需要附加身份验证 - 没有兼容的 TPM 时允许 BitLocker”：

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig8.JPG)

#### 安装 BitLocker 功能组件
对于 Windows Server 2012 和更高版本，请使用以下命令：

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

对于 Windows Server 2008 R2，请使用以下命令：

    ServerManagerCmd -install BitLockers

#### 使用 bdehdcfg 为 BitLocker 准备 OS 卷

执行以下命令以压缩 OS 分区并为 BitLocker 准备计算机。

    bdehdcfg -target c: shrink -quiet

#### 使用 BitLocker 保护 OS 卷
使用 [manage-bde](https://technet.microsoft.com/library/ff829849.aspx) 命令以通过外部密钥保护程序在引导卷上启用加密，并将外部密钥（.bek 文件）放置在外部驱动器或卷上。下次重新启动后，将会在系统/引导卷上启用加密。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**注意：**必须先使用独立的数据/资源 VHD 准备 VM，才能使用 BitLocker 获取外部密钥。

#### 准备已预先加密的 Linux VHD

##### Ubuntu 14。

1\. 在 /usr/local/sbin/azure\_crypt\_key.sh 下创建一个包含以下脚本内容的文件。请注意 KeyFileName，因为它是 Azure 放置的通行短语文件名。

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    sleep 2
    OPENED=0
    for SFS in /sys/block/sd*; do
        DEV=`basename $SFS`
        F=$SFS/${DEV}1/dev
        echo "> Trying device: $DEV ..." >&2
        mount /dev/${DEV}1 $MountPoint -t vfat -r >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2\. 在 */etc/crypttab* 中更改 crypt 配置。它看起来应该如下所示：

    Sda5_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\. 如果要在 Windows 中编辑 *azure\_crypt\_key.sh* 并将它复制到 Linux，请记得运行 *dos2unix /usr/local/sbin/azure\_crypt\_key.sh*。
4. 运行 *update-initramfs -u -k all* 以更新 initramfs，使 keyscript 生效。

##### openSUSE 13.2。

1\. 编辑 /etc/dracut.conf add\_drivers+="vfat nls\_cp437 nls\_iso8859-1"

2\. 注释掉文件“/usr/lib/dracut/modules.d/90crypt/module-setup.sh”末尾的这些代码行：

    #    inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator


3\. 在文件“/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh”开头附加 DRACUT\_SYSTEMD=0，并将所有“if [ -z "$DRACUT\_SYSTEMD" ]; then”更改为“if [ 1 ]; then”

4\. 编辑 /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh 并将此代码附加在“# Open LUKS device”的后面

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
       echo "> Trying device:$SFS..." >&2
       mount ${SFS}1 $MountPoint -t vfat -r >&2
       if [ -f $MountPoint/$KeyFileName ]; then
          echo "> keyfile got..." >&2
          luksfile=$MountPoint/$KeyFileName
          break
       fi
    done

5\. 运行“dracut –f -v”以更新 initrd

##### CentOS 7
1\. 编辑 /etc/dracut.conf add\_drivers+="vfat nls\_cp437 nls\_iso8859-1"

2\. 注释掉文件“/usr/lib/dracut/modules.d/90crypt/module-setup.sh”末尾的这些代码行：

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\. 在文件“/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh”开头附加 DRACUT\_SYSTEMD=0，并将所有“if [ -z "$DRACUT\_SYSTEMD" ]; then”更改为“if [ 1 ]; then”

4\. 编辑 /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh 并将此代码附加在“# Open LUKS device”的后面

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        luksfile=$MountPoint/$KeyFileName
        break
    fi
    done


5\. 运行“/usr/sbin/dracut -f -v”以更新 initrd。

###将加密的 VHD 上载到 Azure 存储帐户
启用 BitLocker 加密或 DM-Crypt 加密后，需要将本地加密的 VHD 上载到存储帐户。

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### 将预先加密的 VM 的磁盘加密机密上载到密钥保管库
需要将前面获取的磁盘加密机密作为机密上载到密钥保管库中。

#### 未使用 KEK 加密的磁盘加密机密
使用 [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) 在密钥保管库中预配机密。对于 Windows 虚拟机，需将 bek 文件编码为 base64 字符串，然后使用 Set-AzureKeyVaultSecret cmdlet 将其上载到密钥保管库。对于 Linux，需将通行短语编码为 base64 字符串，然后将其上载到密钥保管库。此外，请确保在密钥保管库中创建机密时设置以下标记。

    "tags":
    {
       “DiskEncryptionKeyEncryptionAlgorithm”: “RSA-OAEP (optional)”
       "DiskEncryptionKeyFileName": "Bek file name (windows) or Passphrase filename (linux)"
    }

    param(
      [Parameter(Mandatory=$True)]
      [String]$BekFilePath = "C:\vm\nbox\2640EE52-41B3-426C-87B9-484232452CE4.BEK",
      [String]$VaultName = "DiskEncryptionTestAus",
      [String]$SecretName = "BitLockerKey"
      )

    #"EAN//ojeIQk="
    $bekFileName = split-path $BekFilePath -leaf
    echo "Bek file name = $bekFileName"

    $secretBytes = [System.IO.File]::ReadAllBytes($BekFilePath);
    $secret = [Convert]::ToBase64String($secretBytes);
    echo "Secret = $secret"

    $secureSecret = ConvertTo-SecureString $secret -AsPlainText -Force
    $tags = @{"DiskEncryptionKeyFileName" = "$bekFileName"}

    echo "Tags = $tags"
    echo "Vault = $VaultName"
    echo "Secret name = $SecretName"
    echo "Adding secret to Key vault"

    Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secureSecret -tags $tags


#### 使用 KEK 加密的磁盘加密机密

可以选择使用密钥加密密钥来加密机密，然后将其上载到密钥保管库。先使用包装 [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) 加密使用密钥加密密钥的机密。此包装操作的输出是 base64 URL 编码的字符串。然后使用 [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) cmdlet 将它上载为机密。


##下载此指南
可以从 [TechNet 库](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)下载此指南。


## 更多信息
[探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密 - 第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=Mooncake_0418_2016-->