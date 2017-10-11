
## <a name="start-your-powershell-session"></a>启动 PowerShell 会话
首先你需要安装最新[Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx)安装并正在运行。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

> [!NOTE]
> 在此主题使用示例[Azure 资源管理器部署模型](../articles/azure-resource-manager/resource-group-overview.md)，因此示例使用[Azure 资源管理器 cmdlet](http://msdn.microsoft.com/library/azure/mt125356.aspx)。 
> 
> 

运行[**添加 AzureRmAccount** ](http://msdn.microsoft.com/library/mt619267.aspx) cmdlet，并将显示登录屏幕以输入你的凭据。 使用你用于登录到 Azure 门户的相同凭据。

    Add-AzureRmAccount

如果你有多个订阅使用[**集 AzureRmContext** ](http://msdn.microsoft.com/library/mt619263.aspx) cmdlet 来选择你的 PowerShell 会话应使用的订阅。 若要查看哪些订阅的当前 PowerShell 会话中使用的，运行[ **Get AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx)。 若要查看所有订阅，请运行[ **Get-azurermsubscription**](http://msdn.microsoft.com/library/mt619284.aspx)。

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

