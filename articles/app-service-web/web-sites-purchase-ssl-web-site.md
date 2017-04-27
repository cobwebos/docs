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
ms.author: apurvajo;aelnably
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 00e252e249dbd1a38a4649e435071685860722e4
ms.lasthandoff: 04/07/2017


---

# <a name="add-an-ssl-certificate-to-your-app-service-app"></a>将 SSL 证书添加到应用服务应用
> [!div class="op_single_selector"]
> * [在 Azure 中购买 SSL 证书](web-sites-purchase-ssl-web-site.md)
> * [使用其他来源的 SSL 证书](web-sites-configure-ssl-certificate.md)
> 
> 

默认情况下，[Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)已使用 \*.azurewebsites.net 域的通配符证书为 Web 应用启用了 HTTPS。 如果你不打算设置自定义域，可以直接利用默认的 HTTPS 证书。 但是，就像所有[通配符域](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates)一样，Azure 通配符证书不如将自定义域与自己的证书搭配使用那么安全。

应用服务提供一种简化的方式让你在 Azure 门户中购买和管理 SSL 证书。 

本文介绍如何为 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)应用购买和设置 SSL 证书。 

> [!NOTE]
> 不能将自定义域名的 SSL 证书用于“免费”或“共享”应用服务计划中托管的应用。 若要使用 SSL 证书，必须将 Web 应用托管在“基本”、“标准”或“高级”应用服务计划中。 更改订阅类型可能会更改订阅的计费量。 有关详细信息，请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/web-sites/)。
> 
> 

> [!WARNING]
> 请不要尝试使用没有与有效信用卡关联的订阅购买 SSL 证书， 否则可能导致订阅被禁用。 
> 
> 

## <a name="prerequisites"></a>先决条件
若要为某个自定义域启用 HTTPS，请先[将自定义域名映射到 Azure 应用](web-sites-custom-domain-name.md)。

请求 SSL 证书之前，请先确定要使用证书保护的域名。 这样就可以确定所需的证书类型。 如果只需保护单个域名（例如 contoso.com *或* www.contoso.com），可以使用标准（基本）证书。 如果需要保护多个域名（例如 contoso.com、www.contoso.com *和* mail.contoso.com），可以获取[通配符证书](http://en.wikipedia.org/wiki/Wildcard_certificate)。

## <a name="bkmk_purchasecert"></a>购买 SSL 证书

1. 在 [Azure 门户](https://portal.azure.com/)上的菜单中，选择“浏览”。 在“搜索”框中，键入“应用服务证书”。 在搜索结果中，选择“应用服务证书”。 

   ![使用“浏览”创建](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
2. 在“应用服务证书”页上，选择“添加”。 

   ![添加证书](./media/app-service-web-purchase-ssl-web-site/add.jpg)

3. 输入 SSL 证书的**名称**。
4. 输入**主机名**。
   
   > [!WARNING]
   > 这是购买过程中的最重要步骤之一。 请务必输入想要使用此证书保护的正确主机名（自定义域）。 *切勿*在主机名的开头添加“www”。 例如，如果自定义域名为 www.contoso.com，请在“主机名”框中输入 **contoso.com**。 该证书将保护 www 和根域。 
   > 

5. 选择你的**订阅**。 
   
   如果你有多个订阅，请在自定义域或 Web 应用使用的同一订阅中创建 SSL 证书。

6. 选择或创建一个**资源组**。
   
   可以使用资源组以单位的形式管理相关的 Azure 资源。 如果你要为应用建立基于角色的访问控制 (RBAC) 规则，资源组可发挥作用。 有关详细信息，请参阅“Managing your Azure resources”（管理 Azure 资源）。

7. 选择“证书 SKU”。 
   
   选择符合需要的证书 SKU，然后选择“创建”。 
   
   可以选择应用服务中的两个 SKU 之一：
   * **S1**：标准证书，有效期为一年，可自动续订  
   * **W1**：通配符证书，有效期为一年，可自动续订       
  
    ![证书 SKU](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

    有关详细信息，请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/web-sites/)。

> [!NOTE]
> 创建 SSL 证书最长需要 10 分钟。 创建过程会在后台执行多个步骤。  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>将证书存储在 Azure Key Vault 中

1. 完成 SSL 证书购买后，请在 Azure 门户中转到“应用服务证书”边栏选项卡。

   ![证书已准备好存储在 Key Vault 中](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   可以看到，证书状态为“等待颁发”。 在开始使用此证书之前，需要完成几个步骤。

2. 在“证书属性”边栏选项卡中，选择“证书配置”。 若要将此证书存储在 Azure Key Vault 中，请选择“步骤 1: 存储”。
3. 在“Key Vault 状态”边栏选项卡中，若要选择用于存储此证书的现有 Key Vault，请选择“Key Vault 存储库”。  若要在同一订阅和资源组中创建新的 Key Vault，请选择“创建新的 Key Vault”。

   ![创建新的 Key Vault](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > 在 Azure Key Vault 中存储证书会产生少量的费用。 有关详细信息，请参阅 [Azure Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)。
   > 
   > 

4. 选择用于存储证书的 Key Vault 存储库后，请在“Key Vault 状态”边栏选项卡顶部选择“存储”按钮。  
   
若要检查所做的选择，可以单击浏览器的刷新按钮。 此时会出现一个绿色的复选标记，表示此步骤已完成。

## <a name="bkmk_VerifyOwnership"></a>验证域所有权

1. 在“证书配置”边栏选项卡中，选择“步骤 2: 验证”。
2. 参考以下信息选择验证选项。 

应用服务证书支持三种类型的域验证：

   * 域验证
   * 邮件验证
   * 手动验证

### <a name="domain-verification"></a>域验证 
     
域验证是最方便的过程，但是，*仅当*已[从 Azure 应用服务购买自定义域](custom-dns-web-site-buydomains-web-app.md)时，才能使用这种验证。

1. 若要完成此步骤，请选择“验证”。
2. 若要在完成验证后更新证书状态，请选择“刷新”。 验证可能需要几分钟才能完成。

### <a name="mail-verification"></a>邮件验证
     
对于自定义域，会将一封验证电子邮件发送到与该域关联的电子邮件地址。 

1. 若要完成电子邮件验证步骤，请打开电子邮件，然后单击验证链接。 
2. 如果需要重新发送验证电子邮件，请单击“重新发送电子邮件”按钮。

### <a name="manual-verification"></a>手动验证    
     
**HTML 网页验证（仅适用于标准证书 SKU）**

1. 创建名为 starfield.html 的 HTML 文件。 此文件的内容应与域验证令牌的名称完全相同。 （可以从“域验证状态”边栏选项卡复制该令牌。）
2. 将此文件上载到托管域的 Web 服务器的根目录， 例如 /.well-known/pki-validation/starfield.html。
3.  完成验证后，若要更新证书状态，请选择“刷新”。 验证可能需要几分钟才能完成。

    例如，如果为域验证令牌为 **tgjgthq8d11ttaeah97s3fr2sh** 的 **contosocertdemo.com** 购买了标准证书，则对 **http://contosocertdemo.com/.well-known/pki-validation/starfield.html** 发出的 Web 请求应返回 **tgjgthq8d11ttaeah97s3fr2sh**。

**DNS TXT 记录验证**
        
1. 使用 DNS 管理器，在 **@** 子域上创建值等于**域验证令牌**的 TXT 记录。
2. 若要在完成验证后更新证书状态，请选择“刷新”。 验证可能需要几分钟才能完成。
 
   例如，若要对主机名为 **\*.contosocertdemo.com** 或 **\*.subdomain.contosocertdemo.com** 且域验证令牌为 **tgjgthq8d11ttaeah97s3fr2sh** 的通配符证书执行验证，则需要在 **contosocertdemo.com** 上创建一条值为 **tgjgthq8d11ttaeah97s3fr2sh** 的 TXT 记录。     

## <a name="bkmk_AssignCertificate"></a>将证书分配到应用服务应用

> [!NOTE]
> 在完成本部分中的步骤之前，必须将某个自定义域名与应用相关联。 有关详细信息，请参阅[为 Web 应用配置自定义域名](web-sites-custom-domain-name.md)。
> 
> 

1. 在 [Azure 门户](https://portal.azure.com/)上的菜单中，选择“应用服务”。
2. 选择要将此证书分配到的应用的名称。 
3. 转到“设置” > “SSL 证书” > “导入应用服务证书”，然后选择该证书。

   ![导入证书](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

4. 在“SSL 绑定”部分中，选择“添加绑定”。
5. 在“添加 SSL 绑定”边栏选项卡中，选择要使用 SSL 证书保护的域名。 选择要使用的证书。 还可以选择是要使用[服务器名称指示 (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication) 还是使用基于 IP 的 SSL。

   ![SSL 绑定](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
    * 为了将证书与域名相关联，基于 IP 的 SSL 会将服务器的专用公共 IP 地址映射到域名。 使用基于 IP 的 SSL 时，与服务关联的每个域名（例如 contoso.com 或 fabricam.com）必须有一个专用 IP 地址。 这是将 SSL 证书与 Web 服务器关联的传统方法。
    * 基于 SNI 的 SSL 是 SSL 和[传输层安全性](http://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) 的扩展。 使用基于 SNI 的 SSL 时，多个域可以共享同一个 IP 地址。 每个域都有独立的安全证书。 大多数新式浏览器（包括 Internet Explorer、Chrome、Firefox 和 Opera）都支持 SNI。 旧式浏览器可能不支持 SNI。 有关 SNI 的详细信息，请参阅 Wikipedia 中的 [Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)（服务器名称指示）。

6. 若要保存更改并启用 SSL，请选择“添加绑定”。

如果你选择“基于 IP 的 SSL”，并且自定义域是使用 A 记录配置的，则必须完成以下附加步骤。

1.  设置基于 IP 的 SSL 绑定后，系统会将一个专用 IP 地址分配给应用。 若要查找 IP 地址，请转到“设置” > “自定义域”。 在紧靠在“主机名”部分的上方，你的 IP 地址已列为“外部 IP 地址”。

   ![基于 IP 的 SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
    
  请注意，此 IP 地址与以前用于配置域的 A 记录的虚拟 IP 地址不同。 如果应用已设置为使用基于 SNI 的 SSL 或者未设置为使用 SSL，则此处不会列出任何 IP 地址。

2.  使用域名注册机构提供的工具修改自定义域名的 A 记录，使其指向在上一步骤中使用的 IP 地址。

3.  若要验证证书是否已正确配置，请使用 HTTPS:// 而不是 HTTP:// 访问应用。

## <a name="bkmk_Export"></a>导出应用服务证书
可以创建应用服务证书的本地 PFX 副本。 创建本地副本后，可将该证书用于其他 Azure 服务。 有关详细信息，请阅读我们的博客文章 [Create a local PFX copy of your App Service certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)（创建应用服务证书的本地 PFX 副本）。

## <a name="bkmk_Renew"></a>自动续订应用服务证书
若要设置证书自动续订或要手动续订证书，请在“证书属性”边栏选项卡中选择“自动续订设置”。 

![自动续订设置](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

若要在证书过期之前自动续订，将“自动续订”设置为“打开”。 这是默认选项。 如果已启用自动续订，我们会在证书过期之前的 90 天尝试续订该证书。 如果已通过 Azure 门户在应用服务应用中创建 SSL 绑定，则新证书准备就绪时，也会更新这些绑定（类似于“重新生成密钥并同步”方案）。 

如果你想要手动续订，请将“自动续订”设置为“关闭”。 只能在应用服务证书过期前 90 天内手动续订该证书。

## <a name="bkmk_Rekey"></a>重新生成密钥并同步证书

1. 如果需要重新生成证书的密钥（出于安全原因），请在“证书属性”边栏选项卡中选择“重新生成密钥和同步”。 
2. 选择“重新生成密钥”。 该过程最多可能需要 20 分钟才能完成。 

   ![重新生成 SSL 密钥](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

下面是有关重新生成密钥的一些补充信息：

* 重新生成证书密钥会使用新证书来滚动更新原有证书。 新证书由证书颁发机构颁发。
* 在证书的生存期内，你不需要支付重新生成密钥的费用。 
* 重新生成证书密钥会使该证书进入“等待颁发”状态。 
* 证书准备就绪后，为了防止服务中断，请确保使用该证书同步资源。
* 同步选项不适用于尚未分配到 Web 应用的证书。 

## <a name="next-steps"></a>后续步骤

* [使用 HTTPS 保护应用的自定义域](web-sites-configure-ssl-certificate.md)
* [在 Azure 应用服务中购买和配置自定义域名](custom-dns-web-site-buydomains-web-app.md)
* [Microsoft Azure 信任中心](https://azure.microsoft.com/en-us/support/trust-center/)

> [!NOTE]
> 如果要在注册 Azure 帐户之前就开始使用 Azure 应用服务，请参阅 [Try App Service](https://azure.microsoft.com/try/app-service/)（试用应用服务）。 可以在应用服务中创建一个生存期较短的初学者 Web 应用。 这不需要使用信用卡，也不需要做出承诺。
> 
> 

