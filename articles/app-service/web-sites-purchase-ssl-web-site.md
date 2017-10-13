---
title: "将 SSL 证书添加到 Azure 应用服务应用 | Microsoft Docs"
description: "了解如何将 SSL 证书添加到应用服务应用。"
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo
ms.openlocfilehash: 191dd7240ad15b4936a72bc27a2d0162350f3afb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>为 Azure 应用服务购买和配置 SSL 证书

在本教程中，将保护 Web 应用的安全，方式是为 **[Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)**购买 SSL 证书、将其安全存储在 [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) 中并与自定义域进行关联。

## <a name="step-1---log-in-to-azure"></a>步骤 1 - 登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户

## <a name="step-2---place-an-ssl-certificate-order"></a>步骤 2 - 订购 SSL 证书

可通过在“Azure 门户”中新建[应用服务证书](https://portal.azure.com/#create/Microsoft.SSL)来订购 SSL 证书。

![证书创建](./media/app-service-web-purchase-ssl-web-site/createssl.png)

为 SSL 证书输入友好“名称”并输入“域名”

> [!NOTE]
> 这是购买过程中的最重要步骤之一。 请务必输入想要使用此证书保护的正确主机名（自定义域）。 **切勿**在主机名前面附加 WWW。 
>

选择“订阅”、“资源组”和“证书 SKU”

> [!WARNING]
> 应用服务证书仅可用于同一订阅中的其他应用服务。  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>步骤3 - 将证书存储在 Azure Key Vault 中

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) 是一项 Azure 服务，可帮助保护云应用程序和服务使用的加密密钥和机密。
>

SSL 证书购买过程完成之后，需要打开“[应用服务证书](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)”资源边栏选项卡。

![插入已准备好存储在 KV 中的图像](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

将看到证书状态为“等待颁发”，因为还需完成一些步骤，才可开始使用此证书。

单击“证书属性”边栏选项卡中的“证书配置”，然后单击“步骤 1: 存储”将此证书存储到 Azure Key Vault。

在“Key Vault 状态”边栏选项卡中单击“Key Vault 存储库”，选择要存储此证书的现有 Key Vault，或者选择“新建 Key Vault”，在同一订阅和资源组中创建新的 Key Vault。

> [!NOTE]
> 在 Azure Key Vault 中存储此证书会产生少量的费用。
> 有关详细信息，请参阅 **[Azure Key Vault 定价详细信息](https://azure.microsoft.com/pricing/details/key-vault/)**。
>

选择用于存储此证书的 Key Vault 存储库后，“存储”选项应显示为“成功”。

![插入表示在 KV 中存储成功的图像](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>步骤 4 - 验证域所有权

> [!NOTE]
> 应用服务证书支持 3 种类型的域验证：域验证、邮件验证和手动验证。 有关更多详细信息，请参阅[高级部分](#advanced)。

在步骤 3 中使用的同一“证书配置”边栏选项卡中，单击“步骤 2: 验证”。

域验证：此过程最简便，但仅适用于已**[从 Azure 应用服务购买自定义域](custom-dns-web-site-buydomains-web-app.md)**的情况。
请单击“验证”按钮完成此步骤。

![插入域验证图像](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

请在单击“验证”后使用“刷新”按钮，直到“验证”选项显示成功。

![插入表示在 KV 中成功验证的图像](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>步骤 5 - 将证书分配到应用服务应用

> [!NOTE]
> 在执行本部分中的这些步骤之前，必须将某个自定义域名与应用相关联。 有关详细信息，请参阅 **[为 Web 应用配置自定义域名](app-service-web-tutorial-custom-domain.md)**。
>

在 [Azure 门户](https://portal.azure.com/)中，单击页面左侧的“应用服务”选项。

单击要分配此证书的应用的名称。

在“设置”中，单击“SSL 证书”。

单击“导入应用服务证书”，再选择刚购买的证书。

![插入导入证书的图像](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

在“SSL 绑定”部分中单击“添加绑定”，使用下拉菜单选择要使用 SSL 保护的域名，并选择要使用的证书。 还可以选择是要使用**[服务器名称指示 (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** 还是使用基于 IP 的 SSL。

![插入 SSL 绑定的图像](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

单击“添加绑定”以保存更改并启用 SSL。

> [!NOTE]
> 若选中“基于 IP 的 SSL”，且自定义域是使用 A 记录配置的，则还必须执行以下步骤。 有关更多详细信息，请参阅[高级部分](#Advanced)。

此时，应可使用 `HTTPS://`（而非 `HTTP://`）访问应用，以验证证书已正确配置。

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>步骤 6 - 管理任务

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>高级

### <a name="verifying-domain-ownership"></a>验证域所有权

应用服务证书还支持其他两种类型的域验证：邮件验证和手动验证。

#### <a name="mail-verification"></a>邮件验证

验证电子邮件已发送到与此自定义域关联的电子邮件地址。
要完成电子邮件验证步骤，请打开电子邮件，并单击验证链接。

![插入电子邮件验证图像](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

如果需要重新发送验证电子邮件，请单击“重新发送电子邮件”按钮。

#### <a name="manual-verification"></a>手动验证

> [!IMPORTANT]
> HTML 网页验证（仅适用于标准证书 SKU）
>

1. 创建名为“starfield.html”的 HTML 文件

1. 此文件的内容应与“域验证令牌”的名称完全相同。 （可以从“域验证状态”边栏选项卡复制该令牌）

1. 在托管域的 Web 服务器的根目录处上传此文件 `/.well-known/pki-validation/starfield.html`

1. 单击“刷新”，在完成验证后更新证书状态。 验证可能需要几分钟才能完成。

> [!TIP]
> 使用 `curl -G http://<domain>/.well-known/pki-validation/starfield.html` 在终端中进行验证，响应需包含 `<verification-token>`。

#### <a name="dns-txt-record-verification"></a>DNS TXT 记录验证

1. 使用 DNS 管理器，在 `@` 子域上创建值等于“域验证令牌”的 TXT 记录。
1. 单击“刷新”，在完成验证后更新证书状态。

> [!TIP]
> 需要在 `@.<domain>` 上创建值为 `<verification-token>` 的 TXT 记录。

### <a name="assign-certificate-to-app-service-app"></a>将证书分配到应用服务应用

如果选择了“基于 IP 的 SSL”，并且自定义域是使用 A 记录配置的，则必须执行以下附加步骤：

配置基于 IP 的 SSL 绑定后，会向应用分配专用 IP 地址。 可以在“自定义域”页面中应用设置的下面（紧靠在“主机名”部分的上方）找到此 IP 地址。 此 IP 地址作为“外部 IP 地址”列出

![插入 IP SSL 的图像](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

请注意，此 IP 地址与先前配置域的 A 记录时所用的虚拟 IP 地址不同。 若要配置为使用基于 SNI 的 SSL，或未配置为使用 SSL，则不会为此条目列出任何地址。

通过使用域名注册机构所提供的工具，修改自定义域名的 A 记录以指向上一步中的 IP 地址。

## <a name="rekey-and-sync-the-certificate"></a>重新生成密钥并同步证书

如果需要重新生成证书的密钥，请在“证书属性”边栏选项卡中选择“重新生成密钥并同步”选项。

单击“重新生成密钥”按钮启动该过程。 此过程需要 1 - 10 分钟才能完成。

![插入重新生成 SSL 密钥的图像](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

通过重新生成证书的密钥，将使用证书颁发机构颁发的新证书滚动更新现有证书。

## <a name="next-steps"></a>后续步骤

* [添加内容交付网络](app-service-web-tutorial-content-delivery-network.md)