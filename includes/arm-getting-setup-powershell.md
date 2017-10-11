## <a name="setting-up-powershell-for-resource-manager-templates"></a>为资源管理器模板设置 PowerShell
你可以使用 Azure PowerShell 与资源管理器之前，你将需要使其具有正确的 Windows PowerShell 和 Azure PowerShell 版本。

### <a name="verify-powershell-versions"></a>验证 PowerShell 版本
验证你有 Windows PowerShell 版本 3.0 或 4.0。 若要查找的 Windows PowerShell 的版本，请在 Windows PowerShell 命令提示符下键入以下命令。

    $PSVersionTable

你将收到以下类型的信息：

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


验证的值**PSVersion**为 3.0 或 4.0。 如果没有，请参阅[Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)或[Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)。

### <a name="set-your-azure-account-and-subscription"></a>设置你的 Azure 帐户和订阅
如果你还没有 Azure 订阅，则可以激活你[MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

使用此命令打开 Azure PowerShell 命令提示符，然后登录到 Azure。

    Login-AzureRmAccount

如果你有多个 Azure 订阅，则可以列出你使用此命令的 Azure 订阅。

    Get-AzureRmSubscription

你将收到以下类型的信息：

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

可以通过在 Azure PowerShell 命令提示符运行以下命令来设置当前 Azure 订阅。 将引号，包括内的所有内容 < 和 > 字符，替换为正确的名称。

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

有关 Azure 订阅和帐户的详细信息，请参阅[如何： 连接到你的订阅](/powershell/azureps-cmdlets-docs#step-3-connect)。

