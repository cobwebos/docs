获取用于域名的记录已传播后，你应能够使用你的浏览器来验证可以使用自定义域名访问你在 Azure App Service 的 web 应用。

> [!NOTE]
> 它可能需要一些时间 cname 传播到整个 DNS 系统。 你可以使用一种服务，如<a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a>以验证该 CNAME 是否可用。
> 
> 

如果您尚未为流量管理器终结点添加你的 web 应用，你必须执行此操作之前的名称解析将起作用，作为自定义域名将路由到 Traffic Manager。 然后，流量管理器将路由到你的 web 应用。 使用中的信息[添加或删除终结点](../articles/traffic-manager/traffic-manager-endpoints.md)将你的 web 应用添加为 Traffic Manager 配置文件中的终结点。

> [!NOTE]
> 如果你的 web 应用未列出添加终结点时，请验证为配置**标准**App Service 计划模式。 必须使用**标准**才能使用流量管理器 web 应用的模式。
> 
> 

1. 在浏览器中，打开[Azure 门户](https://portal.azure.com)。
2. 在**Web Apps**选项卡上，单击你的 web 应用程序，而选择的名称**设置**，然后选择**自定义域**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. 在**自定义域**边栏选项卡，单击**将主机名添加**。
4. 使用**主机名**文本框中输入要与此 web 应用关联的流量管理器域名。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. 单击**验证**以保存域名配置。
6. 如果单击**验证**Azure 将启动域验证工作流。 这将检查域所有权以及主机名可用性和报告成功或如何修复此错误的说明性 guidence 使用详细的错误。    
7. 在成功地验证**将主机名添加**按钮将变为活动状态并且你将能够分配主机名。 现在导航到你的浏览器中的自定义域名。 你现在应该看到你使用自定义域名的应用程序运行。 
   
   配置完成后，自定义域名将会列在**域名**的 web 应用程序的部分。

此时，你应能够在你的浏览器中输入 Traffic Manager 域名名称并查看，它已成功将你带到你的 web 应用。

