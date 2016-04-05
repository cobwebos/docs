<!-- not suitable for Mooncake -->

Azure 库提供了由 Microsoft、第三方公司和开源软件计划开发的各种流行 Web 应用。从 Azure 库中创建的 Web 应用不要求安装任何软件，只需通过浏览器连接到 [Azure 预览门户](https://manage.windowsazure.cn/)即可。

在本教程中，您将学习：

- 如何通过 Azure 库创建新的 Web 应用。

- 如何通过 Azure 预览门户部署 Web 应用。
 
您将构建一个使用默认模板的 WordPress 博客。下图演示了完整的应用程序：


![Wordpress 博客][13]

>[AZURE.NOTE] 如果想要在注册 Azure 帐户之前开始使用 Azure，请转到[试用 Azure Web 应用](https://tryappservice.azure.com/)，你可以在 Azure 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。

## 在门户中创建 Web 应用

1. 登录到 Azure 预览门户。

2. 可以通过单击“应用商店”图标，打开 Azure 库：

    ![应用商店图标][marketplace]

    或通过单击仪表板右上方的“新建”图标并选择列表底部的“应用商店”。
	
    ![新建][5]
	
3. 选择“Web + 移动”。搜索 **WordPress**，然后单击 **WordPress** 图标。

	![列表中的 WordPress][7]
	
5. 阅读完 WordPress 应用的说明后，选择“创建”。

6. 单击“Web 应用”，并提供用于配置 Web 应用所需的值。
	
    ![配置应用][8]

7. 单击“数据库”，并提供用于配置 MySQL 数据库所需的值。

    ![配置数据库][database]

8. 提供新资源组的名称。

    ![设置资源组][groupname]

8. 如有必要，单击“订阅”，并指定要使用的订阅。

7. 完成定义 Web 应用后，请单击“创建”，并等待创建新的 Web 应用。

   创建应用后，你将看到包含 Web 应用和数据库的资源组。

   ![显示组][resourcegroup]

## 启动和管理你的 WordPress Web 应用
	
1. 单击新的 Web 应用以查看有关你的应用的详细信息。

    ![启动仪表板][10]

2. 在“基本功能”页上，单击“浏览”或 **Url** 下方的链接可打开 Web 应用的欢迎页面。

    ![网站 URL][browse]

3. 如果还未安装 WordPress，请输入 WordPress 所需的正确配置信息，然后单击“安装 WordPress”以完成配置并打开 Web 应用的登录页面。

4. 单击“登录”并输入你的凭据。

5. 你将拥有一个新的 WordPress Web 应用，它看起来类似以下 Web 应用。

	![你的 WordPress 网站][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png

<!---HONumber=Mooncake_0328_2016-->