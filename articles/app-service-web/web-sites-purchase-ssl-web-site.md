<properties
	pageTitle="为 Azure Web 应用购买和配置 SSL 证书"
	description="了解如何为 Azure Web 应用购买和配置 SSL 证书。"
	services="app-service"
	documentationCenter=".net"
	authors="apurvajo"
	manager="stefsch"
	editor="cephalin"
	tags="buy-ssl-certificates"/>

<tags
	ms.service="app-service"
	ms.date="03/30/2016"
	wacn.date=""/>

#为 Azure 购买和配置 SSL 证书

> [AZURE.SELECTOR]
- [购买用于 Web Apps 的 SSL 证书](/documentation/articles/web-sites-purchase-ssl-web-site)
- [为自定义域启用 SSL](/documentation/articles/web-sites-configure-ssl-certificate)  

默认情况下，**[Azure Web 应用](/documentation/services/web-sites/)**已使用 \*.chinacloudsites.cn 域的通配符证书为你的 Web 应用启用 HTTPS。如果不打算配置自定义域，可以直接利用默认的 HTTPS 证书。但是，就像所有 [通配符域](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates)一样，这并不如将自定义域与自己的证书搭配使用那么安全。
Azure 现在提供一种真正简单的方式来让你直接从 Azure 门户购买和管理 SSL 证书，而完全不用离开门户。
本文说明如何使用 3 个简单的步骤来购买和配置 **[Azure Web 应用](/documentation/services/web-sites/)**的 SSL 证书。

> [AZURE.NOTE]
自定义域名的 SSL 证书不能用于免费和共享的 Web 应用。你必须将 Web 应用配置为“基本”、“标准”或“高级”模式，这可能会更改对你的订阅的计费量。有关详细信息，请参阅 **[Web Apps Pricing Details（Web Apps 定价详细信息）](/home/features/web-site/#price)**。



##<a name="bkmk_Overview"></a>概述
> [AZURE.NOTE]
请不要尝试使用没有与有效信用卡关联的订阅购买 SSL 证书。否则可能导致你的订阅被禁用。

## 购买、存储和分配自定义域的 SSL 证书
若要为自定义域启用 HTTPS（例如 contoso.com），必须先**[在 Azure Web 应用中配置自定义域名](/documentation/articles/web-sites-custom-domain-name)**。

请求 SSL 证书之前，必须先确定将受证书保护的域名。这将确定必须获取的证书类型。如果你只需保护单个域名（例如 contoso.com 或 www.contoso.com ）的安全，标准（基本）证书就足够了。如果你需要保护多个域名（例如 contoso.com、 www.contoso.com 和 mail.contoso.com）的安全，则可以获取**[通配符证书](http://en.wikipedia.org/wiki/Wildcard_certificate)**

##<a name="bkmk_purchasecert"></a>步骤 0：订购 SSL 证书

在此步骤中，你将了解如何订购所选的 SSL 证书。

1.	在 **[Azure 门户](https://portal.azure.cn/)**中，单击“浏览”并在搜索栏中键入“Azure 证书”，从结果中选择“Azure 证书”，然后单击“添加”。 

    ![插入使用浏览创建的图像](./media/app-service-web-purchase-ssl-web-site/browse.jpg)

    ![插入使用浏览创建的图像](./media/app-service-web-purchase-ssl-web-site/add.jpg)

2.	为 SSL 证书提供**友好名称**。

3.	输入**主机名**
> [AZURE.NOTE]
    这是购买过程中的最重要步骤之一。请务必输入想要使用此证书保护的正确主机名（自定义域）。**切勿**在主机名前面附加 WWW。例如，如果自定义域名为 www.contoso.com ，只需在“主机名”字段中输入 contoso.com，相关的证书将保护 www 和根域。
    
4.	选择你的**订阅**。

    如果你有多个订阅，请务必在用于相关自定义域或 Web 应用的同一订阅中创建 SSL 证书。
       
5.	选择或创建**资源组**。

    资源组可你让以单位形式管理相关的 Azure 资源，并可在为应用创建基于角色的访问控制 (RBAC) 规则时发挥作用。有关详细信息，请参阅“Managing your Azure resources”（管理 Azure 资源）。
     
6.	选择**证书 SKU**

    最后，选择符合需要的证书 SKU，然后单击“创建”。现在，Azure 可让你购买两个不同的 SKU
           • S1 - 标准证书，有效期为 1 年，可自动续订  
           • W1 - 通配符证书，有效期为 1 年，可自动续订      
    有关详细信息，请参阅 **[Web Apps Pricing Details（Web Apps 定价详细信息）](/home/features/web-site/#price)**。

![插入证书 SKU 的图像](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [AZURE.NOTE]
创建 SSL 证书需要 1 - 10 分钟。此过程在后台执行多个步骤，如果手动执行这些步骤，则会非常繁琐。

##<a name="bkmk_StoreKeyVault"></a>步骤 1：将证书上载到 Azure 密钥保管库

在此步骤中，你将了解如何将 SSL 证书存储到所选的 Azure 密钥保管库。

1.	SSL 证书购买过程完成之后，你需要手动打开“Azure 证书”资源边栏选项卡，方法是再次浏览到该部分（请参阅上述“步骤 1”）   

    ![插入已准备好存储在 KV 中的图像](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)

    你将注意到证书状态是“等待颁发”，因为在可以开始使用此证书之前，还有一些步骤需要完成。
 
2. 单击“证书属性”边栏选项卡中的“证书配置”，然后单击“步骤 1: 存储”将此证书存储到 Azure 密钥保管库。

3.	在“密钥保管库状态”边栏选项卡中单击“密钥保管库存储库”，选择要存储此证书的现有密钥保管库，或者选择“创建新的密钥保管库”，以在同一订阅和资源组中创建新的密钥保管库。
 
    ![插入新建 KV 的图像](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
 
    > [AZURE.NOTE]
    在 Azure 密钥保管库中存储此证书会产生少量的费用。费用大约是 $0.03 美分。有关详细信息，请参阅 **[Azure Key Vault Pricing Details（Azure 密钥保管库定价详细信息）](/home/features/key-vault/#price)**。

4. 选择存储证书所在的密钥保管库存储库后，请单击“密钥保管库状态”边栏选项卡顶部的“存储”按钮以继续存储。

    然后应会完成使用所选的 Azure 密钥保管库存储购买的证书的步骤。刷新边栏选项卡后，你应会看到此步骤也带有绿色复选标记。
    
##<a name="bkmk_VerifyOwnership"></a>步骤 2：验证域所有权

在此步骤中，你将了解如何对刚刚订购的 SSL 证书执行域所有权验证。

1.	在“证书设置”边栏选项卡中单击“步骤 2: 验证”步骤。Azure 证书支持 4 种类型的域验证。

    * **Azure 验证** 
    
        * 如果已**将自定义域分配到 Azure Web 应用**，则这是最方便的过程。 此方法将列出符合此条件的所有 Azure Web 应用。
           例如，在本例中，**contosocertdemo.com** 是分配到名为**“ContosoCertDemo”**的 Azure Web 应用的自定义域，因此它是此处唯一列出的 Azure Web 应用。如果有多区域部署，则将列出跨区域的所有项。
        
           验证方法仅适用于标准（基本）证书购买。对于通配符证书，请跳过此过程并转到以下选项 B、C 或 D。
        * 单击“验证”按钮来完成此步骤。
        * 单击“刷新”，在完成验证之后更新证书状态。验证可能需要几分钟才能完成。
        
        ![插入 Azure 验证的图像](./media/app-service-web-purchase-ssl-web-site/AppVerify.jpg)

    * **域验证**

        * **仅当**已**[从 Azure Web 应用购买自定义域](/documentation/articles/custom-dns-web-site-buydomains-web-app)**时，这才是最方便的过程。
        
        * 单击“验证”按钮来完成此步骤。
        
        * 单击“刷新”，在完成验证之后更新证书状态。验证可能需要几分钟才能完成。

    * **邮件验证**
        
        * 验证电子邮件已发送到与此自定义域关联的电子邮件地址。
         
        * 打开电子邮件，然后单击验证链接以完成电子邮件验证步骤。
        
        * 如果需要重新发送验证电子邮件，请单击“重新发送电子邮件”按钮。
         
    * **手动验证**
                 
        1. **HTML 网页验证**
        
            * 创建名为 **{域验证令牌}**.html 的 HTML 文件（可以从“域验证状态”边栏选项卡复制令牌）
            
            * 此文件的内容应与“域验证令牌”的名称完全相同。
            
            * 在托管域的 Web 服务器的根目录上载此文件。
            
            * 单击“刷新”，在完成验证之后更新证书状态。验证可能需要几分钟才能完成。
            
            例如，如果你为具有域验证令牌 **'cAGgQrKc'** 的 contosocertdemo.com 购买了标准证书，则对 **'http://contosocertdemo.com/cAGgQrKc.html'** 发出的 Web 请求应返回 **cAGgQrKc**。
        2. **DNS TXT 记录验证**

            * 使用 DNS 管理器在具有与“域验证令牌”相同值的“DZC”子域上创建 TXT 记录。
            
            * 单击“刷新”，在完成验证之后更新证书状态。验证可能需要几分钟才能完成。
                              
            例如，若要对具有主机名 \***.contosocertdemo.com** 或 \***.subdomain.contosocertdemo.com** 且域验证令牌为 **cAGgQrKc** 的通配符证书执行验证，需要对值为 **cAGgQrKc** 的 dzc.contosocertdemo.com 创建 TXT 记录。


##<a name="bkmk_AssignCertificate"></a>步骤 3：将证书分配到 Azure Web 应用

在此步骤中，你将了解如何将这个新购买的证书分配到 Azure Web 应用。

> [AZURE.NOTE]
在执行本部分中的这些步骤之前，必须将某个自定义域名与你的应用相关联。有关详细信息，请参阅 **[Configuring a custom domain name for a web app（为 Web 应用配置自定义域名）](/documentation/articles/web-sites-custom-domain-name)**。

1.	在浏览器中，打开 **[Azure 门户](https://portal.azure.cn/)**。
2.	单击页面左侧的“应用程序服务”选项。
3.	单击要分配此证书的应用的名称。 
4.	在“设置”中，单击“自定义域和 SSL”。
5.	在“证书部分”中，单击“导入证书”并选择刚刚购买的证书

    ![插入导入证书的图像](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.jpg)

6. 在“SSL 设置”选项卡的“ssl 绑定”部分中，使用下拉菜单选择要使用 SSL 保护的域名，然后选择要使用的证书。你还可以选择是使用**[服务器名称指示 (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** 还是使用基于 IP 的 SSL。

    ![插入 SSL 绑定的图像](./media/app-service-web-purchase-ssl-web-site/SSLBindings.jpg)

       • 基于 IP 的 SSL 通过将服务器的专用公共 IP 地址映射到域名，将证书与域名相关联。这要求与您的服务相关联的每个域名（contoso.com、fabricam.com 等）都具有专用的 IP 地址。这是将 SSL 证书与某一 Web 服务器相关联的传统方法。• 基于 SNI 的 SSL 是对 SSL 和**[传输层安全性](http://zh.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) 的扩展，它允许多个域共享相同的 IP 地址，并且对于每个域都有单独的安全证书。当前常用的大多数浏览器（包括 Internet Explorer、Chrome、Firefox 和 Opera）都支持 SNI，但是，较旧的浏览器可能不支持 SNI。有关 SNI 的详细信息，请参阅 Wikipedia 上的文章 **[Server Name Indication（服务器名称指示）](http://en.wikipedia.org/wiki/Server_Name_Indication)**。
       
7. 单击“保存”以保存更改和启用 SSL。



如果你选择“基于 IP 的 SSL”，并且你的自定义域使用 A 记录进行配置，则必须执行以下附加步骤：

* 配置基于 IP 的 SSL 绑定后，将会向你的应用分配专用 IP 地址。你可以在应用的“仪表板”页上的“速览”部分中找到此 IP 地址。它将会作为“虚拟 IP 地址”列出：
    
    ![插入 IP SSL 的图像](./media/app-service-web-purchase-ssl-web-site/IPSSL.jpg)

    请注意，此 IP 地址将与以前用于配置您的域的 A 记录的虚拟 IP 地址不同。如果将您配置为使用基于 SNI 的 SSL，或未将您配置为使用 SSL，则不会为此条目列出任何地址。
    
2. 通过使用您的域名注册机构所提供的工具，修改您的自定义域名的 A 记录以指向上一步中的 IP 地址。此时，你应该能够使用 HTTPS:// 而不是 HTTP:// 访问你的应用，以便验证证书是否已正确配置。


##<a name="bkmk_Rekey"></a>重新生成密钥并同步证书

1. 如果出于安全原因而需要重新生成证书的密钥，只需在“证书属性”边栏选项卡中选择“重新生成密钥并同步”选项。 

2. 单击“重新生成密钥”按钮来启动该过程。此过程需要 1 - 10 分钟才能完成。

    ![插入重新生成 SSL 密钥的图像](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

3. 重新生成证书的密钥会使用证书颁发机构颁发的新证书来滚动更新现有证书。
4. 在证书的生存期内，你不需要支付重新生成密钥的费用。 
5. 重新生成证书密钥将会经历“等待颁发”状态。 
6. 证书准备就绪后，请确保使用此证书同步资源，以免服务中断。
7. 同步选项不适用于尚未分配到 Web 应用的证书。 

## 更多资源 ##
- [在 Azure Web 应用中为应用启用 HTTPS](/documentation/articles/web-sites-configure-ssl-certificate)
- [在 Azure Web 应用中购买和配置自定义域名](/documentation/articles/custom-dns-web-site-buydomains-web-app)
- [Azure 信任中心](/support/trust-center/security/)
- [Azure 网站中解锁的设置选项](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Azure 管理门户](https://manage.windowsazure.cn)

>[AZURE.NOTE] 如果想要在注册 Azure 帐户之前开始使用 Azure，请转到[试用 Azure Web 应用](https://tryappservice.azure.com/)，你可以在 Azure 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。



<!---HONumber=Mooncake_0509_2016-->