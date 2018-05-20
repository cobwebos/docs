---
title: Azure 密钥保管库入门 | Microsoft Docs
description: 本教程会帮助你开始使用 Azure 密钥保管库在 Azure 中创建强化容器，以存储和管理 Azure 中的加密密钥和机密。
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 58a283f1ce5bd2fd78c4fa2038c3998aea1598c9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="get-started-with-azure-key-vault"></a>Azure 密钥保管库入门
本文有助于使用 PowerShell 完成 Azure Key Vault 入门，并详细介绍如何完成以下活动：
- 如何在 Azure 中创建强化容器（保管库）。
- 如何使用 KeyVault 在 Azure 中存储和管理加密密钥和机密。
- 应用程序如何使用该密钥或密码。

在大多数区域中提供了 Azure 密钥保管库。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。

> [!NOTE]
> 本文不包括如何编写 Azure 应用程序的说明。 对于这些步骤，可以使用 [Azure Key Vault 示例应用程序](https://www.microsoft.com/download/details.aspx?id=45343)。

有关跨平台命令行接口说明，请参阅[此对应教程](key-vault-manage-with-cli2.md)。

## <a name="requirements"></a>要求
在继续阅读本文之前，请确认是否符合以下条件：

- **一个 Azure 订阅**。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure PowerShell**，**最低版本为 1.1.0**。 要安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 如果已安装了 Azure PowerShell，但不知道版本，请在 Azure PowerShell 控制台中键入 `(Get-Module azure -ListAvailable).Version`。 如果已安装 Azure PowerShell 版本 0.9.1 到 0.9.8，仍可以使用本教程，但需要进行一些细微更改。 例如，必须使用 `Switch-AzureMode AzureResourceManager` 命令，并且某些 Azure 密钥保管库命令已更改。 有关版本 0.9.1 到 0.9.8 的密钥保管库 cmdlet 的列表，请参阅 [Azure 密钥保管库 Cmdlet](/powershell/module/azurerm.keyvault/#key_vault)。
- **一个可以配置为使用 Key Vault 的应用程序**。 可以从 [Microsoft 下载中心](http://www.microsoft.com/download/details.aspx?id=45343)获取示例应用程序。 有关说明，请参阅随附的**自述**文件。

>[!NOTE]
本文假定读者对 PowerShell 和 Azure 有一个基本的了解。 有关 PowerShell 的详细信息，请参阅 [Windows PowerShell 入门](https://technet.microsoft.com/library/hh857337.aspx)。

要获取你在本教程中看到的任何 cmdlet 的详细帮助，请使用 **Get-Help** cmdlet。

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
例如，若要获取有关 **Connect-AzureRmAccount** cmdlet 的帮助，请键入：

```PowerShell
Get-Help Connect-AzureRmAccount -Detailed
```

还可阅读以下文章，熟悉 Azure PowerShell 中的 Azure 资源管理器部署模型：

* [如何安装和配置 Azure PowerShell](/powershell/azure/overview)
* [将 Azure PowerShell 用于 Resource Manager](../powershell-azure-resource-manager.md)

## <a id="connect"></a>连接到订阅
启动 Azure PowerShell 会话，并使用以下命令登录 Azure 帐户：  

```PowerShell
Connect-AzureRmAccount
```

>[!NOTE]
 若要使用特定的 Azure 实例，请使用 -Environment 参数。 例如： 
 ```powershell
 Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

在弹出的浏览器窗口中，输入 Azure 帐户用户名和密码。 Azure PowerShell 会获取与此帐户关联的所有订阅，并按默认使用第一个订阅。

如果有多个订阅，并想要指定其中一个订阅供 Azure 密钥保管库使用，请键入以下内容以查看帐户的订阅：

```powershell
Get-AzureRmSubscription
```

然后，要指定要使用的订阅，请键入：

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

有关配置 Azure PowerShell 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a id="resource"></a>创建新的资源组
使用 Azure 资源管理器时，会在资源组中创建所有相关资源。 在本教程中，我们将创建名为 **ContosoResourceGroup** 的新资源组：

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>创建密钥保管库
使用 [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) cmdlet 创建密钥保管库。 此 cmdlet 包含三个必需参数：**资源组名称**、**密钥保管库名称**和**地理位置**。

例如，如果使用：
- 保管库名称 **ContosoKeyVault**。
- 资源组名称 **ContosoResourceGroup**。
- 位置“美国东部”。

需键入：

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Key Vault 创建命令完成后的输出](./media/key-vault-get-started/output-after-creating-keyvault.png)

此 cmdlet 的输出显示创建的密钥保管库的属性。 两个最重要的属性是：

* **保管库名称**：在本示例中为 **ContosoKeyVault**。 将在其他密钥保管库 cmdlet 中使用此名称。
* **保管库 URI**：在本示例中，此项为 https://contosokeyvault.vault.azure.net/。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

Azure 帐户现已获取在此密钥保管库上执行任何作业的授权。 而且没有其他人有此授权。

> [!NOTE]
> 在尝试创建新的密钥保管库时，可能会看到错误“该订阅未注册为使用命名空间‘Microsoft.KeyVault’”。 如果显示该消息，请运行 `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`。 成功完成注册以后，可重新运行 New-AzureRmKeyVault 命令。 有关详细信息，请参阅 [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider)。
>
>

## <a id="add"></a>将密钥或机密添加到密钥保管库
可能需要以多种不同的方式与 Key Vault 以及密钥或机密交互。

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Azure Key Vault 生成软件保护密钥

如果希望 Azure Key Vault 创建一个软件保护密钥，请使用 [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) cmdlet，并键入：

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
若要查看此密钥的 URI，请键入：
```powershell
$key.id
```

可以通过密钥的 URI 引用已创建或上传到 Azure Key Vault 的密钥。 若要获取当前版本，可以使用 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**，使用 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** 可获取此特定版本。  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>将现有的 PFX 文件导入 Azure Key Vault

如果现有的密钥存储在 pfx 文件中，而该文件需上传到 Azure Key Vault，则步骤是不同的。 例如：
- 如果在 .PFX 文件中已经有一个软件保护密钥
- 该 pfx 文件名为 softkey.pfx 
- 该文件存储在 C 驱动器中。

可键入：

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

然后键入以下内容，以便从 .PFX 文件导入密钥，从而通过 Key Vault 服务中的软件来保护该密钥：

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

若要显示此密钥的 URI，请键入：

```powershell
$Key.id
```
若要查看密钥，请键入： 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
若要在门户中查看 PFX 文件的属性，则会看到类似于下图所示的内容。

![证书在门户中的显示效果](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>向 Azure Key Vault 添加机密

若要将名为 SQLPassword 且其 Azure 密钥保管库的值为 Pa$$w0rd 的机密（属于密码）添加到保管库，请先键入以下内容，将 Pa$$w0rd 的值转换成安全字符串：

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

然后，键入：

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


现在，可以通过使用密码的 URI，引用已添加到 Azure 密钥保管库的此密码。 使用 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** 始终可获取当前版本，而使用 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** 可获取此特定版本。

若要显示此机密的 URI，请键入：

```powershell
$secret.Id
```
若要查看机密，请键入 `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`。也可在门户中查看该机密。

![secret](./media/key-vault-get-started/secret-value.png)

若要查看机密中包含的纯文本形式的值，请执行以下命令：
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
现在，可以在应用程序中使用该密钥保管库以及密钥或机密。 必须授权应用程序使用这些信息。  

## <a id="register"></a>将应用程序注册到 Azure Active Directory
此步骤通常由开发人员在独立的计算机上完成。 它不是特定于 Azure Key Vault 的。 如需将应用程序注册到 Azure Active Directory 的详细步骤，则应参阅[将应用程序与 Azure Active Directory 集成](../active-directory/develop/active-directory-integrating-applications.md)一文或[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)一文

> [!IMPORTANT]
> 要完成本教程，帐户、保管库以及会在本步骤中注册的应用程序全都必须位于相同的 Azure 目录中。


使用密钥保管库的应用程序必须使用 Azure Active Directory 的令牌进行身份验证。 为此，应用程序的所有者首先必须在其 Azure Active Directory 中注册该应用程序。 注册结束后，应用程序所有者会获得以下值：

- **应用程序 ID** 
- **身份验证密钥**（也称共享机密）。 

应用程序必须向 Azure Active Directory 提供这两个值才能获取令牌。 如何将应用程序配置为执行此操作取决于应用程序。 对于 [Key Vault 示例应用程序](https://www.microsoft.com/download/details.aspx?id=45343)，应用程序所有者会在 app.config 文件中设置这些值。


要在 Azure Active Directory 中注册应用程序，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧单击“应用注册”。 如果没有看到应用注册，单击“更多服务”即可找到。  
>[!NOTE]
必须选择包含用于创建 Key Vault 的 Azure 订阅的相同目录。 
3. 单击“新建应用程序注册”。
4. 在“创建”边栏选项卡上提供应用程序的名称，然后选择“WEB 应用程序和/或 WEB API”（默认值）并指定 Web 应用程序的“登录 URL”。 对于此步骤，如果目前没有该信息，可以进行编造（例如，可以指定 http://test1.contoso.com）。 这些网站是否存在并不重要。 

    ![新建应用程序注册](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    请确保选择“WEB 应用程序和/或 WEB API”，否则在设置下看不到“密钥”选项。

5. 单击“创建”  按钮。
6. 完成应用注册以后，即可看到已注册应用的列表。 找到刚注册的应用，然后单击它。
7. 单击“已注册应用”边栏选项卡，然后复制**应用程序 ID**
8. 单击“所有设置”
9. 在“设置”边栏选项卡上，单击“密钥”
9. 在“密钥说明”框中键入说明，选择持续时间，然后单击“保存”。 页面会刷新，随后显示密钥值。 
10. 在下一步，需使用“应用程序 ID”和“密钥”信息来设置保管库的权限。

## <a id="authorize"></a>授权应用程序使用密钥或机密
可以通过两种方式授权应用程序访问保管库中的密钥或机密。

### <a name="using-powershell"></a>使用 PowerShell
若要使用 PowerShell，可使用 [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet。

例如，如果保管库名称是 **ContosoKeyVault**，要授权的应用程序的客户端 ID 为 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed，而希望授权应用程序使用保管库中的密钥来进行解密和签名，请运行以下命令：

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

如果要授权同一应用程序读取保管库中的机密，请运行以下命令：

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```
### <a name="using-the-azure-portal"></a>使用 Azure 门户
若要更改应用程序的授权以使用密钥或机密，请执行以下操作：
1. 从 Key Vault 资源边栏选项卡中选择“访问策略”
2. 单击边栏选项卡顶部的 [+ 新增] 按钮
3. 单击“选择主体”以选择前面创建的应用程序
4. 从“密钥权限”下拉列表中选择“解密”和“签名”，以授权应用程序使用保管库中的密钥进行解密和签名
5. 从“机密权限”下拉列表中选择“获取”，以允许应用程序读取保管库中的机密

## <a id="HSM"></a>使用硬件安全模块 (HSM)
为了提高可靠性，可以在硬件安全模块 (HSM) 中导入或生成永不超出 HSM 边界的密钥。 这些 HSM 都通过 FIPS 140-2 第 2 级验证。 如果此要求对你不适用，请跳过本部分并转到[删除密钥保管库以及关联的密钥和机密](#delete)。

若要创建这些 HSM 保护的密钥，必须使用 [Azure 密钥保管库高级服务层以支持 HSM 保护的密钥](https://azure.microsoft.com/pricing/free-trial/)。 另外，请注意 Azure China 不提供此功能。

创建密钥保管库时，添加 **-SKU** 参数：

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


可以将软件保护的密钥（如前所示）和 HSM 保护的密钥添加到此密钥保管库。 要创建受 HSM 保护的密钥，请将 **-Destination** 参数设为“HSM”：

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

可以使用以下命令，从计算机上的 .PFX 文件导入密钥。 此命令会将密钥导入到密钥保管库服务中的 HSM：

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

下一个命令会导入“自带密钥”(BYOK) 包。 通过此方案，可以在本地 HSM 中生成密钥，并将其传输到密钥保管库服务中的 HSM，而密钥不会超出 HSM 界限：

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

有关如何生成此 BYOK 包的详细说明，请参阅 [如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥](key-vault-hsm-protected-keys.md)。

## <a id="delete"></a>删除密钥保管库以及关联的密钥和机密
如果不再需要密钥保管库及其包含的密钥或机密，可以使用 [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault) cmdlet 来删除密钥保管库：

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

或者，可以删除整个 Azure 资源组，其中包括密钥保管库和你加入该组的任何其他资源：

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>其他 Azure PowerShell Cmdlet
可能会发现有助于管理 Azure 密钥保管库的其他命令：

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`：此命令获取以表格形式显示的所有密钥和所选属性。
- `$Keys[0]`：此命令显示特定密钥的完整属性列表
- `Get-AzureKeyVaultSecret`：此命令列出以表格形式显示的所有机密名称和所选属性。
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`：示范如何删除特定密钥。
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`：示范如何删除特定机密。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 密钥保管库的概述信息，请参阅 [什么是 Azure 密钥保管库？](key-vault-whatis.md)
- 若要查看如何使用密钥保管库，请参阅 [Azure 密钥保管库日志记录](key-vault-logging.md)。
- 有关在 web 应用程序中使用 Azure 密钥保管库的后续教程，请参阅 [从 Web 应用程序使用 Azure 密钥保管库](key-vault-use-from-web-application.md)。
- 有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](key-vault-developers-guide.md)。
- 有关 Azure 密钥保管库的最新 Azure PowerShell cmdlet 列表，请参阅 [Azure 密钥保管库 Cmdlet](/powershell/module/azurerm.keyvault/#key_vault)。
