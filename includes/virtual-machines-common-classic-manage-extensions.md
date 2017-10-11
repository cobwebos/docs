


## <a name="using-vm-extensions"></a>使用 VM 扩展
Azure VM 扩展实现行为，或者可以帮助其他程序在 Azure Vm 上工作的功能 (例如， **WebDeployForVSDevTest**扩展允许 Visual Studio 向 Azure VM 上的 Web Deploy 解决方案) 或提供您以与 VM 交互的功能，以支持某种其他行为 （例如，你可以使用 VM 访问扩展从 PowerShell、 Azure CLI和 REST 客户端，重置或修改 Azure VM 上的远程访问值)。

> [!IMPORTANT]
> 有关这些扩展按它们支持的功能的完整列表，请参阅[Azure VM 扩展和功能](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 由于每个 VM 扩展支持特定功能，因此完全什么可以并不能执行扩展名取决于该扩展。 因此，在修改之前你的 VM，请确保你已阅读你想要使用的 VM 扩展的文档。 不支持删除某些 VM 扩展;其他人可以设置从根本上更改 VM 行为的属性。
> 
> 

最常见的任务包括：

1. 查找可用扩展
2. 更新加载的扩展
3. 添加扩展
4. 删除扩展

## <a name="find-available-extensions"></a>查找可用扩展
你可以找到扩展和扩展的信息使用：

* PowerShell
* Azure 跨平台命令行界面 (Azure CLI)
* 服务管理 REST API

### <a name="azure-powershell"></a>Azure PowerShell
某些扩展插件具有特定于它们，这可能会使其配置从 PowerShell 更容易; 的 PowerShell cmdlet但是，以下 cmdlet 适用于所有 VM 扩展。

可以使用以下 cmdlet 来获取有关可用扩展的信息：

* 为 web 角色或辅助角色的实例，可以使用[Get-azureserviceavailableextension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet。
* 为虚拟机的实例，你可以使用[Get-azurevmavailableextension](https://msdn.microsoft.com/library/azure/dn722480.aspx) cmdlet。
  
   例如，下面的代码示例显示如何列出的信息**IaaSDiagnostics**使用 PowerShell 的扩展。
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Azure 命令行界面 (Azure CLI)
某些扩展插件具有特定于它们的 Azure CLI 命令 （Docker VM 扩展是一个例子），这可能使其配置更容易;但是，以下命令适用于所有 VM 扩展。

你可以使用**azure vm 扩展列表**命令以获取有关可用扩展的信息并使用**–-json**选项以显示有关一个或多个扩展的所有可用信息。 如果不使用扩展名称，该命令将返回所有可用扩展的 JSON 说明。

例如，下面的代码示例显示如何列出的信息**IaaSDiagnostics**扩展使用 Azure CLI **azure vm 扩展列表**命令并使用**–-json**选项返回完整信息。

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>服务管理 REST Api
以下 REST Api 可用于获取有关可用扩展的信息：

* 为 web 角色或辅助角色的实例，可以使用[列出可用扩展](https://msdn.microsoft.com/library/dn169559.aspx)操作。 若要列出可用扩展的版本，可以使用[列出扩展版本](https://msdn.microsoft.com/library/dn495437.aspx)。
* 为虚拟机的实例，你可以使用[列出资源扩展](https://msdn.microsoft.com/library/dn495441.aspx)操作。 若要列出可用扩展的版本，可以使用[列出资源扩展版本](https://msdn.microsoft.com/library/dn495440.aspx)。

## <a name="add-update-or-disable-extensions"></a>添加、 更新或禁用扩展
创建一个实例，或它们都可添加到正在运行的实例时，可以添加扩展。 可以更新、 禁用或移除扩展。 通过使用 Azure PowerShell cmdlet 或使用服务管理 REST API 操作，你可以执行这些操作。 需要参数来安装和设置某些扩展。 扩展支持公共和私有参数。

### <a name="azure-powershell"></a>Azure PowerShell
使用 Azure PowerShell cmdlet 是添加和更新扩展的最简单方法。 当使用扩展 cmdlet 时，是为你完成大部分扩展配置。 有时，你可能需要以编程方式添加扩展。 当你需要执行此操作时，你必须提供扩展的配置。

你可以使用以下 cmdlet 来了解扩展是否需要配置的公共和私有参数：

* 为 web 角色或辅助角色的实例，可以使用**Get-azureserviceavailableextension** cmdlet。
* 为虚拟机的实例，你可以使用**Get-azurevmavailableextension** cmdlet。

### <a name="service-management-rest-apis"></a>服务管理 REST Api
当使用 REST Api 检索可用扩展的列表时，你将收到关于如何扩展的配置信息。 返回的信息可能会显示由公共架构和私有架构表示的参数信息。 有关实例的查询中返回公共参数值。 不会返回私有参数值。

你可以使用以下 REST Api 来了解扩展是否需要配置的公共和私有参数：

* 为 web 角色或辅助角色的实例**PublicConfigurationSchema**和**PrivateConfigurationSchema**元素包含从响应中的信息[列出可用扩展](https://msdn.microsoft.com/library/dn169559.aspx)操作。
* 为虚拟机的实例**PublicConfigurationSchema**和**PrivateConfigurationSchema**元素包含从响应中的信息[列出资源扩展](https://msdn.microsoft.com/library/dn495441.aspx)操作。

> [!NOTE]
> 扩展也可以使用 JSON 定义的配置。 这些类型的扩展使用时，仅**SampleConfig**使用元素。
> 
> 

