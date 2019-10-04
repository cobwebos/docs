---
title: 使用 Azure AD 为 Azure 磁盘加密创建和配置密钥保管库（以前的版本）
description: 本文提供了对 IaaS VM 使用 Microsoft Azure 磁盘加密所要满足的先决条件。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: be709fcbb45c95f092b24b53fd0c506187fcd8b3
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828545"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>使用 Azure AD 为 Azure 磁盘加密创建和配置密钥保管库（以前的版本）

**新版本的 Azure 磁盘加密无需提供 Azure AD 应用程序参数即可启用 VM 磁盘加密。使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。所有新 VM 都必须使用新版本在没有 Azure AD 应用程序参数的情况下进行加密。若要查看使用新版本启用 VM 磁盘加密的说明，请参阅[Azure 磁盘加密](disk-encryption-overview.md)。已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。**

Azure 磁盘加密使用 Azure Key Vault 来控制和管理磁盘加密密钥和机密。  有关 Key Vault 的详细信息，请参阅 [Azure Key Vault 入门](../../key-vault/key-vault-get-started.md)和[保护 Key Vault](../../key-vault/key-vault-secure-your-key-vault.md)。 

创建和配置密钥保管库以用于与 Azure AD （以前的版本）的 Azure 磁盘加密涉及三个步骤：

1. 创建密钥保管库。 
2. 设置 Azure AD 应用程序和服务主体。
3. 为 Azure AD 应用设置 Key Vault 访问策略。
4. 设置 Key Vault 高级访问策略。
 
你还可以根据需要生成或导入密钥加密密钥（KEK）。

有关如何[安装工具并连接到 azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)的步骤，请参阅[创建和配置 Azure 磁盘加密的密钥保管库一](disk-encryption-key-vault.md)文。

> [!Note]
> 本文中的步骤是在[Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)和[azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中自动完成的。


## <a name="create-a-key-vault"></a>创建 key vault 
Azure 磁盘加密与 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 集成，帮助你控制和管理 Key Vault 订阅中的磁盘加密密钥与机密。 可为 Azure 磁盘加密创建 Key Vault，或使用现有的 Key Vault。 有关 Key Vault 的详细信息，请参阅 [Azure Key Vault 入门](../../key-vault/key-vault-get-started.md)和[保护 Key Vault](../../key-vault/key-vault-secure-your-key-vault.md)。 可以使用资源管理器模板、Azure PowerShell 或 Azure CLI 创建 Key Vault。 


>[!WARNING]
>为确保加密机密不会跨过区域边界，Azure 磁盘加密需要将 Key Vault 和 VM 共置在同一区域。 在要加密的 VM 所在的同一区域中创建并使用 Key Vault。 


### <a name="bkmk_KVPSH"></a>使用 PowerShell 创建 Key Vault

可以在 Azure PowerShell 中使用 [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet 创建 Key Vault。 有关适用于 Key Vault 的更多 cmdlet，请参阅 [Az.KeyVault](/powershell/module/az.keyvault/)。 

1. 根据需要，使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 创建新资源组。  若要列出数据中心位置，请使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation)。 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. 使用 [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) 创建新的 Key Vault
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. 记下返回的“保管库名称”、“资源组名称”、“资源 ID”、“保管库 URI”和“对象 ID”，以便稍后在加密磁盘时使用。 


### <a name="bkmk_KVCLI"></a>使用 Azure CLI 创建 Key Vault
可以在 Azure CLI 中使用 [az keyvault](/cli/azure/keyvault#commands) 命令管理 Key Vault。 若要创建 Key Vault，请使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)。

1. 根据需要，使用 [az group create](/cli/azure/group#az-group-create) 创建新资源组。 若要列出位置，请使用 [az account list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. 使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create) 创建新 Key Vault。
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. 记下返回的“保管库名称”(name)、“资源组名称”、“资源 ID”(ID)、“保管库 URI”和“对象 ID”，以便稍后使用。 

### <a name="bkmk_KVRM"></a>使用资源管理器模板创建 Key Vault

可以使用[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)创建 Key Vault。

1. 在 Azure 快速入门模板中，单击“部署到 Azure”。
2. 选择订阅、资源组、资源组位置、Key Vault 名称、对象 ID、法律条款和协议，然后单击“购买”。 


## <a name="bkmk_ADapp"></a>设置 Azure AD 应用和服务主体 
需要在 Azure 中正在运行的 VM 上启用加密时，Azure 磁盘加密将生成加密密钥并将其写入 Key Vault。 在 Key Vault 中管理加密密钥需要 Azure AD 身份验证。 为此，请创建 Azure AD 应用程序。 对于身份验证，可以使用基于客户端机密的身份验证或[基于客户端证书的 Azure AD 身份验证](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)。


### <a name="bkmk_ADappPSH"></a>使用 Azure PowerShell 设置 Azure AD 应用和服务主体 
若要执行以下命令，请获取并使用 [Azure AD PowerShell 模块](/powershell/azure/active-directory/install-adv2)。 

1. 使用[AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell cmdlet 创建 Azure AD 应用程序。 MyApplicationHomePage 和 MyApplicationUri 可以是所需的任意值。

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId 是 Azure AD ClientID，$aadClientSecret 是稍后启用 Azure 磁盘加密时要使用的客户端机密。 请妥善保存 Azure AD 客户端机密。 运行 `$azureAdApplication.ApplicationId` 会显示 ApplicationID。


### <a name="bkmk_ADappCLI"></a>使用 Azure CLI 设置 Azure AD 应用和服务主体

可以在 Azure CLI 中使用 [az ad sp](/cli/azure/ad/sp) 命令来管理服务主体。 有关详细信息，请参阅[创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。

1. 创建新服务主体。
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  返回的 appId 是其他命令中使用的 Azure AD ClientID。 它也是要在 az keyvault set-policy 中使用的 SPN。 password 是稍后启用 Azure 磁盘加密时要使用的客户端机密。 请妥善保存 Azure AD 客户端机密。
 
### <a name="bkmk_ADappRM"></a>通过 Azure 门户设置 Azure AD 应用和服务主体
使用[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)一文中的步骤创建 Azure AD 应用程序。 下面列出的每个步骤直接链接到要完成的文章部分。 

1. [验证所需的权限](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [创建 Azure Active Directory 应用程序](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - 创建应用程序时，可以使用任意所需的名称和登录 URL。
3. [获取应用程序 ID 和身份验证密钥](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)。 
     - 身份验证密钥是客户端机密，用作 AzVMDiskEncryptionExtension 的 AadClientSecret。 
        - 应用程序使用身份验证密钥作为凭据登录到 Azure AD。 在 Azure 门户中，此机密称为密钥，但与 Key Vault 没有任何关系。 请适当地保护此机密。 
     - 应用程序 ID 稍后将用作 AzVMDiskEncryptionExtension 的 AadClientId 和 AzKeyVaultAccessPolicy 的 ServicePrincipalName。 

## <a name="bkmk_KVAP"></a>为 Azure AD 应用设置 Key Vault 访问策略
若要将加密机密写入指定的 Key Vault，Azure 磁盘加密需要 Azure Active Directory 应用程序的客户端 ID，以及有权将机密写入 Key Vault 的客户端机密。 

> [!NOTE]
> Azure 磁盘加密要求为 Azure AD 客户端应用程序配置以下访问策略：_WrapKey_ 和 _Set_ 权限。

### <a name="bkmk_KVAPPSH"></a>使用 Azure PowerShell 为 Azure AD 应用设置 Key Vault 访问策略
Azure AD 应用程序需有访问保管库中密钥或机密的权限。 使用[AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet，使用客户端 ID （在注册应用程序时生成）作为 _– ServicePrincipalName_参数值来授予对应用程序的权限。 若要了解详细信息，请参阅博客文章 [Azure Key Vault - Step by Step](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)（Azure Key Vault - 分步指南）。 

1. 使用 PowerShell 为 AD 应用程序设置 Key Vault 访问策略。

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a>使用 Azure CLI 为 Azure AD 应用设置 Key Vault 访问策略
使用 [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) 设置访问策略。 有关详细信息，请参阅[使用 CLI 2.0 管理 Key Vault](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)。

使用以下命令，为通过 Azure CLI 创建的服务主体授予获取机密和包装密钥的访问权限：
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a>使用门户为 Azure AD 应用设置 Key Vault 访问策略

1. 打开包含 Key Vault 的资源组。
2. 选择 Key Vault，转到“访问策略”，然后单击“新增”。
3. 在“选择主体”下，搜索创建的 Azure AD 应用程序并选择它。 
4. 对于“密钥权限”，请选中“加密操作”下的“包装密钥”。
5. 对于“机密权限”，请选中“机密管理操作”下的“设置”。
6. 单击“确定”保存访问策略。 

![Azure Key Vault 加密操作 - 包装密钥](./media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault 机密权限 - 设置](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> 设置 Key Vault 高级访问策略
Azure 平台需要访问 Key Vault 中的加密密钥或机密，才能使这些密钥和机密可供 VM 用来启动和解密卷。 对 Key Vault 启用磁盘加密，否则部署将会失败。  

### <a name="bkmk_KVperPSH"></a>使用 Azure PowerShell 设置 Key Vault 高级访问策略
 使用 Key Vault PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 为 Key Vault 启用磁盘加密。

  - **为磁盘加密启用 Key Vault：** 若要启用 Azure 磁盘加密，需要使用 EnabledForDiskEncryption。
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **根据需要为部署启用 Key Vault：** 在资源创建操作中引用此 Key Vault（例如，创建虚拟机）时，使 Microsoft.Compute 资源提供程序能够从此 Key Vault 中检索机密。

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **根据需要为模板部署启用 Key Vault：** 在模板部署中引用此 Key Vault 时，使 Azure 资源管理器能够从此 Key Vault 中获取机密。

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a>使用 Azure CLI 设置 Key Vault 高级访问策略
使用 [az keyvault update](/cli/azure/keyvault#az-keyvault-update) 为 Key Vault 启用磁盘加密。 

 - **为磁盘加密启用 Key Vault：** 需要使用 Enabled-for-disk-encryption。 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **根据需要为部署启用 Key Vault：** 允许虚拟机从保管库中检索作为机密存储的证书。
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **根据需要为模板部署启用 Key Vault：** 允许资源管理器从保管库中检索机密。
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a>通过 Azure 门户设置 Key Vault 高级访问策略

1. 选择 Key Vault，转到“访问策略”，然后选择“单击此处可显示高级访问策略”。
2. 选中标有“启用对 Azure 磁盘加密的访问以进行卷加密”的框。
3. 根据需要选择“启用对 Azure 虚拟机的访问以进行部署”和/或“启用对 Azure 资源管理器的访问以进行模板部署”。 
4. 单击“保存”。

![Azure Key Vault 高级访问策略](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a>设置密钥加密密钥（可选）
若要使用密钥加密密钥 (KEK) 来为加密密钥提供附加的安全层，请将 KEK 添加到 Key Vault。 使用 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 在 Key Vault 中创建密钥加密密钥。 还可从本地密钥管理 HSM 导入 KEK。 有关详细信息，请参阅 [Key Vault 文档](../../key-vault/key-vault-hsm-protected-keys.md)。 指定密钥加密密钥后，Azure 磁盘加密会使用该密钥包装加密机密，然后将机密写入 Key Vault。 

* 生成密钥时，请使用 RSA 密钥类型。 Azure 磁盘加密暂不支持使用椭圆曲线密钥。

* Key Vault 机密和 KEK URL 必须已设置版本。 Azure 会强制实施这项版本控制限制。 有关有效的机密和 KEK URL，请参阅以下示例：

  * 有效机密 URL 的示例：   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 有效 KEK URL 的示例：   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure 磁盘加密不支持将端口号指定为 Key Vault 机密和 KEK URL 的一部分。 有关不支持和支持的 Key Vault URL 的示例，请参阅以下示例：

  * 无法接受的密钥保管库 URL：   *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 可接受的密钥保管库 URL：   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a>使用 Azure PowerShell 设置密钥加密密钥 
在使用 PowerShell 脚本之前，应熟悉 Azure 磁盘加密必备组件，以了解脚本中的步骤。 可能需要根据环境更改示例脚本。 此脚本创建所有 Azure 磁盘加密必备组件、加密现有 IaaS VM，并使用密钥加密密钥来包装磁盘加密密钥。 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a>基于证书的身份验证（可选）
若要使用证书身份验证，可将一个证书上传到 Key Vault，并将其部署到客户端。 在使用 PowerShell 脚本之前，应熟悉 Azure 磁盘加密必备组件，以了解脚本中的步骤。 可能需要根据环境更改示例脚本。

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a>基于证书的身份验证和 KEK（可选）

若要使用证书身份验证并通过 KEK 包装加密密钥，可使用以下脚本作为示例。 在使用 PowerShell 脚本之前，应熟悉前面所述的所有 Azure 磁盘加密必备组件，以了解脚本中的步骤。 可能需要根据环境更改示例脚本。

> [!IMPORTANT]
> Linux VM 当前不支持 Azure AD 基于证书的身份验证。



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>后续步骤

[使用 Linux Vm 上的 Azure AD 启用 Azure 磁盘加密（以前的版本）](disk-encryption-linux-aad.md)
