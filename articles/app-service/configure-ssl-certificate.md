---
title: 添加和管理 SSL 证书
description: 在 Azure 应用服务中创建免费的证书、导入应用服务证书、导入 Key Vault 证书或购买应用服务证书。
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 310bf168b701ba6c37f71bc968da8e9114458e6f
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425301"
---
# <a name="add-an-ssl-certificate-in-azure-app-service"></a>在 Azure 应用服务中添加 SSL 证书

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。 本文介绍如何创建私有证书或公用证书，或将其上传或导入到应用服务中。 

将证书添加到应用服务应用或[函数应用](https://docs.microsoft.com/azure/azure-functions/)后，即可[使用它来保护自定义 DNS 名称](configure-ssl-bindings.md)或[在应用程序代码中使用它](configure-ssl-certificate-in-code.md)。

下表列出了用于在应用服务中添加证书的选项：

|选项|说明|
|-|-|
| 创建免费应用服务托管证书（预览版） | 如果只需保护 `www` [自定义域](app-service-web-tutorial-custom-domain.md)或应用服务中的任何非裸域，则可以轻松使用私有证书。 |
| 购买应用服务证书 | 由 Azure 管理的私有证书。 它结合了自动化证书管理的简单性以及续订和导出选项的灵活性。 |
| 导入来自 Key Vault 的证书 | 这在使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) 管理 [PKCS12 证书](https://wikipedia.org/wiki/PKCS_12)时很有用。 请参阅[私有证书要求](#private-certificate-requirements)。 |
| 上传私有证书 | 如果你已有第三方提供商提供的私有证书，则可以上传它。 请参阅[私有证书要求](#private-certificate-requirements)。 |
| 上传公用证书 | 公用证书不用于保护自定义域，但可以将其加载到代码中（如果需要它们来访问远程资源）。 |

## <a name="prerequisites"></a>必备条件

按照本操作方法指南操作：

- [创建应用服务应用](/azure/app-service/)。
- 仅限免费证书：使用 [CNAME 记录](app-service-web-tutorial-custom-domain.md#map-a-cname-record)将子域（例如 `www.contoso.com`）映射到应用服务。

## <a name="private-certificate-requirements"></a>私有证书要求

> [!NOTE]
> Azure Web 应用**不**支持 AES256，并且所有 pfx 文件都应使用 TripleDES 进行加密。

[免费应用服务托管证书](#create-a-free-certificate-preview)或[应用服务证书](#import-an-app-service-certificate)已满足应用服务的要求。 如果选择将私有证书上传或导入到应用服务，则证书必须满足以下要求：

* 已导出为[受密码保护的 PFX 文件](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* 包含长度至少为 2048 位的私钥
* 包含证书链中的所有中间证书

若要保护 SSL 绑定中的自定义域，证书还有其他要求：

* 包含用于服务器身份验证的[扩展密钥用法](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) (OID = 1.3.6.1.5.5.7.3.1)
* 已由受信任的证书颁发机构签名

> [!NOTE]
> 椭圆曲线加密 (ECC) 证书  可用于应用服务，但本文不予讨论。 请咨询证书颁发机构，了解有关创建 ECC 证书的确切步骤。

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>创建免费证书（预览版）

免费应用服务托管证书是用于保护应用服务中的自定义 DNS 名称的统包解决方案。 它是一个功能完备的 SSL 证书，由应用服务管理并自动续订。 免费证书具有以下限制：

- 不支持通配符证书。
- 不支持裸域。
- 不可导出。
- 不支持 DNS A 记录。

> [!NOTE]
> 免费证书是由 DigiCert 颁发的。 对于某些顶级域，必须通过创建值为 `0 issue digicert.com` 的 [CAA 域记录](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization)显式允许 DigiCert 作为证书颁发者。
> 

若要创建免费应用服务托管证书，请执行以下操作：

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的左侧菜单中，选择“应用程序服务” > “\<app-name>”   。

在应用的左侧导航窗格中，选择“TLS/SSL 设置” > “私钥证书(.pfx)” > “创建应用服务托管证书”    。

![在应用服务中创建免费证书](./media/configure-ssl-certificate/create-free-cert.png)

对话框中列出了使用 CNAME 记录正确映射到应用的任何非裸域。 选择要为其创建免费证书的自定义域，然后选择“创建”  。 只能为每个受支持的自定义域创建一个证书。

操作完成后，会在“私钥证书”列表中看到该证书  。

![创建免费证书已完成](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> 若要使用此证书保护自定义域，仍需要创建证书绑定。 按照[创建绑定](configure-ssl-bindings.md#create-binding)中的步骤操作。
>

## <a name="import-an-app-service-certificate"></a>导入应用服务证书

如果从 Azure 购买应用服务证书，Azure 将管理以下任务：

- 负责 GoDaddy 的购买流程。
- 对证书执行域验证。
- 将证书保留在 [Azure Key Vault](../key-vault/key-vault-overview.md) 中。
- 管理证书续订（请参阅[续订证书](#renew-certificate)）。
- 在应用服务应用中自动将证书与导入的副本同步。

若要购买应用服务证书，请转到[启动证书申请](#start-certificate-order)。

如果你已有一个有效的应用服务证书，则可以：

- [将证书导入到应用服务中](#import-certificate-into-app-service)。
- [管理证书](#manage-app-service-certificates)，如对其进行续订、重新生成密钥和导出。

### <a name="start-certificate-order"></a>启动证书申请

在<a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">应用服务证书创建页</a>启动应用服务证书申请。

![启动应用服务证书购买](./media/configure-ssl-certificate/purchase-app-service-cert.png)

使用下表来帮助配置证书。 完成后，单击“创建”。 

| 设置 | 说明 |
|-|-|
| 名称 | 应用服务证书证书的友好名称。 |
| 裸域主机名 | 在此处指定根域。 颁发的证书可同时保护根域和 `www` 子域  。 在颁发的证书中，“公用名”字段包含根域，“使用者可选名称”字段包含 `www` 域。 若要仅保护子域，请在此处指定子域的完全限定域名（例如，`mysubdomain.contoso.com`）。|
| 订阅 | 将包含证书的订阅。 |
| 资源组 | 将包含证书的资源组。 例如，可以使用新资源组，或选择与应用服务应用相同的资源组。 |
| 证书 SKU | 确定要创建的证书类型是标准证书还是[通配符证书](https://wikipedia.org/wiki/Wildcard_certificate)。 |
| 法律条款 | 单击以确认你同意法律条款。 证书是从 GoDaddy 获取的。 |

### <a name="store-in-azure-key-vault"></a>存储在 Azure Key Vault 中

证书购买过程完成后，还需完成其他一些步骤才可开始使用此证书。 

选择[应用服务证书](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)页中的证书，然后单击“证书配置” > “步骤 1:   存储”。

![配置应用服务证书的 Key Vault 存储](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 是一项 Azure 服务，可帮助保护云应用程序和服务使用的加密密钥和机密。 它是为应用服务证书所选的存储。

在“Key Vault 状态”页，单击“Key Vault 存储库”以创建新的保管库或选择现有保管库   。 如果选择创建新的保管库，请使用下表以帮助配置保管库，然后单击“创建”。 在应用服务应用所在的订阅和资源组中创建新 Key Vault。

| 设置 | 说明 |
|-|-|
| 名称 | 由字母数字字符和短划线组成的唯一名称。 |
| 资源组 | 建议选择与应用服务证书相同的资源组。 |
| 位置 | 选择与应用服务应用相同的位置。 |
| 定价层 | 有关信息，请参阅 [Azure Key Vault 定价详细信息](https://azure.microsoft.com/pricing/details/key-vault/)。 |
| 访问策略| 定义应用程序和对保管库资源允许的访问权限。 可以稍后配置，请按照[授予多个应用程序访问密钥保管库的权限](../key-vault/key-vault-group-permissions-for-apps.md)的步骤进行操作。 |
| 虚拟网络访问 | 限制为仅特定 Azure 虚拟网络具有保管库访问权限。 可以稍后配置，请按照[配置 Azure Key Vault 防火墙和虚拟网络](../key-vault/key-vault-network-security.md)的步骤进行操作。 |

选择保管库后，关闭“Key Vault 存储库”页面  。 “步骤1:  存储”选项应显示绿色复选标记表示成功。 保持页面处于打开状态，执行下一步骤。

### <a name="verify-domain-ownership"></a>验证域所有权

在上一步中所用的同一“证书配置”页中，单击“步骤 2:   验证”。

![验证应用服务证书的域](./media/configure-ssl-certificate/verify-domain.png)

选择“应用服务验证”  。 由于已将域映射到 Web 应用（请参阅[先决条件](#prerequisites)），所以验证已经执行。 只需单击“验证”来完成此步骤  。 单击“刷新”  按钮，直到显示“证书为域已验证”消息  。

> [!NOTE]
> 支持四种类型的域验证方法： 
> 
> - **应用服务验证** - 当域已映射到同一订阅中的应用服务应用时，这是最方便的选项。 它可利用应用服务应用已验证域所有权这一事实。
> - **域** - 验证[从 Azure 购买的应用服务域](manage-custom-dns-buy-domain.md)。 Azure 会自动为你添加验证 TXT 记录，并完成该过程。
> - **邮件** - 通过向域管理员发送电子邮件来验证域。 选择此选项时会提供相应说明。
> - **手动** - 使用 HTML 页（仅标准证书）或 DNS TXT 记录验证域  。 选择此选项时会提供相应说明。

### <a name="import-certificate-into-app-service"></a>将证书导入到应用服务中

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的左侧菜单中，选择“应用程序服务” > “\<app-name>”   。

在应用的左侧导航窗格中，选择“TLS/SSL 设置” > “私钥证书(.pfx)” > “导入应用服务证书”    。

![将应用服务证书导入到应用服务中](./media/configure-ssl-certificate/import-app-service-cert.png)

选择刚刚购买的证书，然后选择“确定”  。

操作完成后，会在“私钥证书”列表中看到该证书  。

![导入应用服务证书已完成](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> 若要使用此证书保护自定义域，仍需要创建证书绑定。 按照[创建绑定](configure-ssl-bindings.md#create-binding)中的步骤操作。
>

## <a name="import-a-certificate-from-key-vault"></a>导入来自 Key Vault 的证书

如果使用 Azure Key Vault 管理证书，则可以将 PKCS12 证书从 Key Vault 导入到应用服务中，前提是该证书[满足要求](#private-certificate-requirements)。

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的左侧菜单中，选择“应用程序服务” > “\<app-name>”   。

在应用的左侧导航窗格中，选择“TLS/SSL 设置” > “私钥证书(.pfx)” > “导入 Key Vault 证书”    。

![将 Key Vault 证书导入到应用服务中](./media/configure-ssl-certificate/import-key-vault-cert.png)

使用下表来帮助选择证书。

| 设置 | 说明 |
|-|-|
| 订阅 | Key Vault 所属的订阅。 |
| Key Vault | 包含要导入的证书的保管库。 |
| 证书 | 从保管库中的 PKCS12 证书列表中进行选择。 保管库中的所有 PKCS12 证书都已通过其指纹列出，但在应用服务中并非支持所有证书。 |

操作完成后，会在“私钥证书”列表中看到该证书  。 如果导入失败并出现错误，则证书不满足[应用服务的要求](#private-certificate-requirements)。

![导入 Key Vault 证书已完成](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> 若要使用此证书保护自定义域，仍需要创建证书绑定。 按照[创建绑定](configure-ssl-bindings.md#create-binding)中的步骤操作。
>

## <a name="upload-a-private-certificate"></a>上传私有证书

从证书提供者处获得证书以后，请执行此部分的步骤，使证书可供应用服务使用。

### <a name="merge-intermediate-certificates"></a>合并中间证书

如果证书颁发机构在证书链中提供了多个证书，则需按顺序合并证书。

若要执行此操作，请在文本编辑器中打开收到的所有证书。

创建名为 mergedcertificate.crt  的合并证书文件。 在文本编辑器中，将每个证书的内容复制到此文件。 证书的顺序应遵循证书链中的顺序，以你的证书开头，以根证书结尾， 如以下示例所示：

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>将证书导出为 PFX

用生成证书请求时所用的私钥导出合并的 SSL 证书。

如果使用 OpenSSL 生成证书请求，则已创建私钥文件。 若要将证书导出为 PFX，请运行以下命令。 将占位符 _&lt;private-key-file>_ 和 _&lt;merged-certificate-file>_ 分别替换为私钥和合并证书文件的路径。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

出现提示时，定义导出密码。 稍后将 SSL 证书上传到应用服务时需使用此密码。

如果使用 IIS 或 Certreq.exe  生成证书请求，请将证书安装到本地计算机，然后[将证书导出为 PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)。

### <a name="upload-certificate-to-app-service"></a>将证书上传到应用服务

现在可以将证书上传到应用服务了。

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的左侧菜单中，选择“应用程序服务” > “\<app-name>”   。

在应用的左侧导航窗格中，选择“TLS/SSL 设置” > “私钥证书(.pfx)” > “上载证书”    。

![将私有证书上传到应用服务中](./media/configure-ssl-certificate/upload-private-cert.png)

在“PFX 证书文件”  中选择 PFX 文件。 在“证书密码”  中，键入导出 PFX 文件时创建的密码。 完成后，单击“上传”  。 

操作完成后，会在“私钥证书”列表中看到该证书  。

![上传证书文件已完成](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> 若要使用此证书保护自定义域，仍需要创建证书绑定。 按照[创建绑定](configure-ssl-bindings.md#create-binding)中的步骤操作。
>

## <a name="upload-a-public-certificate"></a>上传公用证书

支持使用 .cer 格式的公用证书  。 

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的左侧菜单中，选择“应用程序服务” > “\<app-name>”   。

在应用的左侧导航窗格中，单击“TLS/SSL 设置” > “公用证书(.cer)” > “上传公钥证书”    。

在“名称”  中，键入证书的名称。 在“CER 证书文件”  中，选择 CER 文件。

单击“上载” 。 

![将公用证书上传到应用服务中](./media/configure-ssl-certificate/upload-public-cert.png)

上传证书后，复制证书指纹并检查是否[使证书可访问](configure-ssl-certificate-in-code.md#make-the-certificate-accessible)。

## <a name="manage-app-service-certificates"></a>管理应用服务证书

本部分介绍如何管理在[导入应用服务证书](#import-an-app-service-certificate)中购买的应用服务证书。

- [为证书重新生成密钥](#rekey-certificate)
- [续订证书](#renew-certificate)
- [导出证书](#export-certificate)
- [删除证书](#delete-certificate)

### <a name="rekey-certificate"></a>为证书重新生成密钥

如果你认为证书的私钥已泄露，则可以为证书重新生成密钥。 选择[应用服务证书](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)页的证书，然后选择左侧导航窗格的“重新生成密钥和同步”  。

单击“重新生成密钥”以启动该过程  。 此过程需要 1 - 10 分钟才能完成。

![为应用服务证书重新生成密钥](./media/configure-ssl-certificate/rekey-app-service-cert.png)

通过重新生成证书的密钥，将使用证书颁发机构颁发的新证书滚动更新现有证书。

重新生成密钥操作完成后，单击“同步”  。同步操作会自动更新应用服务中证书的主机名绑定，而不会导致应用停机。

> [!NOTE]
> 如果未单击“同步”  ，应用服务会在 48 小时内自动同步证书。

### <a name="renew-certificate"></a>续订证书

若要在任何时候启用证书自动续订，请选择[应用服务证书](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)页面中的证书，然后单击左侧导航窗格的“自动续订设置”  。 默认情况下，应用服务证书具有一年的有效期。

选择“开”  ，然后单击“保存”  。 如果启用了自动续订，则证书会在到期前 60 天自动续订。

![自动续订应用服务证书](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

若要改为手动续订证书，请单击“手动续订”  。 可以请求在到期前 60 天手动续订证书。

续订操作完成后，单击“同步”  。同步操作会自动更新应用服务中证书的主机名绑定，而不会导致应用停机。

> [!NOTE]
> 如果未单击“同步”  ，应用服务会在 48 小时内自动同步证书。

### <a name="export-certificate"></a>导出证书

由于应用服务证书是 [Key Vault 机密](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)，因此可以导出该证书的 PFX 副本，并将其用于其他 Azure 服务或 Azure 之外的服务。

若要将应用服务证书导出为 PFX 文件，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令。 如果[已安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)，则还可以在本地运行该命令。 将占位符替换为[创建应用服务证书](#start-certificate-order)时使用的名称。

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

下载的 appservicecertificate.pfx  文件是一个原始 PKCS12 文件，其中包含公用证书和私有证书。 在每个提示中，针对导入密码和 PEM 密码使用空字符串。

### <a name="delete-certificate"></a>删除证书 

删除应用服务证书是最终且不可逆的。 此证书的任何应用服务绑定都将变得无效。 为了防止意外删除，Azure 在证书上放置锁定。 若要删除应用服务证书，必须先删除证书上的删除锁定。

选择[应用服务证书](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)页的证书，然后选择左侧导航窗格的“锁”  。

查找证书上锁定类型为“删除”的锁定  。 在该锁定右侧，选择“删除”  。

![应用服务证书的删除锁定](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

现在，你可以删除应用服务证书。 在左侧导航窗格中选择“概述”   > “删除”  。 在确认对话框中，键入证书名称并选择“确定”  。

## <a name="automate-with-scripts"></a>使用脚本自动化

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>更多资源

* [使用 SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)
* [实施 HTTPS](configure-ssl-bindings.md#enforce-https)
* [实施 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [在应用程序代码中使用 SSL 证书](configure-ssl-certificate-in-code.md)
* [常见问题解答：应用服务证书](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
