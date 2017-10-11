### <a name="troubleshoot-azure-diagnostics"></a>对 Azure 诊断进行故障排除

如果你收到以下错误消息，使用 Microsoft.insights 资源提供程序未注册：

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

若要注册资源提供程序，请在 Azure 门户中执行以下步骤：

1.  在左侧导航窗格中，单击*订阅*
2.  选择标识错误消息中的订阅
3.  单击*资源提供程序*
4.  查找*Microsoft.insights*提供程序
5.  单击*注册*链接

![注册使用 microsoft.insights 资源提供程序](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

一次*Microsoft.insights*注册资源提供程序，重试配置诊断。


在 PowerShell 中，如果你收到以下错误消息，你需要更新你的 PowerShell 版本：

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

到 2016 年 11 月 (v2.3.0)，更新你的 PowerShell 版本或更高版本，释放使用中的说明[要开始使用 Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)文章。
