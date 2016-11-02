
## 启动 PowerShell 会话

首先，需要安装并运行最新的 [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx)。有关详细信息，请参阅[如何安装和配置 Azure PowerShell](../articles/powershell-install-configure.md)。


>[AZURE.NOTE] SQL 数据库的许多新功能仅在使用 [Azure Resource Manager 部署模型](../articles/resource-group-overview.md)时才受支持，因此示例使用适用于 Resource Manager 的 [Azure SQL 数据库 PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)。向后兼容支持现有的经典部署模型 [Azure SQL 数据库（经典）cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx)，但建议使用 Resource Manager cmdlet。


运行 [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet，随后显示要求输入凭据的登录屏幕。使用与登录 Azure 门户相同的凭据。

	Add-AzureRmAccount

如果你有多个订阅，请使用 [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) cmdlet 选择你的 PowerShell 会话应使用的订阅。若要查看当前 PowerShell 会话正在使用哪个订阅，请运行 [**Get AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx)。若要查看所有订阅，请运行 [**Get AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx)。

	Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

<!---HONumber=AcomDC_0921_2016-->