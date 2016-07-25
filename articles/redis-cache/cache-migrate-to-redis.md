<properties 
    pageTitle="缓存迁移到 Redis | Azure"
    description="了解如何将托管缓存服务应用程序迁移到 Azure Redis 缓存"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags
	ms.service="cache"
	ms.date="06/09/2016"
	wacn.date=""/>

# 从托管缓存服务迁移到 Azure Redis 缓存

在将使用 Azure 托管缓存服务的应用程序迁移到 Azure Redis 缓存时，你只需对应用程序略做更改，具体情况取决于缓存应用程序所使用的托管缓存服务功能。API 虽非完全相同，但却极为类似，而且使用托管缓存服务来访问缓存的多数现有代码，只需略做更改即可重复使用。本主题说明如何对配置和应用程序进行必要的更改，以将托管缓存服务应用程序迁移为使用 Azure Redis 缓存，并说明如何使用 Azure Redis 缓存的某些功能，来实现托管缓存服务缓存的功能。

## 迁移步骤

若要将托管缓存服务应用程序迁移为使用 Azure Redis 缓存，需执行以下步骤。

-	将托管缓存服务功能映射到 Azure Redis 缓存
-	选择缓存产品/服务
-	创建缓存
-	配置缓存客户端
	-	删除托管缓存服务配置
	-	使用 StackExchange.Redis NuGet 包配置缓存客户端
-	迁移托管缓存服务代码
	-	使用 ConnectionMultiplexer 类连接到缓存
	-	访问缓存中的基元数据类型
	-	处理缓存中的 .NET 对象
-	将 ASP.NET 会话状态和输出缓存迁移到 Azure Redis 缓存

## 将托管缓存服务功能映射到 Azure Redis 缓存

Azure 托管缓存服务与 Azure Redis 缓存类似，但两者在实现某些功能时会使用不同的方式。本节描述其中的一些差异，并针对如何在 Azure Redis 缓存中实现托管缓存服务功能提供指导。

|托管缓存服务功能|托管缓存服务支持|Azure Redis 缓存支持|
|---|---|---|
|命名缓存|系统会配置默认缓存，而在标准和高级缓存产品/服务中，还可以根据需要额外配置多达 9 个命名缓存。|Azure Redis 缓存具有可用于对命名缓存实现类似功能的、数目可配置的数据库（默认值为 16 个）。有关详细信息，请参阅[默认 Redis 服务器配置](/documentation/articles/cache-configure/#default-redis-server-configuration)。|
|高可用性|在标准和高级缓存产品/服务中，为缓存中的项提供高可用性。如果因为故障而导致项丢失，缓存中的项仍有备份副本可供使用。辅助缓存的写入操作以同步方式进行。|标准和高级缓存产品/服务提供高可用性，它们具有双节点的主要/副本配置（高级缓存的每个分片都有主要/副本对）。副本的写入操作以异步方式进行。有关详细信息，请参阅 [Azure Redis 缓存定价](/home/features/redis-cache/pricing/)。| 
|通知|当命名缓存上发生各种缓存操作时，允许客户端接收异步通知。|客户端应用程序可以使用 Redis pub/sub 或[密钥空间通知](/documentation/articles/cache-configure/#keyspace-notifications-advanced-settings)来实现与通知类似的功能。|
|本地缓存|在客户端本地存储缓存对象的副本，以实现超快访问。|客户端应用程序需使用字典或类似的数据结构来实现此功能。|
|逐出策略|无或 LRU。默认策略是 LRU。|Azure Redis 缓存支持以下逐出策略：volatile-lru、allkeys-lru、volatile-random、allkeys-random、volatile-ttl、noeviction。默认策略是 volatile-lru。有关详细信息，请参阅[默认 Redis 服务器配置](/documentation/articles/cache-configure/#default-redis-server-configuration)。|
|过期策略|默认过期策略为“绝对”，默认过期间隔为 10 分钟。另外也提供“滑动”和“永不”策略。|默认情况下，缓存中的项不会过期，但可以使用缓存集重载，对每次写入配置过期时间。有关详细信息，请参阅[添加和从缓存检索对象](/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#add-and-retrieve-objects-from-the-cache)。|
|区域和标记|区域是缓存项的子组。区域也支持使用称为标记的额外描述性字符串为缓存项添加批注。区域支持对该区域内的任何标记项执行搜索操作的能力。区域内的所有项全部位于缓存群集的单个节点内。|Redis 缓存由单个节点组成（除非已启用 Redis 群集），因此托管缓存服务区域的概念不适用。Redis 支持在检索键时执行搜索和通配符操作，让描述性标记可以嵌入键名称内并在后面用于检索项。有关使用 Redis 实现标记解决方案的示例，请参阅[使用 Redis 实现缓存标记](http://stackify.com/implementing-cache-tagging-redis/)。|
|序列化|托管缓存支持 NetDataContractSerializer 和 BinaryFormatter，也支持使用自定义序列化程序。默认值为 NetDataContractSerializer。|由客户端应用程序负责先将 .NET 对象序列化，再将它们放入缓存中，至于要选择使用哪个序列化程序则由客户端应用程序开发人员决定。有关详细信息和示例代码，请参阅[处理缓存中的 .NET 对象](/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#work-with-net-objects-in-the-cache)。|
| 缓存模拟器 | 托管缓存提供本地缓存模拟器。 | Azure Redis 缓存没有模拟器，但你可以通过[在本地运行 redis-server.exe 的 MSOpenTech 生成](/documentation/articles/cache-faq/#cache-emulator)来提供模拟器体验。 |

## 选择缓存产品/服务

Azure Redis 缓存提供以下层：

-	**基本** - 单个节点。多种大小，最大 53 GB。
-	**标准** - 双节点主/副本配置。多种大小，最大 53 GB。99.9% SLA。
-	**高级** - 双节点主/副本配置，最多有 10 个分片。从 6 GB 到 530 GB 的多种大小（有关详细信息，请与我们联系）。标准层的所有功能加上其他功能，支持 [Redis 群集](/documentation/articles/cache-how-to-premium-clustering/)、[Redis 持久性](/documentation/articles/cache-how-to-premium-persistence/)和 [Azure 虚拟网络](/documentation/articles/cache-how-to-premium-vnet/)。99.9% SLA。

每个级别在功能和定价方面存在差异。在本指南的后面将介绍这些功能，而有关定价的详细信息，则请参阅[缓存定价详细信息](/home/features/redis-cache/pricing/)。

迁移操作的第一步是选择与以前的托管缓存服务缓存大小匹配的大小，然后根据应用程序的需求进行增减。有关如何选择合适 Azure Redis 缓存产品/服务的详细指导，请参阅[我应使用哪种 Redis 缓存产品/服务和大小？](/documentation/articles/cache-faq/#what-redis-cache-offering-and-size-should-i-use)。

## 创建缓存

[AZURE.INCLUDE [redis-cache-create](../includes/redis-cache-create.md)]

## 配置缓存客户端

创建并配置缓存后，接下来就是删除托管缓存服务配置，并添加 Azure Redis 缓存配置和引用，让缓存客户端可以访问缓存。

-	删除托管缓存服务配置
-	使用 StackExchange.Redis NuGet 包配置缓存客户端

### 删除托管缓存服务配置

若要将客户端应用程序配置为使用 Azure Redis 缓存，必须先卸载托管缓存服务 NuGet 包，以便删除现有托管缓存服务的配置和组件引用。

若要卸载托管缓存服务 NuGet 包，请在“解决方案资源管理器”中右键单击客户端项目，然后选择“管理 NuGet 程序包”。选择“已安装的包”节点，然后在“搜索已安装的包”框中键入 **WindowsAzure.Caching**。选择“Microsoft Azure Cache”（或“Microsoft Azure Caching”，视 NuGet 包的版本而定），单击“卸载”，然后单击“关闭”。

![卸载 Azure 托管缓存服务 NuGet 包](./media/cache-migrate-to-redis/IC757666.jpg)

卸载托管缓存服务 NuGet 包时，会删除客户端应用程序的 app.config 或 web.config 中的托管缓存服务组件和托管缓存服务条目。卸载 NuGet 包时可能不会删除部分自定义设置，因此请打开 web.config 或 app.config，确保已彻底删除以下元素。

确保已从 `configSections` 元素删除 `dataCacheClients` 条目。请勿删除整个 `configSections` 元素，只需删除 `dataCacheClients` 条目（如果存在）。

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

确保已删除 `dataCacheClients` 节。`dataCacheClients` 节将类似于以下示例。

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

一旦删除托管缓存服务配置，就可以如下一节所述配置缓存客户端。

### 使用 StackExchange.Redis NuGet 包配置缓存客户端

[AZURE.INCLUDE [redis-cache-configure](../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## 迁移托管缓存服务代码

StackExchange.Redis 缓存客户端的 API 与托管缓存服务类似。本节将概述两者的差异。

### 使用 ConnectionMultiplexer 类连接到缓存

在托管缓存服务中，缓存连接由 `DataCacheFactory` 和 `DataCache` 类负责处理。在 Azure Redis 缓存中，这些连接则由 `ConnectionMultiplexer` 类进行管理。

将以下 using 语句添加到要从中访问缓存的任何文件的顶部。

	using StackExchange.Redis
								
如果此命名空间并未解析，请确保你已如[配置缓存客户端](/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#configure-the-cache-clients)中所述添加 StackExchange.Redis NuGet 包。

>[AZURE.NOTE] 请注意，StackExchange.Redis 客户端需要 .NET Framework 4 或更高版本。

若要连接到 Azure Redis 缓存实例，请调用静态 `ConnectionMultiplexer.Connect` 方法并传入终结点和密钥。共享应用程序中的 `ConnectionMultiplexer` 实例的一个方法是，拥有返回连接示例的静态属性（与下列示例类似）。这种线程安全方法，可仅初始化单一连接的 `ConnectionMultiplexer` 实例。在此示例中，`abortConnect` 设置为 false，这表示即使未建立缓存连接，也可成功调用。`ConnectionMultiplexer` 的一个关键功能是，一旦还原网络问题和其他原因，它将自动还原缓存连接。

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5.redis.cache.chinacloudapi.cn,abortConnect=false,ssl=true,password=...");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

可以从缓存实例的“Redis 缓存”边栏选项卡中获取缓存终结点、密钥和端口。有关详细信息，请参阅 [Redis 缓存属性](/documentation/articles/cache-configure/#properties)。

建立连接后，通过调用 `ConnectionMultiplexer.GetDatabase` 方法返回对 Redis 缓存数据库的引用。从 `GetDatabase` 方法返回的对象是一个轻型直通对象，不需要存储。

	IDatabase cache = Connection.GetDatabase();
	
	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);
	
	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

StackExchange.Redis 客户端使用 `RedisKey` 和 `RedisValue` 类型在缓存中访问和存储项。这些类型可映射到最基本的语言类型（包括字符串），但通常不直接使用。Redis 字符串是最基本的一种 Redis 值，可包含许多类型的数据（包括序列化的二进制数据流），你可能不会直接使用此类型，但你会使用在名称中包含 `String` 的方法。对于最基本的数据类型，你会使用 `StringSet` 和 `StringGet` 方法在缓存中存储和检索项，除非你要在缓存中存储集合或其他 Redis 数据类型。

`StringSet` 和 `StringGet` 非常类似于托管缓存服务的 `Put` 和 `Get` 方法，其中最主要的差异在于，若要设置 .NET 对象并将其放到缓存中，必须先将其序列化。

调用 `StringGet` 时，如果该对象存在，则返回它，如果该对象不存在，则返回 null。在这种情况可以从所需的数据源检索值，并将其存储在缓存中供后续使用。这称为缓存端模式。

要在缓存中指定项的过期时间，请使用 `StringSet` 的 `TimeSpan` 参数。

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Azure Redis 缓存可以处理 .NET 对象以及基元数据类型，但在缓存 .NET 对象之前，必须先将其序列化。这是应用程序开发人员的责任。这可让开发人员灵活地选择序列化程序。有关详细信息和示例代码，请参阅[处理缓存中的 .NET 对象](/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#work-with-net-objects-in-the-cache)。

## 将 ASP.NET 会话状态和输出缓存迁移到 Azure Redis 缓存

Azure Redis 缓存有适用于 ASP.NET 会话状态和页面输出缓存的提供程序。若要迁移使用这些提供程序的托管缓存服务版本的应用程序，请先从 web.config 中删除现有节，然后配置这些提供程序的 Azure Redis 缓存版本。有关使用 Azure Redis 缓存 ASP.NET 提供程序的说明，请参阅 [Azure Redis 缓存的 ASP.NET 会话状态提供程序](/documentation/articles/cache-aspnet-session-state-provider/)和 [Azure Redis 缓存的 ASP.NET 输出缓存提供程序](/documentation/articles/cache-aspnet-output-cache-provider/)。

## 后续步骤

浏览 [Azure Redis 缓存文档](/documentation/services/redis-cache/)中的教程、示例、视频及其他信息。


<!---HONumber=Mooncake_0718_2016-->