---
title: "适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密 | Microsoft Docs"
description: "本文概述适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2016
ms.author: krkhan
translationtype: Human Translation
ms.sourcegitcommit: ca5b12c90814b9906389a12e8c72e2b4c266483f
ms.openlocfilehash: 3eb295e0ccec9a9410c0bb64e6ead10202b30289


---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密
Microsoft Azure 坚决承诺确保数据隐私权和数据所有权，并通过各种先进技术来加密、控制和管理加密密钥以及控制和审核对数据的访问，使你能够控制 Azure 托管的数据。 这样，Azure 客户便可以灵活选择最符合其业务需求的解决方案。 在本文中，我们将会介绍新的技术解决方案“适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密”，以帮助你保护数据，使你的组织能够信守在安全性与合规性方面所做的承诺。 本文提供有关如何使用 Azure 磁盘加密功能的详细指导，包括支持的方案和用户体验。

**注意**：本文中的某些建议可能会导致数据、网络或计算资源使用量增加，还可能导致许可或订阅成本增加。

## <a name="overview"></a>概述
Azure 磁盘加密是用于加密 Windows 和 Linux IaaS 虚拟机磁盘的新功能。 Azure 磁盘加密利用 Windows 的行业标准 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，为 OS 和数据磁盘提供卷加密。 该解决方案与 [Azure 密钥保管库](https://azure.microsoft.com/documentation/services/key-vault/)集成，可帮助你控制和管理密钥保管库订阅中的磁盘加密密钥和机密，同时确保虚拟机磁盘中的所有数据可在 Azure 存储空间中静态加密。

在标准 VM 和使用高级存储的 VM 的所有 Azure 公共区域中，适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密现在以**通用版本**提供。

### <a name="encryption-scenarios"></a>加密方案
Azure 磁盘加密解决方案支持以下客户方案：

* 在通过预加密 VHD 和加密密钥创建的新 IaaS VM 上启用加密
* 在通过 Azure 库映像创建的新 IaaS VM 上启用加密
* 在 Azure 中运行的现有 IaaS VM 上启用加密
* 在 Windows IaaS VM 上禁用加密
* 在 Linux IaaS VM 的数据驱动器上禁用加密

在 Microsoft Azure 中启用时，该解决方案支持以下 IaaS VM：

* 与 Azure 密钥保管库集成
* 标准层 VM - [A、D、DS、G 和 GS 等系列 IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)
* 在 Windows 和 Linux IaaS VM 上启用加密
* 在 Windows IaaS VM 的 OS 和数据驱动器上禁用加密
* 在 Linux IaaS VM 的数据驱动器上禁用加密
* 在运行 Windows 客户端 OS 的 IaaS VM 上启用加密
* 在包含装入路径的卷上启用加密
* 在使用 mdadm 配置了磁盘分段 (RAID) 的 Linux VM 上启用加密。
* 使用 LVM 对 Linux VM 上的数据磁盘启用加密。
* 在配置带有存储空间的 Windows VM 上启用加密
* 支持所有 Azure 公共区域

该解决方案不支持版本中的以下方案、功能和技术：

* 基本层 IaaS VM
* 在 Linux IaaS VM 的 OS 驱动器上禁用加密
* 使用经典 VM 创建方法创建的 IaaS VM
* 与本地密钥管理服务集成
* Azure 文件（Azure 文件共享）、网络文件系统 (NFS)、动态卷、配置带有基于软件的 RAID 系统的 Windows VM

### <a name="encryption-features"></a>加密功能
为 Azure IaaS VM 启用并部署 Azure 磁盘加密后，将会根据提供的配置启用以下功能：

* 加密 OS 卷以轻松保护客户存储中的引导卷
* 加密数据卷以轻松保护客户存储中的数据卷
* 在 Windows IaaS VM 的 OS 和数据驱动器上禁用加密
* 在 Linux IaaS VM 的数据驱动器上禁用加密
* 保护客户 Azure 密钥保管库订阅中的加密密钥和机密
* 报告已加密 IaaS VM 的加密状态
* 从 IaaS 虚拟机中删除磁盘加密配置设置

适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密解决方案包括适用于 Windows 的磁盘加密扩展、适用于 Linux 的磁盘加密扩展、磁盘加密 PowerShell cmdlet、磁盘加密 CLI cmdlet 和磁盘加密 Azure 资源管理器模板。 在运行 Windows 或 Linux OS 的 IaaS VM 上支持 Azure 磁盘加密解决方案。 有关支持的操作系统的详细信息，请参阅以下先决条件部分。

**注意**：可以免费使用 Azure 磁盘加密来加密 VM 磁盘。

### <a name="value-proposition"></a>价值主张
Azure 磁盘加密管理解决方案可以解决云中的以下业务需求：

* 使用行业标准的加密技术轻松保护 IaaS VM，满足组织的安全性与合规性要求。
* IaaS VM 将会根据客户控制的密钥和策略启动，客户可以在密钥保管库中审核密钥和策略的使用方式。

### <a name="encryption-workflow"></a>加密工作流
为 Windows 和 Linux VM 启用磁盘加密所需的高级步骤如下：

1. 客户从上述加密方案中选择加密方案
2. 客户选择通过 Azure 磁盘加密资源管理器模板、PS cmdlet 或 CLI 命令启用磁盘加密，并指定加密配置
   
   * 针对客户加密的 VHD 方案，客户将加密的 VHD 上载到其存储帐户，将加密密钥材料上载到其密钥保管库，并提供加密配置以便在新 IaaS VM 上启用加密
   * 针对通过 Azure 库和已在 Azure 中运行的现有 VM 创建新的 VM，客户将提供加密配置以便在 IaaS VM 上启用加密
3. 客户向 Azure 平台授予访问权限，使其能够从密钥保管库中读取加密密钥数据（Windows 系统的 BitLocker 加密密钥和 Linux 的通行短语），从而在 IaaS VM 上启用加密
4. 客户提供 Azure AD 应用程序标识以便将加密密钥材料写入其密钥保管库，从而按上述方案 2 所述在 IaaS VM 上启用加密
5. Azure 使用加密和密钥保管库配置更新 VM 服务模型，并为客户预配加密的 VM

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>解密工作流
为 IaaS VM 禁用磁盘加密所需的高级步骤如下：

1. 客户选择通过 Azure 磁盘加密资源管理器模板或 PS cmdlet 在 Azure 中运行的 laaS VM 上禁用加密（解密），并指定解密配置。
2. 禁用加密步骤将禁用正在运行的 Windows IaaS VM 上的 OS 卷和/或数据卷的加密。 但是如上述文档所述，不支持对 Linux 禁用 OS 磁盘加密。 仅允许对 Linux VM 上的数据驱动器执行禁用步骤。 
3. Azure 将更新 VM 服务模型，IaaS VM 将标记为已解密。 VM 的内容不再静态加密。
4. 禁用加密操作不会删除客户密钥保管库和加密密钥材料（Windows 的 BitLocker 加密密钥或 Linux 的通行短语）。

## <a name="prerequisites"></a>先决条件
以下是针对概述部分提到支持的方案，在 Azure IaaS VM 上启用 Azure 磁盘加密所要满足的先决条件

* 用户必须具有有效的活动 Azure 订阅，才能在 Azure 的受支持区域中创建资源
* 以下 Windows 服务器 SKU 支持 Azure 磁盘加密 - Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016。
* 以下 Windows 客户端 SKU 支持 Azure 磁盘加密 - Windows 8 Client 和 Windows 10 Client。

**注意**：对于 Windows Server 2008 R2，必须先安装 .NET Framework 4.5，然后才能在 Azure 中启用加密。 你可以通过安装可选更新“适用于 Windows Server 2008 R2 x64 系统的 Microsoft .NET Framework 4.5.2 ([KB2901983](https://support.microsoft.com/kb/2901983))”，从 Windows 更新安装它

* 以下 Linux 服务器 SKU 支持 Azure 磁盘加密 - Ubuntu、CentOS、SUSE、SUSE Linux Enterprise Server (SLES) 和 Red Hat Enterprise Linux。

**注意**：下面的 Linux 分发（RHEL 7.2、CentOS 7.2、Ubuntu 16.04）当前支持 Linux OS 磁盘加密

* 所有资源（例如密钥保管库、存储帐户和 VM 等）必须属于同一个 Azure 区域和订阅。

**注意**：Azure 磁盘加密要求密钥保管库和 VM 位于同一 Azure 区域。 在不同区域中配置它们会导致启用 Azure 磁盘加密功能失败。

* 若要根据 Azure 磁盘加密的使用方式设置和配置 Azure 密钥保管库，请参阅本文*先决条件*中的**根据 Azure 磁盘加密的使用方式设置和配置 Azure 密钥保管库**部分。
* 若要根据 Azure 磁盘加密的使用方式，在 Azure Active Drectory 中设置和配置 Azure AD 应用程序，请参阅本文*先决条件*中的**在 Azure Active Directory 中设置 Azure AD 应用程序**部分。
* 若要设置和配置 Azure AD 应用程序的密钥保管库访问策略，请参阅本文*先决条件*中的**为 Azure AD 应用程序设置密钥保管库访问策略**部分。
* 若要准备预先加密的 Windows VHD，请参阅本文附录中的**准备已预先加密的 Windows VHD** 部分。
* 若要准备预先加密的 Linux VHD，请参阅本文附录中的**准备已预先加密的 Linux VHD** 部分。
* Azure 平台需要访问客户 Azure 密钥保管库中的加密密钥或机密，才能使这些密钥和机密可供虚拟机用来启动和解密虚拟机 OS 卷。 若要向 Azure 平台授予访问客户密钥保管库的权限，必须针对此要求在密钥保管库上设置 **enabledForDiskEncryption** 属性。 有关详细信息，请参阅本文附录中的**根据 Azure 磁盘加密的使用方式设置和配置 Azure 密钥保管库**部分。
* 密钥保管库机密和密钥加密密钥 (KEK) URL 必须已设置版本。 Azure 会强制实施这项版本控制限制。 请参阅以下有效机密和 KEK URL 的示例：
  * 有效机密 URL 的示例：  *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 有效 KRK KEK 的示例：  *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Azure 磁盘加密不支持将端口号指定为密钥保管库机密和 KEK URL 的一部分。 有关支持的密钥保管库 URL，请参阅以下示例：
  * 不接受的密钥保管库 URL： *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    * 接受的密钥保管库 URL：*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 若要启用 Azure 磁盘加密功能，IaaS VM 必须符合以下网络终结点配置要求： 
  * IaaS VM 必须能够连接到 Azure Active Directory 终结点 \[Login.windows.net\]，以获取用于连接 Azure 密钥保管库的令牌
  * IaaS VM 必须能够连接到 Azure 密钥保管库终结点，以将加密密钥写入客户密钥保管库
  * IaaS VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户

**注意：**如果你的安全策略限制从 Azure VM 到 Internet 的访问，则你可以解析上述要连接到的 URI，并配置特定的规则以允许与这些 IP 建立出站连接。

* 使用最新版本的 Azure PowerShell SDK 来配置 Azure 磁盘加密。 下载最新的 [Azure PowerShell 发行版](https://github.com/Azure/azure-powershell/releases)

**注意：**[Azure PowerShell SDK 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016) 不支持 Azure 磁盘加密。 如果你收到与使用 Azure PowerShell 1.1.0 相关的错误，请参阅 [Azure Disk Encryption Error Related to Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx)（与 Azure PowerShell 1.1.0 相关的 Azure 磁盘加密错误）一文。

* 若要运行任何 Azure CLI 命令并将其与 Azure 订阅关联，必须首先安装 Azure CLI 版本：
  * 若要安装 Azure CLI 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure CLI](../xplat-cli-install.md)
  * 将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理器配合使用，请参阅[此处](../virtual-machines/azure-cli-arm-commands.md)
* Azure 磁盘加密解决方案对 Windows IaaS VM 使用 BitLocker 外部密钥保护程序。 如果 VM 已加入域，请不要推送会强制使用 TPM 保护程序的任何组策略。 请参阅[此文](https://technet.microsoft.com/library/ee706521)，以了解“没有兼容的 TPM 时允许 BitLocker”组策略的详细信息。
* 用于创建 Azure AD 应用程序、创建新的密钥保管库或设置现有密钥保管库并启用加密的 Azure 磁盘加密先决条件 PowerShell 脚本位于[此处](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)。

#### <a name="setup-the-azure-ad-application-in-azure-active-directory"></a>在 Azure Active Directory 中设置 Azure AD 应用程序
需要在 Azure 中正在运行的 VM 上启用加密时，Azure 磁盘加密将生成加密密钥并将其写入密钥保管库。 在密钥保管库中管理加密密钥需要 Azure AD 身份验证。

为此，应该创建 Azure AD 应用程序。 注册应用程序的详细步骤可在此[博客文章](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)的“获取应用程序的标识”部分中找到。  这篇文章还包含一些有关预配和配置密钥保管库的有用示例。 对于身份验证，可以使用基于客户端机密的身份验证或基于客户端证书的 Azure AD 身份验证。

##### <a name="client-secret-based-authentication-for-azure-ad"></a>Azure AD 的基于客户端机密的身份验证
以下部分提供了为 Azure AD 配置基于客户端机密的身份验证所要执行的步骤。

##### <a name="create-a-new-azure-ad-app-using-azure-powershell"></a>使用 Azure PowerShell 创建新的 Azure AD 应用
使用以下 PowerShell cmdlet 来创建新的 Azure AD 应用：

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**注意：**$azureAdApplication.ApplicationId 是 Azure AD ClientID，$aadClientSecret 是客户端机密，稍后你应该使用这些信息来启用 ADE。你应该妥善保护 Azure AD 客户端机密。

##### <a name="provisioning-the-azure-ad-client-id-and-secret-from-the-azure-classic-deployment-model-portal"></a>从 Azure 经典部署模型门户预配 Azure AD 客户端 ID 和机密
也可以使用 Azure 经典部署模型门户 (https://manage.windowsazure.com) 来预配 Azure AD 客户端 ID 和机密，请遵循以下步骤执行此任务：

1. 单击“Active Directory”选项卡，如下图所示：

![Azure 磁盘加密](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. 单击“添加应用程序”，然后键入应用程序名称，如下所示：

![Azure 磁盘加密](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. 单击箭头按钮并配置应用程序的属性，如下所示：

![Azure 磁盘加密](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. 单击左下角的复选标记完成操作。 此时将显示应用的配置页面。 注意 Azure AD 客户端 ID 位于页面底部，如下图所示。

![Azure 磁盘加密](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. 单击“保存”按钮保存 Azure AD 客户端机密。 单击“保存”按钮并记下“密钥”文本框中的机密，这是 Azure AD 客户端机密。 你应该妥善保护 Azure AD 客户端机密。

![Azure 磁盘加密](./media/azure-security-disk-encryption/disk-encryption-fig7.png)

**注意：**门户不支持上述流程。

##### <a name="use-an-existing-app"></a>使用现有的应用
若要执行以下命令，你需要 Azure AD PowerShell 模块，可从[此处](https://technet.microsoft.com/library/jj151815.aspx)获取该模块。

**注意：**必须在新的 PowerShell 窗口中执行以下命令。 请不要使用 Azure PowerShell 或 Azure 资源管理器窗口执行这些命令。 提出这项建议的原因是这些 cmdlet 位于 MSOnline 模块或 Azure AD PowerShell 中。

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Azure AD 的基于证书的身份验证
> [!NOTE]
> 在 Linux VM 上当前不支持 AAD 基于证书的身份验证。
> 
> 

以下部分提供了为 Azure AD 配置基于证书的身份验证所要执行的步骤。

##### <a name="create-a-new-azure-ad-app"></a>创建新的 Azure AD 应用
执行以下 PowerShell cmdlet 以创建新的 Azure AD 应用：

**注意：**请将 `yourpassword` 字符串替换为你的安全密码并保护该密码。

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

完成此步骤后，请将 .pfx 文件上载到密钥保管库，并启用将该证书部署到 VM 所需的访问策略。

##### <a name="use-an-existing-azure-ad-app"></a>使用现有的 Azure AD 应用
如果要为现有应用配置基于证书的身份验证，请使用以下 PowerShell cmdlet。 请务必在新的 PowerShell 窗口中执行这些 cmdlet。

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

完成此步骤后，请将 .pfx 文件上载到密钥保管库，并启用将该证书部署到 VM 所需的访问策略。

##### <a name="upload-a-pfx-file-to-key-vault"></a>将 PFX 文件上载到密钥保管库
有关如何运行此过程的详细说明，请阅读此[博客文章](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)。 不过，只需执行以下 PowerShell cmdlet 即可完成此任务。 请务必通过 Azure PowerShell 控制台执行这些 cmdlet：

**注意：**请将 `yourpassword` 字符串替换为你的安全密码并保护该密码。

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

##### <a name="deploy-a-certificate-in-key-vault-to-an-existing-vm"></a>将密钥保管库中的证书部署到现有 VM
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


#### <a name="setting-key-vault-access-policy-for-the-azure-ad-application"></a>为 Azure AD 应用程序设置密钥保管库访问策略
Azure AD 应用程序需有访问保管库中密钥或机密的权限。 使用 [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) cmdlet，并将客户端ID（注册应用程序时所生成）用作 -ServicePrincipalName 参数值，即可向应用程序授予权限。 你可以阅读此[博客文章](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)以获取一些相关的示例。 下面还提供了有关如何通过 PowerShell 执行此任务的示例：

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

**注意**：Azure 磁盘加密要求你为 AAD 客户端应用程序配置以下访问策略 -“WrapKey”和“Set”权限

## <a name="terminology"></a>术语
参考该术语表来理解本技术所用的一些常见术语：

| 术语 | 定义 |
| --- | --- |
| Azure AD |Azure AD 是 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) 的缩写。 若要从密钥保管库进行身份验证以及存储和检索机密，必须有一个 Azure AD 帐户。 |
| Azure 密钥保管库 [AKV] |Azure 密钥保管库是基于 FIPS 验证的硬件安全模块的加密密钥管理服务，可以安全保护加密密钥和敏感机密。有关详细信息，请参阅[密钥保管库](https://azure.microsoft.com/services/key-vault/)文档。 |
| ARM |Azure Resource Manager |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) 是一种行业认可的 Windows 卷加密技术，用于在 Windows IaaS VM 上启用磁盘加密 |
| BEK |BitLocker 加密密钥用于加密 OS 引导卷和数据卷。 BitLocker 密钥在客户的 Azure 密钥保管库中以机密形式进行保护。 |
| CLI |[Azure 命令行接口](../xplat-cli-install.md) |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 是基于 Linux 的透明磁盘加密子系统，用于在 Linux IaaS VM 上启用磁盘加密 |
| KEK |密钥加密密钥是非对称密钥 (RSA 2048)，用于在需要时保护或包装机密。 你可以提供 HSM 保护的密钥或软件保护的密钥。 有关详细信息，请参阅 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)文档 |
| PS cmdlet |[Azure PowerShell cmdlet](../powershell-install-configure.md) |

### <a name="setting-and-configuring-azure-key-vault-for-azure-disk-encryption-usage"></a>根据 Azure 磁盘加密的使用方式设置和配置 Azure 密钥保管库
Azure 磁盘加密将保护 Azure 密钥保管库中的磁盘加密密钥和机密。 请遵循以下每个部分中的步骤，根据 Azure 磁盘加密使用方式设置密钥保管库。

#### <a name="create-a-new-key-vault"></a>创建新的密钥保管库
若要创建新的密钥保管库，请使用下列选项之一：

* 使用位于[此处](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)的“101-Create-KeyVault”资源管理器模板
* 使用 Azure PowerShell [密钥保管库 cmdlet](https://msdn.microsoft.com/library/dn868052.aspx)。
* 使用 Azure 资源管理器门户。

**注意：**如果你已为订阅设置密钥保管库，请转到下一部分。

![Azure 密钥保管库](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="provisioning-a-key-encryption-key-optional"></a>预配密钥加密密钥（可选）
如果你想要使用密钥加密密钥 (KEK) 获得附加的安全层以包装 BitLocker 加密密钥，你应将 KEK 添加到密钥保管库以便在预配过程中使用。  使用 [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) cmdlet 可在密钥保管库中创建新的密钥加密密钥。 你还可以从本地密钥管理 HSM 导入 KEK。 有关详细信息，请参阅[密钥保管库文档](https://azure.microsoft.com/documentation/services/key-vault/)。

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

可以从 Azure 资源管理器门户添加 KEK，也可以使用 Azure 密钥保管库 UX 添加。

![Azure 密钥保管库](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions-to-allow-the-azure-platform-access-to-the-keys-and-secrets"></a>设置密钥保管库权限以允许 Azure 平台访问密钥和机密
Azure 平台需要访问 Azure 密钥保管库中的加密密钥或机密，才能使这些密钥和机密可供 VM 用来启动和卷。 若要向 Azure 平台授予权限以使其能够访问密钥保管库，必须在密钥保管库上设置 *enabledForDiskEncryption* 属性。 可以使用密钥保管库 PS cmdlet 在密钥保管库上设置 enabledForDiskEncryption 属性：

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

你还可以通过访问 https://resources.azure.com 来设置 *enabledForDiskEncryption* 属性。 必须如前所述在密钥保管库上设置 *enabledForDiskEncryption* 属性。 否则，部署将失败。

你可以从密钥保管库 UX 设置 AAD 应用的访问策略：

![Azure 密钥保管库](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure 密钥保管库](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

确保在“高级访问策略”中为磁盘加密启用了密钥保管库：

![Azure 密钥保管库](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>磁盘加密部署方案和用户体验
你可以在许多方案中启用磁盘加密，具体步骤根据方案的不同而异。 以下部分将会根据这些方案详细介绍如何操作。

### <a name="enable-encryption-on-new-iaas-vms-created-from-the-azure-gallery"></a>在通过 Azure 库创建的新 IaaS VM 上启用加密
可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)发布的资源管理器模板在通过 Azure 的 Azure 库创建的新 IaaS Windows VM 上启用磁盘加密。 单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入加密配置，然后单击“确定”。 选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在新 IaaS VM 上启用加密。

**注意：**此模板使用 Windows Server 2012 库映像创建新的加密 Windows VM。

可以使用[此](https://aka.ms/fde-rhel)资源管理器模板在带有 200 GB RAID-0 数组的新 IaaS RedHat Linux 7.2 VM 上启用磁盘加密。 部署模板后，请按照“[在运行的 Linux VM 上加密 OS 驱动器](#encrypting-os-drive-on-a-running-linux-vm)”一节中所述使用 `Get-AzureRmVmDiskEncryptionStatus` cmdlet 验证 VM 加密状态。 当计算机返回状态 `VMRestartPending` 时，重新启动 VM。

你可以在下表中使用 Azure AD 客户端 ID 查看 Azure 库方案中新 VM 的资源管理器模板参数详细信息：

| 参数 | 说明 |
| --- | --- |
| adminUserName |虚拟机的管理员用户名 |
| adminPassword |虚拟机的管理员用户密码 |
| newStorageAccountName |用于存储 OS 和数据 VHD 的存储帐户的名称 |
| vmSize |VM 的大小 目前仅支持标准 A、D 和 G 系列 |
| virtualNetworkName |VM NIC 所属的 VNet 的名称。 |
| subnetName |VM NIC 所属的 vNet 中子网的名称 |
| AADClientID |有权可将机密写入密钥保管库的 Azure AD 应用的客户端 ID |
| AADClientSecret |有权可将机密写入密钥保管库的 Azure AD 应用的客户端机密 |
| keyVaultURL |BitLocker 密钥应上载到的密钥保管库的 URL。 可以使用 cmdlet 获取该 URL：(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL |用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。 这是可选的。 |
| keyVaultResourceGroup |密钥保管库的资源组 |
| vmName |执行加密操作的 VM 的名称 |

**注意：**KeyEncryptionKeyURL 是可选参数。 你可以使用自己的 KEK，在密钥保管库中进一步保护数据加密密钥（通行短语机密）。

### <a name="enable-encryption-on-new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>在通过客户加密 VHD 和加密密钥创建的新 IaaS VM 上启用加密
在此方案中，可以通过使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 以下部分将详细介绍资源管理器模板和 CLI 命令。

按照以下某一部分的说明操作，准备可在 Azure 中使用的预先加密映像。 创建映像后，下一部分中的步骤可用于创建加密的 Azure VM。

* [准备预先加密的 Windows VHD](#preparing-a-pre-encrypted-windows-vhd)
* [准备预先加密的 Linux VHD](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-resource-manager-template"></a>使用资源管理器模板
可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)发布的资源管理器模板对客户加密的 VHD 启用磁盘加密。 单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入加密配置，然后单击“确定”。 选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在新 IaaS VM 上启用加密。

下表描述了客户加密 VHD 方案的资源管理器模板参数详细信息：

| 参数 | 说明 |
| --- | --- |
| newStorageAccountName |用于存储加密 OS VHD 的存储帐户的名称。 应该已在与 VM 相同的资源组和相同的位置中创建此存储帐户 |
| osVhdUri |存储帐户中 OS VHD 的 URI |
| osType |OS 产品类型 (Windows/Linux) |
| virtualNetworkName |VM NIC 所属的 VNet 的名称。 应该已在与 VM 相同的资源组和相同的位置中创建此项目 |
| subnetName |VM NIC 所属的 vNet 中子网的名称 |
| vmSize |VM 的大小 目前仅支持标准 A、D 和 G 系列 |
| keyVaultResourceID |用于标识 ARM 中密钥保管库资源的 ResourceID。 你可以使用 PowerShell cmdlet 获取该 ID：(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId |
| keyVaultSecretUrl |密钥保管库中预配的磁盘加密密钥的 URL |
| keyVaultKekUrl |用于加密所生成磁盘加密密钥的密钥加密密钥的 URL |
| vmName |IaaS VM 的名称 |

#### <a name="using-powershell-cmdlets"></a>使用 PowerShell cmdlet
可以使用[此处](https://msdn.microsoft.com/library/azure/mt603746.aspx)发布的 PS cmdlet 对客户加密的 VHD 启用磁盘加密。  

#### <a name="using-cli-commands"></a>使用 CLI 命令
遵循以下步骤使用 CLI 命令为此方案启用磁盘加密：

1. 设置对密钥保管库的访问策略：
   * 设置“EnabledForDiskEncryption”标志：`azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * 向 Azure AD 应用设置将机密写入 KeyVault 的权限：`azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]`
2. 若要在现有/正在运行的 VM 上启用加密，请键入： *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 获取加密状态：*“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. 若要从客户加密的 VHD 在新 VM 上启用加密，请将以下参数与“azure vm create”命令结合使用：
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>在 Azure 中现有或正在运行的 IaaS Windows VM 上启用加密
在此方案中，可以通过使用资源管理器模板、PowerShell cmdlet 或 CLI 命令启用加密。 以下部分将详细介绍如何使用资源管理器模板和 CLI 命令启用加密。

#### <a name="using-resource-manager-template"></a>使用资源管理器模板
可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)发布的资源管理器模板，在 Azure 中现有/正在运行的 IaaS Windows VM 上启用磁盘加密。 单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入加密配置，然后单击“确定”。 选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在现有/正在运行的 IaaS VM 上启用加密。

下表中提供了使用 Azure AD 客户端 ID 的现有/正在运行的 VM 方案的资源管理器模板参数详细信息：

| 参数 | 说明 |
| --- | --- |
| AADClientID |有权可将机密写入密钥保管库的 Azure AD 应用的客户端 ID |
| AADClientSecret |有权可将机密写入密钥保管库的 Azure AD 应用的客户端机密 |
| KeyVaultName |应将 BitLocker 密钥上载到的密钥保管库的名称。 可以使用 cmdlet 获取此名称：(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>)。 Vaultname |
|  keyEncryptionKeyURL |用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。 如果在 UseExistingKek 下拉列表中选择 `nokek`，则此参数为可选参数。 如果在 UseExistingKek 下拉列表中选择 `kek`，则必须输入 keyEncryptionKeyURL 值 |
| volumeType |执行加密操作的卷的类型。 有效值为“OS”、“Data”、“All” |
| sequenceVersion |BitLocker 操作的序列版本。 每当在同一个 VM 上执行磁盘加密操作时，此版本号便会递增 |
| vmName |执行加密操作的 VM 的名称 |

**注意：**KeyEncryptionKeyURL 是可选参数。 你可以使用自己的 KEK，在密钥保管库中进一步保护数据加密密钥（BitLocker 加密机密）。

#### <a name="using-powershell-cmdlets"></a>使用 PowerShell cmdlet
有关如何使用 PS cmdlet 启用使用 Azure 磁盘加密的加密的详细信息，请参阅**探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密**博客文章[第 1 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)和[第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)。

#### <a name="using-cli-commands"></a>使用 CLI 命令
遵循以下步骤，使用 CLI 命令在 Azure 中现有/正在运行的 IaaS Windows VM 上启用加密：

1. 设置对密钥保管库的访问策略：
   * 设置 ‘EnabledForDiskEncryption’ 标志：“azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
   * 向 Azure AD 应用设置将机密写入 KeyVault 的权限：“azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]”
2. 若要在现有/正在运行的 VM 上启用加密，请键入： *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 获取加密状态：*“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. 若要从客户加密的 VHD 在新 VM 上启用加密，请将以下参数与“azure vm create”命令结合使用：
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>

### <a name="enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure"></a>在 Azure 中现有或正在运行的 IaaS Linux VM 上启用加密
可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)发布的资源管理器模板，在 Azure 中现有/正在运行的 IaaS Linux VM 上启用磁盘加密。 单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入加密配置，然后单击“确定”。 选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在现有/正在运行的 IaaS VM 上启用加密。

下表中描述了使用 Azure AD 客户端 ID 的现有/正在运行的 VM 方案的资源管理器模板参数详细信息：

| 参数 | 说明 |
| --- | --- |
| AADClientID |有权可将机密写入密钥保管库的 Azure AD 应用的客户端 ID |
| AADClientSecret |有权可将机密写入密钥保管库的 Azure AD 应用的客户端机密 |
| KeyVaultName |应将 BitLocker 密钥上载到的密钥保管库的名称。 可以使用 cmdlet 获取此名称：(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>)。 Vaultname |
|  keyEncryptionKeyURL |用于加密所生成 BitLocker 密钥的密钥加密密钥的 URL。 如果在 UseExistingKek 下拉列表中选择“nokek”，则此参数为可选参数。 如果在 UseExistingKek 下拉列表中选择“kek”，则必须输入 keyEncryptionKeyURL 值 |
| volumeType |执行加密操作的卷的类型。 受支持的有效值为“OS”/“All”（对于 RHEL 7.2、CentOS 7.2 和 Ubuntu 16.04）和“Data”（对于所有其他发行版）。 |
| sequenceVersion |BitLocker 操作的序列版本。 每当在同一个 VM 上执行磁盘加密操作时，此版本号便会递增 |
| vmName |执行加密操作的 VM 的名称 |
| passPhrase |键入强通行短语作为数据加密密钥 |

**注意：**KeyEncryptionKeyURL 是可选参数。 你可以使用自己的 KEK，在密钥保管库中进一步保护数据加密密钥（通行短语机密）。

#### <a name="cli-commands"></a>CLI 命令
可以使用从[此处](../xplat-cli-install.md)安装的 CLI 命令，在客户加密的 VHD 上启用磁盘加密。 遵循以下步骤，使用 CLI 命令在 Azure 中现有/正在运行的 IaaS Linux VM 上启用加密：

1. 设置对密钥保管库的访问策略：
   * 设置 ‘EnabledForDiskEncryption’ 标志：“azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
   * 向 Azure AD 应用设置将机密写入 KeyVault 的权限：“azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]”
2. 若要在现有/正在运行的 VM 上启用加密，请键入： *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 获取加密状态：“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”
4. 若要从客户加密的 VHD 在新 VM 上启用加密，请将以下参数与“azure vm create”命令结合使用：
   * *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
   * *disk-encryption-key-url <disk-encryption-key-url>*
   * *key-encryption-key-vault-id <key-encryption-key-vault-id>*
   * *key-encryption-key-url <key-encryption-key-url>*

### <a name="get-encryption-status-of-an-encrypted-iaas-vm"></a>获取已加密 IaaS VM 的加密状态
可以使用 Azure 资源管理器门户、[PowerShell cmdlets](https://msdn.microsoft.com/library/azure/mt622700.aspx) 或 CLI 命令获取加密状态。 以下部分将说明如何使用 Azure 门户和 CLI 命令来获取加密状态。

#### <a name="get-encryption-status-of-an-encrypted-windows-vm-using-azure-resource-manager-portal"></a>使用 Azure 资源管理器门户获取已加密 Windows VM 的加密状态
可以从 Azure 资源管理器门户获取 IaaS VM 的加密状态。 登录 Azure 门户 (https://portal.azure.com/)，在左侧菜单中单击虚拟机链接以查看你的订阅中的虚拟机的摘要视图。 可以通过从订阅下拉列表中选择订阅名称来筛选虚拟机视图。 单击位于虚拟机页面菜单顶部的列。 从“选择列”边栏选项卡中选择“磁盘加密”列，然后单击“更新”。 你应会看到，对于每个 VM，磁盘加密列会显示加密状态“已启用”或“未启用”，如下图所示。

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-encryption-status-of-an-encrypted-windowslinux-iaas-vm-using-disk-encryption-ps-cmdlet"></a>使用磁盘加密 PS Cmdlet 获取已加密 (Windows/Linux) IaaS VM 的加密状态
你可以使用磁盘加密 PS cmdlet“Get-AzureRmVMDiskEncryptionStatus”来获取 IaaS VM 的加密状态。 若要获取 VM 的加密设置，请在 Azure PowerShell 会话中键入：

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

可以检查 Get-AzureRmVMDiskEncryptionStatus 的输出来获取加密密钥 URL。

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMNam
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

OSVolumeEncrypted 和 DataVolumesEncrypted 设置值为“Encrypted”，表明这两个卷都已使用 Azure 磁盘加密进行加密。 有关如何使用 PS cmdlet 启用使用 Azure 磁盘加密的加密的详细信息，请参阅**探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密**博客文章[第 1 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)和[第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)。

**注意**：在 Linux VM 上，`Get-AzureRmVMDiskEncryptionStatus` cmdlet 需要 3-4 分钟来报告加密状态。

#### <a name="get-encryption-status-of-the-iaas-vm-from-disk-encryption-cli-command"></a>通过磁盘加密 CLI 命令获取 IaaS VM 的加密状态。
你可以通过磁盘加密 CLI 命令 *azure vm show-disk-encryption-status* 获取 IaaS VM 的加密状态。 若要获取 VM 的加密设置，请在 Azure CLI 会话中键入：

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>在正在运行的 Windows IaaS VM 上禁用加密
你可以通过 Azure 磁盘加密资源管理器模板或 PS cmdlet 在运行的 Windows 或 Linux IaaS VM 上禁用加密，并指定解密配置。

##### <a name="windows-vm"></a>Windows VM
禁用加密步骤将禁用正在运行的 Windows IaaS VM 上的 OS 卷和/或数据卷的加密。 你无法禁用 OS 卷并保持数据卷的加密状态。 执行禁用加密步骤后，Azure 经典部署模型将更新 VM 服务模型，Windows IaaS VM 将标记为已解密。 VM 的内容不再静态加密。 禁用加密不会删除客户密钥保管库和加密密钥材料（Windows 的 BitLocker 加密密钥和 Linux 的通行短语）。 

##### <a name="linux-vm"></a>Linux VM
禁用加密步骤将禁用正在运行的 Linux IaaS VM 上的数据卷的加密

**注意**：在 Linux VM 上不允许禁用 OS 磁盘上的加密。

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-resource-manager-template"></a>使用资源管理器模板在 Azure 中的现有/正在运行的 IaaS VM 上禁用加密
可以使用[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)发布的资源管理器模板，在正在运行的 Windows IaaS VM 上禁用磁盘加密。 单击 Azure 快速入门模板上的“部署到 Azure”按钮，在参数边栏选项卡中输入解密配置，然后单击“确定”。 选择订阅、资源组、资源组位置、法律条款和协议，然后单击“创建”按钮以在新 IaaS VM 上启用加密。

对于 Linux VM，[此](https://aka.ms/decrypt-linuxvm)模板可用于禁用加密。

用于在正在运行的 IaaS VM 上禁用加密的资源管理器模板参数详细信息：

| vmName | 执行加密操作的 VM 的名称 |
| --- | --- |
| volumeType |执行解密操作的卷的类型。 有效值为“OS”、“Data”、“All”。 **注意：**如果未在“Data”卷上禁用加密，则无法在运行中的 Windows IaaS VM OS/引导卷上禁用加密。 **注意**：在 Linux VM 上不允许禁用 OS 磁盘上的加密。 |
| sequenceVersion |BitLocker 操作的序列版本。 每当在同一个 VM 上执行磁盘解密操作时，此版本号便会递增 |

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-ps-cmdlet"></a>使用 PS cmdlet 在 Azure 中的现有/正在运行的 IaaS VM 上禁用加密
若要使用 PS cmdlet 禁用，可以使用 [Disable-AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) cmdlet 在服务架构 (IaaS) 虚拟机上禁用加密。 此 cmdlet 同时支持 Windows 和 Linux VM。 此 cmdlet 将在虚拟机上安装一个用于禁用加密的扩展。 如果未指定 Name 参数，将创建默认名称为“AzureDiskEncryption for Windows VMs”的扩展。 

在 Linux VM 上，使用“AzureDiskEncryptionForLinux”扩展。

**注意**：运行此 cmdlet 会重新启动虚拟机。 

## <a name="appendix"></a>附录
### <a name="connect-to-your-subscription"></a>连接到订阅
在继续下一步之前，请务必查看本文档中的*先决条件*部分。 确保满足所有先决条件后，执行以下步骤来连接到你的订阅：

1. 启动 Azure PowerShell 会话，然后使用以下命令登录你的 Azure 帐户：

    Login-AzureRmAccount

2. 如果你有多个订阅，并想要指定其中一个要使用的订阅，请键入以下内容以查看帐户的订阅：

    Get-AzureRmSubscription

3. 若要指定要使用的订阅，请键入：

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4. 若要验证配置的订阅是否正确，请键入：

    Get-AzureRmSubscription

5. 若要确认已安装 Azure 磁盘加密 cmdlet，请键入：

    Get-command *diskencryption*

6. 你应会看到以下输出，其中确认已安装 Azure 磁盘加密 PowerShell：

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     

### <a name="preparing-a-pre-encrypted-windows-vhd"></a>准备已预先加密的 Windows VHD
以下部分介绍了准备预先加密的 Windows VHD 以在 Azure IaaS 中部署为加密的 VHD 的必要操作。 这些步骤用于在 Hyper-V 或 Azure 上准备和启动全新的 Windows VM (VHD)。

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>更新组策略以允许使用非 TPM 保护 OS
你需要在“本地计算机策略”\“计算机设置”\“管理模板”\“Windows 组件”下配置名为“BitLocker 驱动器加密”的 BitLocker 组策略设置。 如下图所示，将此设置更改为“*操作系统驱动器 - 启动时需要附加身份验证 - 没有兼容的 TPM 时允许 BitLocker*”：

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>安装 BitLocker 功能组件
对于 Windows Server 2012 和更高版本，请使用以下命令：

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

对于 Windows Server 2008 R2，请使用以下命令：

    ServerManagerCmd -install BitLockers

#### <a name="prepare-os-volume-for-bitlocker-using-bdehdcfg"></a>使用 `bdehdcfg` 为 BitLocker 准备 OS 卷
执行以下命令以压缩 OS 分区并为 BitLocker 准备计算机。

    bdehdcfg -target c: shrink -quiet

#### <a name="using-bitlocker-to-protect-the-os-volume"></a>使用 BitLocker 保护 OS 卷
使用 [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) 命令以通过外部密钥保护程序在引导卷上启用加密，并将外部密钥（.bek 文件）放置在外部驱动器或卷上。 下次重新启动后，将会在系统/引导卷上启用加密。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**注意：**必须先使用独立的数据/资源 VHD 准备 VM，才能使用 BitLocker 获取外部密钥。

#### <a name="encrypting-os-drive-on-a-running-linux-vm"></a>在正在运行的 Linux VM 上加密 OS 驱动器
以下发行版支持在正在运行的 Linux 虚拟机上加密 OS 驱动器：

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

OS 磁盘加密的先决条件：

* 必须从 Azure 资源管理器门户中的 Azure 库映像创建 VM。
* Azure VM，至少具有 4 GB RAM（建议大小为 7 GB）。
* （对于 RHEL 和 CentOS）必须在 VM 上[禁用](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) SELinux。 禁用 SELinux 之后，必须至少重新启动一次 VM。

##### <a name="steps"></a>步骤
1. 使用上面指定的发行版之一创建 VM。

对于 CentOS 7.2，通过专门的映像支持 OS 磁盘加密。 若要使用此映像，请在创建 VM 时将“7.2n”指定为 Sku：

    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"

2. 根据你的需要配置 VM。 如果你打算加密所有（OS + 数据）驱动器，则需要指定数据驱动器且可从 /etc/fstab 处装载数据驱动器。

> [!NOTE]
> 必须使用 UUID =... 在 /etc/fstab 中指定（而不是指定 /dev/sdb1 等块设备名称）数据驱动器。 在加密过程中，驱动器的顺序将在 VM 上有所改变。 如果你的 VM 依赖于特定块设备顺序，加密后将无法装载。
> 
> 

3. 注销 SSH 会话。

4. 若要加密 OS，请在[启用加密](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure)时将 volumeType 指定为“All”或“OS”。

> [!NOTE]
> 未作为 `systemd` 服务运行的所有用户空间进程将使用 `SIGKILL` 终止。 VM 将重新启动。 当在正在运行的 VM 上启用 OS 磁盘加密时，请计划 VM 的停机时间。
> 
> 

5. 使用[下一部分](#monitoring-os-encryption-progress)中的说明，定期监视加密进度。

6. 一旦 Get-AzureRmVmDiskEncryptionStatus 显示“VMRestartPending”，则通过登录 VM 或通过 Portal/PowerShell/CLI 来重新启动 VM。

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, please reboot the VM

建议在重新启动*之前*保存 VM 的[启动诊断](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/)。

#### <a name="monitoring-os-encryption-progress"></a>监视 OS 加密进度
有三种方法来监视 OS 加密进度。

1. 使用 Get-AzureRmVmDiskEncryptionStatus cmdlet 并检查 ProgressMessage 字段： 

    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started

一旦 VM 达到“OS disk encryption started”（OS 磁盘加密开始），在支持高级存储的 VM 上将需要花费大约 40-50 分钟。

由于 WALinuxAgent 出现[问题 #388](https://github.com/Azure/WALinuxAgent/issues/388)，`OsVolumeEncrypted` 和 `DataVolumesEncrypted` 在某些发行版中显示为 `Unknown`。 在 WALinuxAgent 2.1.5 版及更高版本中，将自动修复此问题。 如果在输出中看到 `Unknown`，可以通过使用 Azure Resource Viewer 来验证磁盘加密状态。

转到 [Azure Resource Viewer](https://resources.azure.com/)，然后在左侧的选择面板中展开此层次结构：

~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

在 InstanceView 中，向下滚动以查看驱动器的加密状态。

![VM 实例视图](./media/azure-security-disk-encryption/vm-instanceview.png)

2. 查看[启动诊断](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/)。 来自 ADE 扩展的消息应带有前缀 `[AzureDiskEncryption]`。

3. 通过 SSH 登录 VM 并获取扩展日志

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

建议不要在 OS 加密过程中登录 VM。 因此，只有当其他两个方法都失败时，才能复制日志。

#### <a name="preparing-a-pre-encrypted-linux-vhd"></a>准备已预先加密的 Linux VHD
##### <a name="ubuntu-16"></a>Ubuntu 16
###### <a name="configure-encryption-during-distro-install"></a>在发行版安装期间配置加密
1. 当对磁盘进行分区时选择“配置加密的卷”。

![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. 创建一个单独的不得加密的启动驱动器。 对根驱动器进行加密。

![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. 提供通行短语。 这是将上载到 KeyVault 的通行短语。

![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. 完成分区。

![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. 在启动 VM 时，将要求你提供通行短语。 使用步骤 3 中提供的通行短语。

![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. 使用[这些说明](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)准备 VM 以上载到 Azure。 不要运行最后一步（解除配置 VM）。

###### <a name="configure-encryption-to-work-with-azure"></a>配置加密来与 Azure 一起工作
1. 在 /usr/local/sbin/azure_crypt_key.sh 下创建一个包含以下脚本内容的文件。 请注意 KeyFileName，因为它是 Azure 放置的通行短语文件名。

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
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


2. 在 */etc/crypttab* 中更改加密配置。 它看起来应该如下所示：

    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3. 如果要在 Windows 中编辑 *azure_crypt_key.sh* 并将它复制到 Linux，请记得运行 *dos2unix /usr/local/sbin/azure_crypt_key.sh*。

4. 将可执行文件的权限添加到脚本：

    chmod +x /usr/local/sbin/azure_crypt_key.sh

4. 通过追加行编辑 */etc/initramfs-tools/modules*：

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5. 运行 `update-initramfs -u -k all` 更新 initramfs 以使 `keyscript` 生效。
6. 现在可以解除配置 VM。

![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

7. 继续下一步，[上载 VHD](#upload-encrypted-vhd-to-an-azure-storage-account) 到 Azure。

##### <a name="opensuse-132"></a>openSUSE 13.2
###### <a name="configure-encryption-during-distro-install"></a>在发行版安装期间配置加密
1. 当对磁盘进行分区时选择“加密卷组”。 提供通行短语。 这是将上载到 KeyVault 的通行短语。

![openSUSE 13.2 安装](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. 使用你的通行短语启动 VM。

![openSUSE 13.2 安装](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. 使用[这些说明](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)准备 VM 以上载到 Azure。 不要运行最后一步（解除配置 VM）。

###### <a name="configure-encryption-to-work-with-azure"></a>配置加密来与 Azure 一起工作
1. 编辑 /etc/dracut.conf 并添加以下行：

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2. 注释掉文件“/usr/lib/dracut/modules.d/90crypt/module-setup.sh”末尾的这些代码行：

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3. 在文件“/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh”的开头附加以下行

    DRACUT_SYSTEMD=0

并更改所有匹配项

    if [ -z "$DRACUT_SYSTEMD" ]; then

to

    if [ 1 ]; then

4. 编辑 /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh 并将此代码附加在“# Open LUKS device”的后面

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5. 运行“/usr/sbin/dracut -f -v”以更新 initrd。

6. 现在解除配置 VM，[上载 VHD](#upload-encrypted-vhd-to-an-azure-storage-account) 到 Azure。

##### <a name="centos-7"></a>CentOS 7
###### <a name="configure-encryption-during-distro-install"></a>在发行版安装期间配置加密
1. 当对磁盘进行分区时选择“加密我的数据”。

![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. 确保为根分区选择了“加密”。

![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. 提供通行短语。 这是将上载到 KeyVault 的通行短语。

![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. 在启动 VM 时，将要求你提供通行短语。 使用步骤 3 中提供的通行短语。

![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. 使用[这些说明](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)准备 VM 以上载到 Azure。 不要运行最后一步（解除配置 VM）。

6. 现在解除配置 VM，[上载 VHD](#upload-encrypted-vhd-to-an-azure-storage-account) 到 Azure。

###### <a name="configure-encryption-to-work-with-azure"></a>配置加密来与 Azure 一起工作
1. 编辑 /etc/dracut.conf 并添加以下行：

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2. 注释掉文件“/usr/lib/dracut/modules.d/90crypt/module-setup.sh”末尾的这些代码行：

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3. 在文件“/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh”的开头附加以下行

    DRACUT_SYSTEMD=0

并更改所有匹配项

    if [ -z "$DRACUT_SYSTEMD" ]; then

to

    if [ 1 ]; then

4. 编辑 /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh 并将此代码附加在“# Open LUKS device”的后面

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5. 运行“/usr/sbin/dracut -f -v”以更新 initrd。

![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>将加密的 VHD 上载到 Azure 存储帐户
启用 BitLocker 加密或 DM-Crypt 加密后，需要将本地加密的 VHD 上载到存储帐户。

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-disk-encryption-secret-for-the-pre-encrypted-vm-to-key-vault"></a>将预先加密的 VM 的磁盘加密机密上载到密钥保管库
需要将前面获取的磁盘加密机密作为机密上载到密钥保管库中。 密钥保管库需要具有对你的 AAD 客户端和磁盘加密启用的权限。

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $KeyVault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>未使用 KEK 加密的磁盘加密机密
使用 [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) 在密钥保管库中预配机密。 对于 Windows 虚拟机，需将 bek 文件编码为 base64 字符串，然后使用 Set-AzureKeyVaultSecret cmdlet 将其上载到密钥保管库。 对于 Linux，需将通行短语编码为 base64 字符串，然后将其上载到密钥保管库。 此外，请确保在密钥保管库中创建机密时设置以下标记。

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

将在下一步中使用 `$secretUrl` 以便[在不使用 KEK 的情况下附加 OS 磁盘](#without-using-a-kek)。

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>使用 KEK 加密的磁盘加密机密
可以选择使用密钥加密密钥来加密机密，然后将其上载到密钥保管库。 先使用包装 [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) 加密使用密钥加密密钥的机密。 此包装操作的输出是 base64 URL 编码的字符串。然后使用 [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) cmdlet 将它上载为机密。

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

将在下一步中使用 `$KeyEncryptionKey` 和 `$secretUrl` 以便[在使用 KEK 的情况下附加 OS 磁盘](#using-a-kek)。

### <a name="specify-secret-url-when-attaching-os-disk"></a>当附加 OS 磁盘时指定机密 URL
#### <a name="without-using-a-kek"></a>不使用 KEK
当附加 OS 磁盘时，需要传递 `$secretUrl`。 该 URL 是在[“不使用 KEK 对磁盘加密机密进行加密”](#disk-encryption-secret-not-encrypted-with-a-kek)部分中生成的。

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>使用 KEK
当附加 OS 磁盘时，需要传递 `$KeyEncryptionKey` 和 `$secretUrl`。 该 URL 是在[“使用 KEK 对磁盘加密机密进行加密”](#disk-encryption-secret-encrypted-with-a-kek)部分中生成的。

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>下载此指南
可以从 [TechNet 库](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)下载此指南。

## <a name="for-more-information"></a>更多信息
[探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[探讨如何使用 Azure PowerShell 启用 Azure 磁盘加密 - 第 2 部分](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)




<!--HONumber=Nov16_HO4-->


