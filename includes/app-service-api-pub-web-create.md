<!-- not suitable for Mooncake -->
1. 在“解决方案资源管理器”中，右键单击项目（不是解决方案）并单击“发布”。 

	![项目发布菜单选项](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. 单击“配置文件”选项卡，然后单击“Microsoft Azure API Apps (预览版)”。

	![“发布 Web”对话框](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. 单击“新建”，在你的 Azure 订阅中预配新的 API 应用。

	![“选择现有的 API 服务”对话框](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. 在“创建 API 应用”对话框中输入以下内容：

	- 对于“API 应用名称”，请输入你在本教程中使用的名称。 
	- 如果你有多个 Azure 订阅，请选择想要使用的订阅。
	- 对于“App Service 计划”，请从现有的 App Service 计划中选择，或者选择“创建新的 App Service 计划”并输入新计划的名称。 
	- 对于“资源组”，请从现有的资源组中选择，或者选择“创建新资源组”并输入名称。 
	- 对于“访问级别”，请选择“适用于任何人”。以后可以通过 Azure 预览门户限制访问权限。
	- 对于“区域”，请选择离你最近的区域。  

	![“配置 Microsoft Azure 网站”对话框](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. 单击“确定”在你的订阅中创建 API 应用。

	由于此过程可能需要几分钟时间，Visual Studio 将显示一个确认对话框。

6. 在确认对话框中单击“确定”。
 
	预配过程将在你的 Azure 订阅中创建资源组和 API 应用。Visual Studio 将在“Azure 网站活动”窗口中显示进度。

	![通过“Azure 网站活动”窗口显示的状态通知](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=Mooncake_0118_2016-->