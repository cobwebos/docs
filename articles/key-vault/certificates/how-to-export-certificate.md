---
title: 从 Azure Key Vault 中导出证书
description: 从 Azure Key Vault 中导出证书
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588886"
---
# <a name="export-certificate-from-azure-key-vault"></a>从 Azure Key Vault 中导出证书

Azure Key Vault 使你能轻松地为网络预配、管理和部署数字证书，并支持应用程序的安全通信。 若要详细了解证书的常规信息，请参阅 [Azure Key Vault 证书](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

## <a name="about-azure-key-vault-certificate"></a>关于 Azure Key Vault 证书

### <a name="composition-of-certificate"></a>证书组合
创建 Key Vault 证书后，还可以创建具有相同名称的可寻址密钥和机密。 Key Vault 密钥允许密钥操作，Key Vault 机密允许以机密的形式检索证书值。 Key Vault 证书还包含公共 x509 证书元数据。 [了解详细信息](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>可导出的或不可导出的密钥
创建 Key Vault 证书后，可以使用 PFX 或 PEM 格式的私钥从可寻址机密中检索该证书。 用于创建证书的策略必须指示密钥可导出。 如果策略指示密钥不可导出，则在作为机密检索私钥时，该私钥不包括在值中。

证书支持以下两种类型的密钥：RSA 或 RSA HSM   。 仅 RSA 允许可导出，RSA HSM 不支持。 [了解详细信息](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

可以使用 Azure CLI、PowerShell 或门户导出 Azure Key Vault 中存储的证书。

> [!NOTE]
> 需要注意的是，在 Key Vault 中导入证书时，你只需要该证书的密码。 Key Vault 不会保存关联的密码，因此，在导出证书时，密码将为空。

## <a name="exporting-certificate-using-cli"></a>使用 CLI 导出证书
以下命令将允许你下载 Key Vault 证书的公有部分。

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
有关示例和参数定义，请[查看此处](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



如果要下载整个证书，即组成了证书的公有部分和私有部分，则可以通过将证书下载为一个机密以完成此操作。

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
有关参数定义，请[查看此处](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>使用 PowerShell 导出证书

此命令从名为 ContosoKV01 的密钥保管库中获取名为 TestCert01 的证书。 若要将证书下载为 pfx 文件，请运行以下命令。 这些命令访问 SecretId，并将内容另存为 pfx 文件。

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
这会导出具有私钥的整个证书链，并且此证书将受密码保护。
有关 ```Get-AzKeyVaultCertificate``` 命令和参数的详细信息，请参阅[示例 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)

## <a name="exporting-certificate-using-portal"></a>使用门户导出证书

在门户上，当你在“证书”边栏选项卡中创建/导入证书时，你将收到已成功创建证书的通知。 选择证书时，可以单击当前版本，你将看到下载选项。


单击“使用 CER 格式下载”或“使用 PFX/PEM 格式下载”按钮即可下载证书。


![证书下载](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>从密钥保管库导出应用服务证书

Azure 应用服务证书提供了一种购买 SSL 证书并在门户中直接将其分配到 Azure 应用的简便方法。 这些证书还可以作为 PFX 文件从门户导出，以便在其他地方使用。
导入后，可以在机密下找到应用服务证书。

有关导出应用服务证书的步骤，请[参阅此处](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>了解详细信息
* [各种证书文件类型和定义](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)