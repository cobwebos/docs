[用于.NET 的 Microsoft Azure 配置管理器库](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)提供类，用于分析从配置文件的连接字符串。 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx)类分析而不考虑是否将客户端应用程序运行在桌面上，在移动设备上，在 Azure 虚拟机中，或在 Azure 云服务中的配置设置。

若要引用 CloudConfigurationManager 包，添加以下`using`指令：

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

下面是演示如何从配置文件中检索连接字符串示例：

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

使用 Azure 配置管理器是可选的。 你还可以使用像.NET Framework 的 API [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)类。

