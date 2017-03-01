### <a name="troubleshoot-azure-diagnostics"></a>排查 Azure 诊断问题

如果收到以下错误消息，说明未注册 Microsoft.insights 资源提供程序：

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

若要注册资源提供程序，请在 Azure 门户中执行以下步骤：

1.    在左侧导航窗格中，单击“订阅”
2.    选择在错误消息中标识的订阅
3.    单击“资源提供程序”
4.    找到 *Microsoft.insights* 提供程序
5.    单击“注册”链接

![注册 microsoft.insights 资源提供程序](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

注册 *Microsoft.insights* 资源提供程序以后，可重试配置诊断。


<!--HONumber=Feb17_HO2-->


