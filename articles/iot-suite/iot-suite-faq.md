<properties
  pageTitle="Azure IoT 套件常见问题 | Azure"
  description="有关 IoT 套件的常见问题"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.date="05/09/2016"
  wacn.date="05/17/2016"/>
   
# 有关 IoT 套件的常见问题

### 在 Azure 门户中删除资源组与在 azureiotsuite.com 中对预配置解决方案单击删除之间的区别是什么？

- 如果在 [azureiotsuite.com][lnk-azureiotsuite] 中删除预配置解决方案，则会删除在创建预配置解决方案时设置的所有资源；如果向资源组添加了其他资源，则也会删除这些资源。 

- 如果在 [Azure 门户][lnk-azure-portal]中删除某个资源组，则只删除该资源组中的资源；还需要在 [Azure 经典门户][lnk-classic-portal]中删除与预配置解决方案关联的 Azure Active Directory 应用程序。

### 在订阅中可以设置多少个 DocumentDB 实例？

五个。可以创建 [Azure 支持票证][link-azuresupportticket]以提高此限制，但是在默认情况下，对每个订阅只能设置五个 DocumentDB 实例。因此，在给定订阅中只能设置最多五个远程监视预配置解决方案。

### 在订阅中可以设置多少个免费必应地图 API？

两个。在订阅中只能创建两个免费必应 地图 API。远程监视解决方案在默认情况下使用免费必应地图 API 进行设置。因此，如果不进行修改，则在订阅中只能设置最多两个远程监视解决方案。

### 我的远程监视解决方案部署具有静态地图，我如何添加交互式必应地图？ 
1. 从 [Azure 门户][lnk-azure-portal]获取用于企业的必应地图 API 查询密钥。 
 1. 在 [Azure 门户][lnk-azure-portal]中导航到用于企业的必应地图 API 所处的资源组。
 2. 单击“所有设置”，然后单击“密钥管理”。 
 3. 你会注意到两个密钥：主密钥和查询密钥。复制查询密钥的值。

     > [AZURE.NOTE] 没有用于企业的必应地图 API 帐户？ 通过单击“+ 新建”、搜索用于企业的必应地图 API 并按照提示进行创建，在 [Azure 门户][lnk-azure-portal]中创建一个帐户。

2. 从 [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github] 下拉最新代码。

3. 在存储库的 /docs/ 文件夹中按照命令行部署指南运行本地或云部署。

4. 运行了本地或云部署之后，在根文件夹中查找在部署过程中创建的 *.user.config 文件。在文本编辑器中打开此文件。

5. 更改以下行，以包含为查询密钥复制的值：
   
  `<setting name="MapApiQueryKey" value="" />`

### 如果我具有 Azure for DreamSpark，是否可以创建预配置解决方案？
当前无法使用 [Azure for DreamSpark][lnk-dreamspark] 帐户创建预配置解决方案。但是，可以在仅仅几分钟内创建[试用][1rmb-trial]，这使你可以创建预配置解决方案。

### 如何删除 AAD 租户？

请参阅 Eric Golpe 的博客文章[删除 Azure AD 租户的演练][lnk-delete-aad-tennant]。

[link-azuresupportticket]: https://portal.azure.cn/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-azure-portal]: https://portal.azure.cn
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.cn
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99
[1rmb-trial]: /pricing/1rmb-trial
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=Mooncake_0523_2016-->