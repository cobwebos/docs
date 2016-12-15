---
title: "如何使用 Azure 托管缓存服务"
description: "了解如何使用 Azure 托管缓存服务提高 Azure 应用程序的性能"
services: cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 80552353-6682-4b96-a4cc-4e975a25980c
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/15/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feeeaef2bd5e5ff665ca870b61036365d75f53d


---
# <a name="how-to-use-azure-managed-cache-service"></a>如何使用 Azure 托管缓存服务
本指南演示如何开始使用 **Azure 托管缓存服务**。 相关示例用 C\# 代码编写且使用 .NET API。 涉及的应用场景包括**创建和配置缓存**、**配置缓存客户端**、 **在缓存中添加和删除对象、在缓存中存储 ASP.NET 会话状态**以及**使用缓存启用 ASP.NET 页面输出缓存**。 有关使用 Azure 缓存的详细信息，请参阅[后续步骤][后续步骤]部分。

> [!IMPORTANT]
> 按照去年的 [公告](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)，将于 2016 年 11 月 30 日停用 Azure 托管缓存服务和 Azure 角色中缓存服务。 我们建议使用 [Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。 有关迁移的信息，请参阅 [从托管缓存服务迁移到 Azure Redis 缓存](../redis-cache/cache-migrate-to-redis.md)。
> 
> 

<a name="what-is"></a>

## <a name="what-is-azure-managed-cache-service"></a>什么是 Azure 托管缓存服务？
Azure 托管缓存服务是内存中的可缩放分布式解决方案，利用该解决方案，可通过对数据进行超高速访问来生成高度可缩放且高度可响应的应用程序。

Azure 托管缓存服务包含以下功能：

* 为会话状态和页面输出缓存预生成 ASP.NET 提供程序，这样无需修改应用程序代码即可启用 Web 应用程序加速。
* 缓存任何可序列化的托管对象 - 例如：CLR 对象、行、XML、二进制数据。
* 跨 Azure 和 Windows Server AppFabric 实现一致的开发模型。

托管缓存服务提供对 Microsoft 所管理安全专用缓存的访问权限。 使用托管缓存服务创建的缓存可从在 Azure 网站、Web 角色和辅助角色以及虚拟机中运行的 Azure 内的应用程序进行访问。

托管缓存服务分三个级别提供：

* 基本 – 大小从 128 MB 到 1 GB 的缓存
* 标准 – 大小从 1 GB 到 10 GB 的缓存
* 高级 – 大小从 5 GB 到 150 GB 的缓存

每个级别在功能和定价方面存在差异。 本指南稍后将介绍这些功能，而有关定价的详细信息，请参阅[缓存定价详细信息][缓存定价详细信息]。

本指南提供了托管缓存服务的入门概述。 有关本入门指南未提及功能的更多详细信息，请参阅 [Azure 托管缓存服务概述][Azure 托管缓存服务概述]。

<a name="getting-started-cache-service"></a>

## <a name="getting-started-with-cache-service"></a>缓存服务入门
托管缓存服务入门相当容易。 若要开始使用，需要首先设置和配置缓存。 接下来，配置缓存客户端，以便它们可以访问缓存。 在配置了缓存客户端后，就可以开始使用它们。

* [创建缓存][创建缓存]
* [配置缓存][配置缓存]
* [配置缓存客户端][配置缓存客户端]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>创建缓存
托管缓存服务中的缓存是使用 PowerShell cmdlet 创建的。 

> 使用 PowerShell cmdlet 创建托管缓存服务实例后，可在 [Azure 经典门户][Azure 经典门户]中查看和配置该实例。
> 
> 

若要创建托管缓存服务实例，请打开 Azure PowerShell 命令窗口。

> 有关安装和使用 Azure PowerShell 的说明，请参阅[如何安装和配置 Azure PowerShell][如何安装和配置 Azure PowerShell]。
> 
> 

调用 [Add-AzureAccount][Add-AzureAccount] cmdlet，然后输入与帐户关联的电子邮件地址和密码。 默认选择订阅，并在调用 [Add-AzureAccount][Add-AzureAccount] cmdlet 后显示订阅。 若要更改订阅，请调用 [Select-AzureSubscription][Select-AzureSubscription] cmdlet。

> 如果你使用你的帐户证书配置了 Azure PowerShell，则可以跳过此步骤。 有关使用 Azure 帐户连接 Azure PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell][如何安装和配置 Azure PowerShell]。
> 
> 

默认选择并显示订阅。 若要更改订阅，请调用 [Select-AzureSubscription][Select-AzureSubscription] cmdlet。

调用 [New-AzureManagedCache][New-AzureManagedCache] cmdlet 并指定名称、区域、缓存产品/服务和缓存大小。

对于“名称”，请输入要用于缓存终结点的子域名。 该终结点必须是长度在 6 到 20 个字符之间的字符串，仅包含小写的数字和字母，并且必须以字母开头。

对于“位置”，请指定缓存的区域。 最佳做法是在与缓存客户端应用程序相同的区域中创建缓存。

“SKU”和“内存”协同工作来确定缓存大小。 托管缓存服务按以下三个级别提供。

* 基本 - 缓存大小在 128MB 到 1GB 之间，以 128MB 为增量，具有一个默认命名缓存
* 标准 - 缓存大小在 1GB 到 10GB 之间，以 1GB 为增量，支持通知以及最多 10 个命名缓存
* 高级 - 缓存大小在 5GB 到 150GB 之间，以 5GB 为增量，支持通知、高可用性以及最多 10 个命名缓存

选择满足应用程序需求的“SKU”和“内存”。 请注意，某些缓存功能（例如通知和高可用性）仅可用于某些缓存产品。 有关选择最适合应用程序的缓存产品/服务和大小的详细信息，请参阅[缓存产品/服务][缓存产品]。

 在以下示例中，基本版 128MB 缓存是使用名称 contosocache 在“美国中南部”地理区域中创建的。

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> 有关创建缓存时可供使用的参数和值的完整列表，请参阅 [New-AzureManagedCache][New-AzureManagedCache] cmdlet 文档。
> 
> 

调用 PowerShell cmdlet 后，创建缓存可能需要数分钟时间。 创建缓存后，新缓存的状态为 `Running`，它采用默认设置且随时可供使用，你可在 [Azure 经典门户][Azure 经典门户]中查看和配置该缓存。 若要自定义缓存配置，请参阅以下[配置缓存][配置缓存]部分。

你可以在 Azure PowerShell 窗口中监视创建进度。 缓存可供使用后，[New-AzureManagedCache][New-AzureManagedCache] cmdlet 将显示缓存信息，如下例中所示。

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>




<a name="enable-caching"></a>

## <a name="configure-the-cache"></a>配置缓存
在 Azure 经典门户中，用于缓存的“配置”选项卡位于配置缓存选项的位置。 每个缓存都具有**默认**命名缓存，并且标准和高级缓存产品/服务支持最多 9 个附加的命名缓存，总共 10 个。 每个命名缓存都具有自己的一组选项，可用于以高度灵活的方式配置你的缓存。

![命名缓存][NamedCaches]

若要创建命名缓存，请在“名称”框中键入新缓存的名称，指定所需选项，单击“保存”，然后单击“是”进行确认。 若要取消任何更改，请单击“放弃”。

## <a name="expiry-policy-and-time-min"></a>有效期策略和时间（分钟）
“过期策略”与“时间(分钟)”设置共同确定缓存项目的过期时间。 有三种过期策略：“绝对”、“可调”和“从不”。 

如果指定了“绝对”**Absolute**，则项目添加到缓存中时，由“时间(分钟)”指定的过期间隔便开始计时。 “时间(分钟)”指定的时间间隔过去后，该项过期。 

如果指定了“可调”，则每次访问缓存中的项目时，“时间(分钟)”所指定过期间隔将被重置。 直到最近访问项后经过了“时间(分钟)”所指定时间间隔，该项才过期。

如果指定“从不”，“时间(分钟)”必须设置为“0”，项目才不会过期。

过期策略默认设置为“绝对”，“时间(分钟)”默认设置为 10 分钟。 过期策略对命名缓存中的每个项是固定的，但可通过使用获取 timeout 参数的 **Add** 和 **Put** 重载来自定义每个项的“时间(分钟)”。

有关逐出和过期策略的详细信息，请参阅[过期和逐出][过期和逐出]。

## <a name="notifications"></a>通知
允许你的应用程序在缓存群集上发生多种不同的缓存操作时接收异步通知的缓存通知。 缓存通知还提供本地缓存的对象的自动失效。 有关详细信息，请参阅[通知][通知]。

> 仅在标准和高级缓存产品中提供通知，在基本缓存产品中不提供通知。 有关详细信息，请参阅[缓存产品/服务][缓存产品]。
> 
> 

## <a name="high-availability"></a>高可用性
在启用高可用性后，将对添加到缓存中的每一项生成一个备份副本。 如果该项的主副本发生了意外失败，则备份副本仍可用。

按照定义，高可用性使用量等于每个缓存项所需的内存量乘以二。 在容量规划任务中将考虑此内存影响。 有关详细信息，请参阅[高可用性][高可用性]。

> 仅在高级缓存产品中提供高可用性，在基本或标准缓存产品中不提供高可用性。 有关详细信息，请参阅[缓存产品/服务][缓存产品]。
> 
> 

## <a name="eviction"></a>逐出
为了保持在缓存中提供的内存容量，需支持最近最少使用 (LRU) 逐出。 在内存使用量超出内存阈值时，在内存压力得到缓解之前，将从内存中逐出对象，无论这些对象是否已到期。
默认情况下启用逐出。 如果禁用了逐出，在达到容量时将不会从缓存中逐出项，并且 Put 和 Add 操作将失败。

有关逐出和过期策略的详细信息，请参阅[过期和逐出][过期和逐出]。

在配置缓存后，可以配置缓存客户端以允许访问缓存。

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>配置缓存客户端
使用托管缓存服务创建的缓存可从 Azure 网站、Web 角色、辅助角色和虚拟机中运行的 Azure 应用程序进行访问。 提供了 NuGet 包，它可用于简化缓存客户端应用程序的配置。 

若要使用缓存 NuGet 包配置客户端应用程序，请在“解决方案资源管理器”中右键单击项目，然后选择“管理 NuGet 包”。 

![NuGet 包菜单][NuGetPackageMenu]

在“在线搜索”文本框中键入 **WindowsAzure.Caching**，然后从结果中选择“Microsoft Azure 缓存”  
。 单击“安装”，然后单击“我接受”。

![NuGet 包][NuGetPackage]

NuGet 包可执行多项操作：将所需配置添加到应用程序的 config 文件，并且将添加所需的程序集引用。 对于云服务项目，它还将缓存客户端诊断级别设置添加到云服务的 ServiceConfiguration.cscfg 文件。

> 对于 ASP.NET Web 项目，缓存 NuGet 包还将两个注释掉的节添加到 web.config 中。 第一个节允许会话状态存储在缓存中，第二个节启用 ASP.NET 页面输出缓存。 有关详细信息，请参阅[如何在缓存中存储 ASP.NET 会话状态]和[如何在缓存中存储 ASP.NET 页面输出缓存][如何在缓存中存储 ASP.NET 页面输出缓存]。
> 
> 

NuGet 包将以下配置元素添加到应用程序的 web.config 或 app.config 中。 将 **dataCacheClients** 节和 **cacheDiagnostics** 节添加到 **configSections** 元素之下。 如果 **configSections** 元素不存在，则会创建一个作为 **configuration** 元素的子级。

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />

    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />


这些新节包括对 **dataCacheClients** 元素的引用，该元素也会添加到 **configuration** 元素。

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>


在添加配置后，在新添加的配置中替换以下两项。

1. 将 **[Cache role name or Service Endpoint]** 替换为在 Azure 经典门户仪表板上显示的终结点。
   
    ![终结点][Endpoint]
2. 取消注释 securityProperties 节，将 **[Authentication Key]** 替换为身份验证密钥，可通过单击缓存仪表板中的“管理密钥”，在 Azure 经典门户中找到该身份验证密钥。
   
    ![访问密钥][AccessKeys]

> 必须正确配置这些设置，否则，客户端将无法访问缓存。
> 
> 

对于云服务项目，NuGet 包还将 **ClientDiagnosticLevel** 设置添加到 ServiceConfiguration.cscfg 中缓存客户端角色的 **ConfigurationSettings** 中。 以下示例是 ServiceConfiguration.cscfg 文件中的 **WebRole1** 节，其 **ClientDiagnosticLevel** 为 1，这是默认的 **ClientDiagnosticLevel**。

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> 客户端诊断级别配置为缓存客户端收集的缓存诊断信息的级别。 有关详细信息，请参阅[疑难解答和诊断][疑难解答和诊断]
> 
> 

NuGet 包还添加对以下程序集的引用：

* Microsoft.ApplicationServer.Caching.Client.dll
* Microsoft.ApplicationServer.Caching.Core.dll
* Microsoft.WindowsFabric.Common.dll
* Microsoft.WindowsFabric.Data.Common.dll
* Microsoft.ApplicationServer.Caching.AzureCommon.dll
* Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

如果你的项目是 Web 角色，则还添加以下程序集引用：

* Microsoft.Web.DistributedCache.dll。

在配置了你的客户端项目的缓存后，你可以使用以下各节中介绍的方法来使用你的缓存。

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>使用缓存
本节中的步骤介绍如何使用缓存执行常见任务。

* [如何创建 DataCache 对象][如何创建 DataCache 对象]
* [如何从缓存添加和检索对象][如何在缓存中添加和检索对象]
* [如何在缓存中指定对象的有效期][如何在缓存中指定对象的有效期]
* [如何在缓存中存储 ASP.NET 会话状态][如何在缓存中存储 ASP.NET 会话状态]
* [如何在缓存中存储 ASP.NET 页面输出缓存][如何在缓存中存储 ASP.NET 页面输出缓存]

<a name="create-cache-object"></a>

## <a name="how-to-create-a-datacache-object"></a>如何创建 DataCache 对象
若要以编程方式使用缓存，你需要引用该缓存。 将以下代码添加到要从中使用 Azure Cache 的任何文件的顶部：

    using Microsoft.ApplicationServer.Caching;

> 如果在安装了添加必要引用的缓存 NuGet 包后，Visual Studio 仍不能识别 using 语句中的类型，请确保项目的目标配置文件是 .NET Framework 4 或更高版本，并确保选择一个未指定**客户端配置文件**的配置文件。 有关配置缓存客户端的说明，请参阅[配置缓存客户端][配置缓存客户端]。
> 
> 

创建 **DataCache** 对象有两种方法。 第一种方法是仅创建 **DataCache**，并传入所需缓存的名称。

    DataCache cache = new DataCache("default");

在实例化 **DataCache** 后，你可以使用它来与缓存交互，如以下各部分中所述。

若要使用第二种方法，请在你的应用程序中使用默认构造函数创建新的 **DataCacheFactory** 对象。 这会导致缓存客户端使用配置文件中的设置。 调用新的 **DataCacheFactory** 实例的 **GetDefaultCache** 方法，该方法返回 **DataCache** 对象，或调用 **GetCache** 方法并传入你的缓存的名称。 这些方法返回以后可用于以编程方式访问缓存的 **DataCache** 对象。

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.    

<a name="add-object"></a>

## <a name="how-to-add-and-retrieve-an-object-from-the-cache"></a>如何在缓存中添加和检索对象
若要向缓存中添加项，可以使用 **Add** 或 **Put** 方法。 **Add** 方法将指定的对象添加到缓存中，并按键参数的值进行键控。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

如果缓存中已存在具有相同键的对象，将引发 **DataCacheException** 并显示以下消息：

> ErrorCode:SubStatus: An attempt is being made to create an object with a Key that already exists in the cache. Caching will only accept unique Key values for objects.
> 
> 

若要检索具有特定键的对象，可以使用 **Get** 方法。 如果对象存在，则返回它，如果对象不存在，则返回 null。

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

如果具有指定键的对象不存在，则 **Put** 方法将该对象添加到缓存中，如果该对象存在，则替换它。

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>

## <a name="how-to-specify-the-expiration-of-an-object-in-the-cache"></a>如何在缓存中指定对象的有效期
默认情况下，缓存中的项在放入缓存中 10 分钟后到期。 在 Azure 经典门户中，可在用于缓存的“配置”选项卡上的“时间(分钟)”设置中配置此选项。

![命名缓存][NamedCaches]

有三种**过期策略**：“从不”、“绝对”和“可调”。 这些类型配置如何使用“时间(分钟)”来确定有效期。 默认的“**过期类型**”为“**绝对**”，这意味着在将项放入缓存中时，记录该项有效期的倒计时器即会启动。 在项经过指定的时间后，该项过期。 如果指定了“可调”，则会在每次访问缓存中的项时重置该项的有效期倒计时，并且仅在自上次访问该项后经过所指定时间段后，该项才会过期。 如果指定了“从不”，则“时间(分钟)”必须设置为 **0**，只要项在缓存中存在，就不会过期并会保持有效。

如果所需超时时间间隔长于或短于缓存属性中所配置的间隔，则可在缓存中添加或更新项时，使用可获取 **TimeSpan** 参数的 **Add** 和 **Put** 重载来指定特定持续时间。 在以下示例中，将字符串 **value** 添加到缓存中，按 **item** 进行键控，且超时为 30 分钟。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

若要查看缓存中的项的剩余超时时间间隔，可以使用 **GetCacheItem** 方法来检索 **DataCacheItem** 对象，该对象包含有关缓存中项的信息，其中包括剩余超时时间间隔。

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## <a name="how-to-store-aspnet-session-state-in-the-cache"></a>如何在缓存中存储 ASP.NET 会话状态
用于 Azure Cache 的会话状态提供程序是用于 ASP.NET 应用程序的进程外存储机制。 此提供程序允许你将会话状态存储在 Azure 缓存中而非内存或 SQL Server 数据库中。 若要使用缓存会话状态提供程序，请先配置缓存，然后使用缓存 NuGet 包配置用于缓存的 ASP.NET 应用程序，如[托管缓存服务入门][托管缓存服务入门]中所述。 在安装缓存 NuGet 包时，它会在 web.config 中添加一个包含 ASP.NET 应用程序所需配置的注释掉的节，以使用用于 Azure Cache 的会话状态提供程序。

    <!--Uncomment this section to use Azure Caching for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

> 如果安装缓存 NuGet 包后，web.config 仍不含此注释掉的节，请确保从[安装 NuGet 包管理器][安装 NuGet 包管理器]安装最新的 NuGet 包管理器，然后卸载并重新安装该包。
> 
> 

若要启用用于 Azure Cache 的会话状态提供程序，请取消注释指定的节。 在提供的代码段中指定了默认缓存。 若要使用不同的缓存，请在 **cacheName** 属性中指定所需缓存。

有关使用托管缓存服务会话状态提供程序的详细信息，请参阅[用于 Azure 缓存的会话状态提供程序][用于 Azure 缓存的会话状态提供程序]。

<a name="store-page"></a>

## <a name="how-to-store-aspnet-page-output-caching-in-the-cache"></a>如何在缓存中存储 ASP.NET 页面输出缓存
用于 Azure Cache 的输出缓存提供程序是用于输出缓存数据的进程外存储机制。 此数据专门用于完整 HTTP 响应（页面输出缓存）。 此提供程序会插入 ASP.NET 4 中引入的新输出缓存提供程序扩展点。 若要使用输出缓存提供程序，请先配置缓存群集，然后使用缓存 NuGet 包配置用于缓存的 ASP.NET 应用程序，如[托管缓存服务入门][托管缓存服务入门]中所述。 在安装 Caching NuGet 包时，它会在 web.config 中添加以下包含 ASP.NET 应用程序所需配置的注释掉的节，以使用用于 Azure Caching 的输出缓存提供程序。

    <!--Uncomment this section to use Azure Caching for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

> 如果安装缓存 NuGet 包后，web.config 仍不含此注释掉的节，请确保从[安装 NuGet 包管理器][安装 NuGet 包管理器]安装最新的 NuGet 包管理器，然后卸载并重新安装该包。
> 
> 

若要启用用于 Azure Cache 的输出缓存提供程序，请取消注释指定的节。 在提供的代码段中指定了默认缓存。 若要使用不同的缓存，请在 **cacheName** 属性中指定所需缓存。

将 **OutputCache** 指令添加到希望为其缓存输出的每个页面中。

    <%@ OutputCache Duration="60" VaryByParam="*" %>

在此示例中，缓存的页面数据将保留在缓存中 60 秒，并且将为每个参数组合缓存不同版本的页面。 有关可用选项的详细信息，请参阅 [OutputCache 指令][OutputCache 指令]。

有关使用用于 Azure 缓存的输出缓存提供程序的详细信息，请参阅[用于 Azure 缓存的输出缓存提供程序][用于 Azure 缓存的输出缓存提供程序]。

<a name="next-steps"></a>

## <a name="next-steps"></a>后续步骤
现在，你已了解托管缓存服务的基础知识，请单击下面的链接了解如何执行更复杂的缓存任务。

* 参阅 MSDN 参考：[托管缓存服务][托管缓存服务]
* 了解如何迁移到托管缓存服务：[迁移到托管缓存服务][迁移到托管缓存服务]
* 查看示例：[托管缓存服务示例][托管缓存服务示例]

<!-- INTRA-TOPIC LINKS -->
[后续步骤]: #next-steps
[什么是 Azure 托管缓存服务？]: #what-is
[创建 Azure 缓存]: #create-cache
[哪种类型的缓存适合我？]: #choosing-cache
[准备 Visual Studio 项目以使用 Azure Caching]: #prepare-vs
[将应用程序配置为使用缓存]: #configure-app
[托管缓存服务入门]: #getting-started-cache-service
[创建缓存]: #create-cache
[配置缓存]: #enable-caching
[配置缓存客户端]: #NuGet
[使用缓存]: #working-with-caches
[如何创建 DataCache 对象]: #create-cache-object
[如何在缓存中添加和检索对象]: #add-object
[如何在缓存中指定对象的有效期]: #specify-expiration
[如何在缓存中存储 ASP.NET 会话状态]: #store-session
[如何在缓存中存储 ASP.NET 页面输出缓存]: #store-page
[定位支持的.NET Framework 配置文件]: #prepare-vs-target-net

<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Endpoint]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg


<!-- LINKS -->
[Azure 经典门户]: https://manage.windowsazure.com/
[如何以编程方式配置缓存客户端]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[用于 Azure 缓存的会话状态提供程序]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric 缓存：缓存会话状态]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[用于 Azure 缓存的输出缓存提供程序]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure 共享缓存]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[团队博客]: http://blogs.msdn.com/b/windowsazure/
[Azure 缓存]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[如何配置虚拟机大小]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching 容量规划注意事项]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure 缓存]: http://go.microsoft.com/fwlink/?LinkId=252658
[如何以声明方式设置 ASP.NET 页面可缓存性]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[如何以编程方式设置页面的可缓存性]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Azure 托管缓存服务概述]: http://go.microsoft.com/fwlink/?LinkId=320830
[托管缓存服务]: http://go.microsoft.com/fwlink/?LinkId=320830
[OutputCache 指令]: http://go.microsoft.com/fwlink/?LinkId=251979
[疑难解答和诊断]: http://go.microsoft.com/fwlink/?LinkId=320839
[安装 NuGet 包管理器]: http://go.microsoft.com/fwlink/?LinkId=240311
[缓存定价详细信息]: http://www.windowsazure.com/pricing/details/cache/
[缓存产品]: http://go.microsoft.com/fwlink/?LinkId=317277
[容量规划]: http://go.microsoft.com/fwlink/?LinkId=320167
[过期和逐出]: http://go.microsoft.com/fwlink/?LinkId=317278
[高可用性]: http://go.microsoft.com/fwlink/?LinkId=317329
[通知]: http://go.microsoft.com/fwlink/?LinkId=317276
[迁移到托管缓存服务]: http://go.microsoft.com/fwlink/?LinkId=317347
[托管缓存服务示例]: http://go.microsoft.com/fwlink/?LinkId=320840
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Azure 托管缓存 Cmdlet]: http://go.microsoft.com/fwlink/?LinkID=398555
[如何安装和配置 Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/library/dn495203.aspx

[哪种 Azure 缓存产品适合我？]: cache-faq.md#which-azure-cache-offering-is-right-for-me




<!--HONumber=Nov16_HO3-->


