---
title: 使用 Azure AD 应用进行 Azure 磁盘加密的先决条件（早期版本）
description: 本文提供了对 IaaS VM 使用 Microsoft Azure 磁盘加密所要满足的先决条件。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 07db6c5cecf2b8dec803e73d5775edef3f8906fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828627"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Azure 磁盘加密先决条件（早期版本）

**新版本的 Azure 磁盘加密无需提供 Azure AD 应用程序参数即可启用 VM 磁盘加密。使用新版本，在执行启用加密步骤时，不再需要提供 Azure AD 凭据。所有新 VM 都必须使用新版本在没有 Azure AD 应用程序参数的情况下进行加密。若要查看使用新版本启用 VM 磁盘加密的说明，请参阅 [Azure 磁盘加密先决条件](azure-security-disk-encryption-prerequisites.md)。已使用 Azure AD 应用程序参数加密的 VM 仍受支持，应继续使用 AAD 语法进行维护。**

 本文“Azure 磁盘加密先决条件”介绍了在使用 Azure 磁盘加密之前所要满足的条件。 除满足常规先决条件外，Azure 磁盘加密还与 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) 集成，并使用 Azure AD 应用程序提供身份验证，以管理 Key Vault 中的加密密钥。 还可以使用 [Azure PowerShell](/powershell/azure/overview) 或 [Azure CLI](/cli/azure/) 来设置或配置 Key Vault 与 Azure AD 应用程序。

针对 [Azure 磁盘加密概述](azure-security-disk-encryption-overview.md)一文中所述的受支持方案在 Azure IaaS VM 上启用 Azure 磁盘加密之前，请务必满足以下先决条件。 

> [!WARNING]
> - 某些建议可能会导致数据、网络或计算资源使用量增加，从而产生额外许可或订阅成本。 必须具有有效的活动 Azure 订阅，才能在 Azure 的受支持区域中创建资源。
> - 如果你以前采用[使用 Azure AD 应用进行 Azure 磁盘加密](azure-security-disk-encryption-prerequisites-aad.md)来加密此 VM，则必须继续使用此选项来加密 VM。 无法在此加密的 VM 上使用 [Azure 磁盘加密](azure-security-disk-encryption-prerequisites.md)，因为不支持此方案，这意味着尚不支持为此加密的 VM 实施 AAD 应用程序切换操作。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

### <a name="windows"></a>Windows

- Windows 客户端：Windows 8 和更高版本。
- Windows Server：Windows Server 2008 R2 和更高版本。  
 
> [!NOTE] 
> Windows Server 2008 R2 要求安装 .NET Framework 4.5 以支持加密；请从 Windows 更新安装此组件，并安装适用于 Windows Server 2008 R2 基于 x64 的系统的 Microsoft .NET Framework 4.5.2 可选更新 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983))。  
>  
> Windows Server 2012 R2 Core 和 Windows Server 2016 Core 要求在 VM 安装 bdehdcfg 组件以支持加密。

### <a name="linux"></a>Linux 

有一部分 [Azure 认可的 Linux 分发版](../virtual-machines/linux/endorsed-distros.md)（其本身是所有可能的 Linux 服务器分发版的一部分）支持 Azure 磁盘加密。

![支持 Azure 磁盘加密的 Linux 服务器分发版文氏图](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

未经 Azure 认可的 Linux 服务器分发版不支持 Azure 磁盘加密，而在已认可的分发版中，只有以下分发版和版本支持 Azure 磁盘加密：

| Linux 分发版 | Version | 支持加密的卷类型|
| --- | --- |--- |
| Ubuntu | 18.04| OS 和数据磁盘 |
| Ubuntu | 16.04| OS 和数据磁盘 |
| Ubuntu | 14.04.5</br>[其 Azure 优化内核更新到 4.15 或更高版本](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | OS 和数据磁盘 |
| RHEL | 7.7 | OS 和数据磁盘（参阅下面的注释） |
| RHEL | 7.6 | OS 和数据磁盘（参阅下面的注释） |
| RHEL | 7.5 | OS 和数据磁盘（参阅下面的注释） |
| RHEL | 7.4 | OS 和数据磁盘（参阅下面的注释） |
| RHEL | 7.3 | OS 和数据磁盘（参阅下面的注释） |
| RHEL | 7.2 | OS 和数据磁盘（参阅下面的注释） |
| RHEL | 6.8 | 数据磁盘（参阅下面的注释） |
| RHEL | 6.7 | 数据磁盘（参阅下面的注释） |
| CentOS | 7.7 | OS 和数据磁盘 |
| CentOS | 7.6 | OS 和数据磁盘 |
| CentOS | 7.5 | OS 和数据磁盘 |
| CentOS | 7.4 | OS 和数据磁盘 |
| CentOS | 7.3 | OS 和数据磁盘 |
| CentOS | 7.2n | OS 和数据磁盘 |
| CentOS | 6.8 | 数据磁盘 |
| openSUSE | 42.3 | 数据磁盘 |
| SLES | 12-SP4 | 数据磁盘 |
| SLES | 12-SP3 | 数据磁盘 |

> [!NOTE]
> 新的 ADE 实现支持 RHEL OS 和 RHEL7 即用即付映像的数据磁盘。 ADE 目前不支持 RHEL 自带订阅 (BYOS) 映像。 有关详细信息，请参阅适用于[Linux 的 Azure 磁盘加密](azure-security-disk-encryption-linux.md)。

- Azure 磁盘加密要求 Key Vault 和 VM 位于同一 Azure 区域和订阅。 在不同区域中配置资源会导致启用 Azure 磁盘加密功能失败。

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>适用于 Linux IaaS VM 的其他先决条件 

- Azure 磁盘加密要求系统上存在 dm-dm-crypt 和 vfat 模块。 在默认映像中删除或禁用 vfat 会阻止系统读取密钥卷，以及在后续重新启动时获取用于解锁磁盘的密钥。 从系统中删除 vfat 模块的系统强化步骤与 Azure 磁盘加密不兼容。 
- 在启用加密之前，要加密的数据磁盘需在 /etc/fstab 中正确列出。 为此条目使用永久性块设备名，因为每次重新启动后，不能依赖于使用“/dev/sdX”格式的设备名来与同一磁盘相关联，尤其是应用加密后。 有关此行为的更多详细信息，请参阅：[排查 Linux VM 设备名称更改问题](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- 确保正确配置用于装载的 /etc/fstab 设置。 若要配置这些设置，请运行 mount -a 命令，或重新启动 VM 并以这种方法触发重新装载。 装载完成后，检查 lsblk 命令的输出，以验证驱动器是否仍已装载。 
  - 如果在启用加密之前 /etc/fstab 文件未正确装载该驱动器，则 Azure 磁盘加密无法将其正确装载。
  - 在加密过程中，Azure 磁盘加密进程会将装载信息移出 /etc/fstab，并移入其自身的配置文件中。 数据驱动器加密完成后，如果看到 /etc/fstab 中缺少条目，请不要担心。
  - 在开始加密之前，请务必停止可能正在向装载的数据磁盘写入数据的所有服务和进程并将其禁用，使其不会在重新引导后自动重启。 这可能会使文件在这些分区上保持打开状态，从而阻止加密过程重新装载这些分区，导致加密失败。 
  - 重新启动后，Azure 磁盘加密进程需要花费一段时间来装载新加密的磁盘。 重新启动后，这些磁盘并不是立即可用。 该进程需要一段时间来启动、解锁然后装载加密的驱动器，然后，这些驱动器才可供其他进程访问。 重新启动后，此进程可能需要一分钟以上的时间，具体时间取决于系统特征。

在[此脚本文件的第 244-248 行](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248)可以找到用于装载数据磁盘和创建所需 /etc/fstab 条目的命令示例。 

## <a name="bkmk_GPO"></a>网络和组策略

**若要启用使用旧 AAD 参数语法的 Azure 磁盘加密功能，IaaS VM 必须符合以下网络终结点配置要求：** 
  - IaaS VM 必须能够连接到 Azure Active Directory 终结点 \[login.microsoftonline.com\]，以获取用于连接 Key Vault 的令牌。
  - IaaS VM 必须能够连接到 Key Vault 终结点，以将加密密钥写入 Key Vault。
  - IaaS VM 必须能够连接到托管 Azure 扩展存储库的 Azure 存储终结点和托管 VHD 文件的 Azure 存储帐户。
  -  如果安全策略限制从 Azure VM 到 Internet 的访问，可以解析上述 URI，并配置特定的规则以允许与这些 IP 建立出站连接。 有关详细信息，请参阅[防火墙后的 Azure Key Vault](../key-vault/key-vault-access-behind-firewall.md)。
  - 在 Windows 中，如果显式禁用了 TLS 1.0 且 .NET 版本尚未更新到 4.6 或更高版本，下面的注册表更改将启用 ADE 以选择较新的 TLS 版本：
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**组策略：**
 - Azure 磁盘加密解决方案对 Windows IaaS VM 使用 BitLocker 外部密钥保护程序。 对于已加入域的 VM，请不要推送会强制执行 TPM 保护程序的任何组策略。 有关“在没有兼容 TPM 的情况下允许 BitLocker”的组策略信息，请参阅 [BitLocker 组策略参考](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

-  具有自定义组策略的已加入域虚拟机上的 BitLocker 策略必须包含以下设置：[配置 BitLocker 恢复信息的用户存储 -> 允许 256 位恢复密钥](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 如果 BitLocker 的自定义组策略设置不兼容，Azure 磁盘加密将会失败。 在没有正确策略设置的计算机上，应用新策略，强制更新新策略 (gpupdate.exe /force)，然后可能需要重启。  


## <a name="bkmk_PSH"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) 提供了一组使用 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)模型管理 Azure 资源的 cmdlet。 可以在浏览器中结合 [Azure Cloud Shell](../cloud-shell/overview.md) 使用 PowerShell，或者遵照以下说明将 PowerShell 安装在本地计算机上，以便在任何 PowerShell 会话中使用这些 cmdlet。 如果已在本地安装它，请确保使用最新版本的 Azure PowerShell 来配置 Azure 磁盘加密。

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>安装在本地计算机上使用的 Azure PowerShell（可选）：  
1. [安装和配置 Azure PowerShell](/powershell/azure/install-az-ps)。 

2. 安装 [Azure Active Directory PowerShell 模块](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module)。 

     ```powershell
     Install-Module AzureAD
     ```

3. 验证安装的模块版本。
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 登录到 Azure。
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 连接到 Azure AD。
     
     ```powershell
     Connect-AzureAD
     ```

6. 根据需要查看 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)和 [AzureAD](/powershell/module/azuread)。

## <a name="bkmk_CLI"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) 是用于管理 Azure 资源的命令行工具。 CLI 旨在提高数据查询灵活性、支持非阻塞进程形式的长时间操作，以及简化脚本编写。 可以在浏览器中结合 [Azure Cloud Shell](../cloud-shell/overview.md) 使用这些 cmdlet，或者将它们安装在本地计算机上并在任何 PowerShell 会话中使用。

1. 安装在本地计算机上使用的 [Azure CLI](/cli/azure/install-azure-cli)（可选）：

2. 验证安装的版本。
     
     ```azurecli-interactive
     az --version
     ``` 

3. 使用 [az login](/cli/azure/authenticate-azure-cli) 登录到 Azure。
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. 根据需要查看 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli)。 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Key Vault 和 Azure AD 应用的先决条件工作流

如果你已熟悉进行 Azure 磁盘加密时的 Key Vault 和 Azure AD 先决条件，则可以使用 [Azure 磁盘加密先决条件 PowerShell 脚本](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )。 有关使用先决条件脚本的详细信息，请参阅[Azure 磁盘加密附录](azure-security-disk-encryption-appendix.md#bkmk_prereq-script)。 

1. 创建密钥保管库。 
2. 设置 Azure AD 应用程序和服务主体。
3. 为 Azure AD 应用设置 Key Vault 访问策略。
4. 设置 Key Vault 高级访问策略。
 
## <a name="bkmk_KeyVault"></a>创建密钥保管库 
Azure 磁盘加密与 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 集成，帮助你控制和管理 Key Vault 订阅中的磁盘加密密钥与机密。 可为 Azure 磁盘加密创建 Key Vault，或使用现有的 Key Vault。 有关 Key Vault 的详细信息，请参阅 [Azure Key Vault 入门](../key-vault/key-vault-get-started.md)和[保护 Key Vault](../key-vault/key-vault-secure-your-key-vault.md)。 可以使用资源管理器模板、Azure PowerShell 或 Azure CLI 创建 Key Vault。 


>[!WARNING]
>为确保加密机密不会跨过区域边界，Azure 磁盘加密需要将 Key Vault 和 VM 共置在同一区域。 在要加密的 VM 所在的同一区域中创建并使用 Key Vault。 


### <a name="bkmk_KVPSH"></a>使用 PowerShell 创建 Key Vault

可以在 Azure PowerShell 中使用 [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet 创建 Key Vault。 有关适用于 Key Vault 的更多 cmdlet，请参阅 [Az.KeyVault](/powershell/module/az.keyvault/)。 

1. 根据需要[连接到 Azure 订阅](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。 
2. 根据需要，使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 创建新资源组。  若要列出数据中心位置，请使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation)。 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. 使用 [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) 创建新的 Key Vault
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. 记下返回的“保管库名称”、“资源组名称”、“资源 ID”、“保管库 URI”和“对象 ID”，以便稍后在加密磁盘时使用。 


### <a name="bkmk_KVCLI"></a>使用 Azure CLI 创建 Key Vault
可以在 Azure CLI 中使用 [az keyvault](/cli/azure/keyvault#commands) 命令管理 Key Vault。 若要创建 Key Vault，请使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)。

1. 根据需要[连接到 Azure 订阅](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 根据需要，使用 [az group create](/cli/azure/group#az-group-create) 创建新资源组。 若要列出位置，请使用 [az account list-locations](/cli/azure/account#az-account-list) 
     
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
需要在 Azure 中正在运行的 VM 上启用加密时，Azure 磁盘加密将生成加密密钥并将其写入 Key Vault。 在 Key Vault 中管理加密密钥需要 Azure AD 身份验证。 为此，请创建 Azure AD 应用程序。 对于身份验证，可以使用基于客户端机密的身份验证或[基于客户端证书的 Azure AD 身份验证](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)。


### <a name="bkmk_ADappPSH"></a>使用 Azure PowerShell 设置 Azure AD 应用和服务主体 
若要执行以下命令，请获取并使用 [Azure AD PowerShell 模块](/powershell/azure/active-directory/install-adv2)。 

1. 根据需要[连接到 Azure 订阅](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。
2. 使用[AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell cmdlet 创建 Azure AD 应用程序。 MyApplicationHomePage 和 MyApplicationUri 可以是所需的任意值。

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId 是 Azure AD ClientID，$aadClientSecret 是稍后启用 Azure 磁盘加密时要使用的客户端机密。 请妥善保存 Azure AD 客户端机密。 运行 `$azureAdApplication.ApplicationId` 会显示 ApplicationID。


### <a name="bkmk_ADappCLI"></a>使用 Azure CLI 设置 Azure AD 应用和服务主体

可以在 Azure CLI 中使用 [az ad sp](/cli/azure/ad/sp) 命令来管理服务主体。 有关详细信息，请参阅[创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。

1. 根据需要[连接到 Azure 订阅](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 创建新服务主体。
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  返回的 appId 是其他命令中使用的 Azure AD ClientID。 它也是要在 az keyvault set-policy 中使用的 SPN。 password 是稍后启用 Azure 磁盘加密时要使用的客户端机密。 请妥善保存 Azure AD 客户端机密。
 
### <a name="bkmk_ADappRM"></a>通过 Azure 门户设置 Azure AD 应用和服务主体
使用[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)一文中的步骤创建 Azure AD 应用程序。 下面列出的每个步骤直接链接到要完成的文章部分。 

1. [验证所需的权限](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - 创建应用程序时，可以使用任意所需的名称和登录 URL。
3. [获取应用程序 ID 和身份验证密钥](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)。 
     - 身份验证密钥是客户端机密，用作 AzVMDiskEncryptionExtension 的 AadClientSecret。 
        - 应用程序使用身份验证密钥作为凭据登录到 Azure AD。 在 Azure 门户中，此机密称为密钥，但与 Key Vault 没有任何关系。 请适当地保护此机密。 
     - 应用程序 ID 稍后将用作 AzVMDiskEncryptionExtension 的 AadClientId 和 AzKeyVaultAccessPolicy 的 ServicePrincipalName。 

## <a name="bkmk_KVAP"></a>为 Azure AD 应用设置 Key Vault 访问策略
若要将加密机密写入指定的 Key Vault，Azure 磁盘加密需要 Azure Active Directory 应用程序的客户端 ID，以及有权将机密写入 Key Vault 的客户端机密。 

> [!NOTE]
> Azure 磁盘加密要求为 Azure AD 客户端应用程序配置以下访问策略：_WrapKey_ 和 _Set_ 权限。

### <a name="bkmk_KVAPPSH"></a>使用 Azure PowerShell 为 Azure AD 应用设置 Key Vault 访问策略
Azure AD 应用程序需有访问保管库中密钥或机密的权限。 使用[AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet，使用客户端 ID （在注册应用程序时生成）作为 _– ServicePrincipalName_参数值来授予对应用程序的权限。 若要了解详细信息，请参阅博客文章 [Azure Key Vault - Step by Step](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)（Azure Key Vault - 分步指南）。 

1. 根据需要[连接到 Azure 订阅](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。
2. 使用 PowerShell 为 AD 应用程序设置 Key Vault 访问策略。

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a>使用 Azure CLI 为 Azure AD 应用设置 Key Vault 访问策略
使用 [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) 设置访问策略。 有关详细信息，请参阅[使用 CLI 2.0 管理 Key Vault](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)。

1. 根据需要[连接到 Azure 订阅](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 使用以下命令，为通过 Azure CLI 创建的服务主体授予获取机密和包装密钥的访问权限：
 
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

![Azure Key Vault 加密操作 - 包装密钥](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault 机密权限 - 设置](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

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

![Azure Key Vault 高级访问策略](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a>设置密钥加密密钥（可选）
若要使用密钥加密密钥 (KEK) 来为加密密钥提供附加的安全层，请将 KEK 添加到 Key Vault。 使用 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 在 Key Vault 中创建密钥加密密钥。 还可从本地密钥管理 HSM 导入 KEK。 有关详细信息，请参阅 [Key Vault 文档](../key-vault/key-vault-hsm-protected-keys.md)。 指定密钥加密密钥后，Azure 磁盘加密会使用该密钥包装加密机密，然后将机密写入 Key Vault。 

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
> [!div class="nextstepaction"]
> [启用适用于 Windows 的 Azure 磁盘加密](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [启用适用于 Linux 的 Azure 磁盘加密](azure-security-disk-encryption-linux-aad.md)
