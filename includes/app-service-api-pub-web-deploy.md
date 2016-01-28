<!-- not suitable for Mooncake -->

7. 在“解决方案资源管理器”中右键单击 API 应用项目，然后选择“发布”打开发布对话框。应预先选择前面创建的发布配置文件。 

9. 单击“发布”开始部署过程。

	![部署 API 应用](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

	“Azure 网站活动”窗口将显示部署进度。

	![“Azure 网站活动”窗口中的状态通知](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

	在此部署过程中，Visual Studio 将自动尝试重新启动 *网关* 。网关是一个网站，它可以处理资源组中所有 API 应用的管理功能，只有将它重新启动才能识别 API 应用的 API 定义或 *apiapp.json* 文件中的更改。
 
	如果你使用其他方法部署 API 应用或者 Visual Studio 无法重新启动网关，你可能需要手动重新启动网关。以下步骤说明了如何执行该操作。

1. 在浏览器中，转到 [Azure 预览门户](https://manage.windowsazure.cn)。

2. 导航到你部署的 API 应用的“API 应用”边栏选项卡。

	有关“API 应用”边栏选项卡以及如何找到它的信息，请参阅[管理 API 应用](/documentation/articles/app-service-api-manage-in-portal)。

4. 单击“网关”链接。

3. 在“网关”边栏选项卡中，单击“重新启动”。

	![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=Mooncake_0118_2016-->