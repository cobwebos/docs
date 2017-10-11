.NET 应用程序可以使用**StackExchange.Redis**缓存客户端，可以使用 NuGet 包，以简化缓存客户端应用程序配置的 Visual Studio 中配置。 

> [!NOTE]
> 有关详细信息，请参阅[StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) github 页面和[StackExchange.Redis 缓存客户端文档](http://github.com/StackExchange/StackExchange.Redis#documentation)。
> 
> 

若要使用 StackExchange.Redis NuGet 包的 Visual Studio 中配置客户端应用程序，右键单击中的项目**解决方案资源管理器**选择**管理 NuGet 包**。 

![管理 NuGet 包](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

类型**StackExchange.Redis**或**StackExchange.Redis.StrongName**在搜索文本框中，从结果中，选择所需的版本，然后单击**安装**。

> [!NOTE]
> 如果想要使用的具有强名称版本**StackExchange.Redis**客户端库，选择**StackExchange.Redis.StrongName**; 否则选择**StackExchange.Redis**。
> 
> 

![StackExchange.Redis NuGet 包](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet 程序包下载，并添加客户端应用程序，以使用 StackExchange.Redis 缓存客户端访问 Azure Redis 缓存所需的程序集引用。

> [!NOTE]
> 如果你之前配置项目以使用 StackExchange.Redis，你可以检查更新从包**NuGet 包管理器**。 若要检查和安装更新的版本的 StackExchange.Redis NuGet 包，请单击**更新**中**NuGet 包管理器**窗口。 如果对 StackExchange.Redis NuGet 包的更新可用，你可以更新项目以使用更新的版本。
> 
> 

你还可以通过单击安装 StackExchange.Redis NuGet 包**NuGet 包管理器**，**程序包管理器控制台**从**工具**菜单，然后运行以下命令从**程序包管理器控制台**窗口。
    
```
Install-Package StackExchange.Redis
```
