### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-powershell"></a>安装 PowerShell
安装最新的 PowerShell（如果未在计算机上安装）。 

1. 在 Web 浏览器中导航到 [Azure 下载](https://azure.microsoft.com/downloads/)页。 
2. 在“命令行工具” -> “PowerShell”部分单击“Windows 安装”。 
3. 若要安装 PowerShell，请运行 **MSI** 文件。 

有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps)。 

#### <a name="log-in-to-powershell"></a>登录到 PowerShell

1. 在计算机上启动 **PowerShell**。 在完成本快速入门之前，请将 PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行这些命令。

    ![启动 PowerShell](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. 运行以下命令，并输入用于登录 Azure 门户的同一 Azure 用户名和密码：
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 运行以下命令查看此帐户的所有订阅：

    ```powershell
    Get-AzureRmSubscription
    ```
3. 如果看到多个订阅与帐户相关联，请运行以下命令，选择要使用的订阅。 请将 **SubscriptionId** 替换为自己的 Azure 订阅的 ID：

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
