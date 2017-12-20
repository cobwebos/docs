---
title: "如何使用 Redis 缓存创建 Web 应用 | Microsoft Docs"
description: "了解如何使用 Redis 缓存创建 Web 应用"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 05/09/2017
ms.author: sdanie
ms.openlocfilehash: 21dc87b3e8c26bfbda36202b31b3b4d44be32179
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>如何使用 Redis 缓存创建 Web 应用
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

本教程介绍如何使用 Visual Studio 2017 创建 ASP.NET Web 应用程序并将其部署到 Azure 应用服务中的 Web 应用。 该示例应用程序显示了数据库中的团队统计信息列表，并显示了使用 Azure Redis 缓存通过缓存存储和检索数据的不同方式。 完成本教程后，将有一个运行的 Web 应用，该应用可以对数据库执行读写操作，已通过 Azure Redis 缓存进行优化，并且托管在 Azure 中。

学习内容：

* 如何在 Visual Studio 中创建 ASP.NET MVC 5 Web 应用程序。
* 如何使用实体框架从数据库访问数据。
* 如何使用 Azure Redis 缓存来存储和检索数据，以便改进数据吞吐量并降低数据库负载。
* 如何使用 Redis 排序的集来检索排名前 5 的团队。
* 如何使用 Resource Manager 模板为应用程序预配 Azure 资源。
* 如何使用 Visual Studio 将应用程序发布到 Azure。

## <a name="prerequisites"></a>先决条件
若要完成本教程，必须具备以下先决条件。

* [Azure 帐户](#azure-account)
* [带有用于 .NET 的 Azure SDK 的 Visual Studio 2017](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Azure 帐户
完成本教程需要有一个 Azure 帐户。 可以：

* [免费注册 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 获取可用来尝试付费版 Azure 服务的信用额度。 即使在信用额度用完之后，也可以保留帐户和使用免费的 Azure 服务和功能。
* [激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 MSDN 订阅每月提供可用来试用付费版 Azure 服务的信用额度。

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>带有用于 .NET 的 Azure SDK 的 Visual Studio 2017
本教程专为配合使用 Visual Studio 2017 和[用于 .NET 的 Azure SDK](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools) 编写。 Azure SDK 2.9.5 随附在 Visual Studio 安装程序中。

如果安装了 Visual Studio 2015，则可遵循适用于 [Azure SDK for .NET](../dotnet-sdk.md) 2.8.2 或更高版本的教程。 [单击此处下载最新的用于 Visual Studio 2015 的 Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003)。 如果尚未安装 Visual Studio，则会随 SDK 一起自动安装。 某些屏幕看起来可能与本教程中显示的插图不同。

如果有 Visual Studio 2013，则可以 [下载最新的 Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322)。 某些屏幕看起来可能与本教程中显示的插图不同。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目
1. 打开 Visual Studio，并依次单击“文件”、“新建”、“项目”。
2. 展开“模板”列表中的“Visual C#”节点，选择“云”，并单击“ASP.NET Web 应用程序”。 确保选中“.NET Framework 4.5.2”或更高版本。  在“名称”框中键入“ContosoTeamStats”，并单击“确定”。
   
    ![创建项目][cache-create-project]
3. 选择“MVC”作为项目类型。 

    对于“身份验证”设置，请确保指定“无身份验证”。 根据 Visual Studio 版本，可能会默认设置为其他。 要对其进行更改，请单击“更改身份验证”，并选择“无身份验证”。

    如果使用的是 Visual Studio 2015，请清除“在云中托管”复选框。 本教程的后续步骤介绍了[预配 Azure 资源](#provision-the-azure-resources)以及[将应用程序发布到 Azure](#publish-the-application-to-azure)。 勾选“在云中托管”，即可通过 Visual Studio 预配应用服务 Web 应用，如需此方面的示例，请参阅[配合 ASP.NET 和 Visual Studio 使用 Azure 应用服务中的 Web 应用入门](../app-service/app-service-web-get-started-dotnet.md)。
   
    ![选择项目模板][cache-select-template]
4. 单击“确定”以创建该项目  。

## <a name="create-the-aspnet-mvc-application"></a>创建 ASP.NET MVC 应用程序
在本教程的此部分，将创建可以从数据库读取和显示团队统计信息的基本应用程序。

* [添加实体框架 NuGet 包](#add-the-entity-framework-nuget-package)
* [添加模型](#add-the-model)
* [添加控制器](#add-the-controller)
* [配置视图](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>添加实体框架 NuGet 包

1. 在“工具”菜单中依次单击“NuGet 包管理器”和“包管理器控制台”。
2. 在“包管理器控制台”窗口中，运行以下命令。
    
    ```
    Install-Package EntityFramework
    ```

有关此包的详细信息，请参阅 [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet 页。

### <a name="add-the-model"></a>添加模型
1. 右键单击“解决方案资源管理器”中的“模型”，并选择“添加”>“类”。 
   
    ![添加模型][cache-model-add-class]
2. 输入 `Team` 作为类名，并单击“添加”。
   
    ![添加模型类][cache-model-add-class-dialog]
3. 将 `Team.cs` 文件顶部的 `using` 语句替换为以下 `using` 语句。

    ```c#
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. 将 `Team` 类的定义替换为以下代码片段，其中包含更新的 `Team` 类定义以及某些其他实体框架帮助器类。 有关本教程中使用的实体框架 Code First 方法的详细信息，请参阅 [对新数据库使用 Code First](https://msdn.microsoft.com/data/jj193542)。

    ```c#
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. 在“解决方案资源管理器”中，双击“web.config”将其打开。
   
    ![Web.config][cache-web-config]
2. 添加下面的 `connectionStrings` 节。 连接字符串的名称必须与实体框架数据库上下文类（即 `TeamContext`）的名称相匹配。

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    可以将新的 `connectionStrings` 节添加到 `configSections` 后面，如以下示例所示。

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

    > [!NOTE]
    > 连接字符串可能不同，具体取决于用来完成教程的 Visual Studio 和 SQL Server Express 版的版本。 web.config 模板应当配置为与安装相匹配，并且可以包含诸如 `(LocalDB)\v11.0`（来自 SQL Server Express 2012）或 `Data Source=(LocalDB)\MSSQLLocalDB`（来自 SQL Server Express 2014 和更高版本）的 `Data Source` 条目。 有关连接字符串和 SQL Express 版本的详细信息，请参阅 [SQL Server 2016 Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。

### <a name="add-the-controller"></a>添加控制器
1. 按“F6”  生成项目。 
2. 在“解决方案资源管理器”中，右键单击“Controllers”文件夹，然后选择“添加”，再选择“控制器”。
   
    ![添加控制器][cache-add-controller]
3. 选择“使用实体框架的包含视图的 MVC 5 控制器”并单击“添加”。 如果在单击“添加”后出现错误，请确保已先生成该项目。
   
    ![添加控制器类][cache-add-controller-class]
4. 从“模型类”下拉列表中选择“Team (ContosoTeamStats.Models)”。 从“数据上下文类”下拉列表中选择“TeamContext (ContosoTeamStats.Models)”。 在“控制器”名称文本框中键入 `TeamsController`（如果尚未自动填充）。 单击“添加”  创建控制器类并添加默认视图。
   
    ![配置控制器][cache-configure-controller]
5. 在“解决方案资源管理器”中展开“Global.asax”，然后双击“Global.asax.cs”将其打开。
   
    ![Global.asax.cs][cache-global-asax]
6. 将以下两个 `using` 语句添加到文件顶部的其他 `using` 语句下方。

    ```c#
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. 在 `Application_Start` 方法的末尾添加以下代码行。

    ```c#
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. 在“解决方案资源管理器”中展开 `App_Start`，并双击 `RouteConfig.cs`。
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. 将以下代码的 `RegisterRoutes` 方法中的 `controller = "Home"` 替换为 `controller = "Teams"`，如以下示例所示。

    ```c#
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>配置视图
1. 在“解决方案资源管理器”中，先展开 **Views** 文件夹，再展开 **Shared** 文件夹，然后双击 **_Layout.cshtml**。 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. 更改 `title` 元素的内容，将 `My ASP.NET Application` 替换为 `Contoso Team Stats`，如以下示例所示。

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. 在 `body` 节中，更新第一个 `Html.ActionLink` 语句，将 `Application name` 替换为 `Contoso Team Stats`，将 `Home` 替换为 `Teams`。
   
   * 之前：`@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * 之后： `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![代码更改][cache-layout-cshtml-code]
2. 按“Ctrl+F5”  生成并运行应用程序。 此版本的应用程序直接从数据库读取结果。 请注意已通过“使用实体框架的包含视图的 MVC 5 控制器”基架自动添加到应用程序的“新建”、“编辑”、“详细信息”和“删除”操作。 在本教程的下一部分，需要添加优化数据访问所需的 Redis 缓存，并向应用程序提供其他功能。

![入门应用程序][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>配置应用程序以使用 Redis 缓存
在本教程的此部分，需要对示例应用程序进行配置，以便使用 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 缓存客户端通过 Azure Redis 缓存实例来存储和检索 Contoso 团队统计信息。

* [配置应用程序以使用 StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [更新 TeamsController 类，以便从缓存或数据库返回结果](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [更新用于缓存的 Create、Edit 和 Delete 方法](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [更新用于缓存的“团队索引”视图](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>配置应用程序以使用 StackExchange.Redis
1. 若要在 Visual Studio 中使用 StackExchange.Redis NuGet 包配置客户端应用程序，请在“工具”菜单中依次单击“NuGet 程序包管理器”、“程序包管理器控制台”。
2. 从 `Package Manager Console` 窗口运行以下命令。
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    NuGet 程序包会给客户端应用程序下载并添加所需的程序集引用，以访问带 StackExchange.Redis 缓存客户端的 Azure Redis 缓存。 如果更愿使用强命名版本的 `StackExchange.Redis` 客户端库，请安装 `StackExchange.Redis.StrongName` 包。
3. 在“解决方案资源管理器”中，展开“Controllers”文件夹，然后双击“TeamsController.cs”将其打开。
   
    ![团队控制器][cache-teamscontroller]
4. 将以下两个 `using` 语句添加到 **TeamsController.cs**。

    ```c#   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. 将以下两个属性添加到 `TeamsController` 类。

    ```c#   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. 在计算机中创建一个名为 `WebAppPlusCacheAppSecrets.config` 的文件，将其置于不会使用示例应用程序的源代码对其进行检查的位置（如果决定在某个位置对其进行检查）。 在此示例中，`AppSettingsSecrets.config` 文件位于 `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`。
   
    编辑 `WebAppPlusCacheAppSecrets.config` 文件，添加以下内容。 如果在本地运行该应用程序，则可利用此信息连接到 Azure Redis 缓存实例。 在本教程中，随后将预配 Azure Redis 缓存实例并更新缓存名称和密码。 如果不打算在本地运行示例应用程序，则可跳过此文件的创建步骤以及后续的文件引用步骤，因为部署到 Azure 时，应用程序会从 Web 应用的应用设置而非从此文件中检索缓存连接信息。 由于 `WebAppPlusCacheAppSecrets.config` 不与应用程序一起部署到 Azure，因此除非要在本地运行应用程序，否则不需要它。

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. 在“解决方案资源管理器”中，双击“web.config”将其打开。
   
    ![Web.config][cache-web-config]
2. 将以下 `file` 属性添加到 `appSettings` 元素。 如果使用了其他文件名或位置，请使用这些值来替换示例中显示的值。
   
   * 之前： `<appSettings>`
   * 之后： ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   ASP.NET 运行时合并了外部文件的内容以及 `<appSettings>` 元素中的标记。 如果找不到指定的文件，运行时会忽略文件属性。 应用程序的源代码中将不包括机密（连接到缓存的连接字符串）。 将 Web 应用部署到 Azure 时，不会部署 `WebAppPlusCacheAppSecrests.config` 文件（正是需要的）。 可以通过多种方式在 Azure 中指定这些机密，而在本教程中，在后续的教程步骤中 [预配 Azure 资源](#provision-the-azure-resources) 时，系统会自动配置这些机密。 有关如何在 Azure 中处理密钥的详细信息，请参阅[将密码和其他敏感数据部署到 ASP.NET 和 Azure 应用服务的最佳做法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)。

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>更新 TeamsController 类，以便从缓存或数据库返回结果
在此示例中，团队统计信息可以从数据库或缓存中检索。 团队统计信息以序列化 `List<Team>`的形式存储在缓存中，同时以排序集的形式按 Redis 数据类型存储。 从排序集检索项目时，可以检索部分项目或所有项目，也可以查询特定项目。 在此示例中，将按胜利数在排序集中查询排名前 5 的团队。

> [!NOTE]
> 不需在缓存中以多种格式存储团队统计信息即可使用 Azure Redis 缓存。 本教程以多种格式演示了缓存数据时可以使用的不同方法和不同数据类型的一部分。
> 
> 

1. 将以下 `using` 语句添加到 `TeamsController.cs` 文件顶部，与其他 `using` 语句放置在一起。

    ```c#   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. 将当前的 `public ActionResult Index()` 方法实现替换为以下实现。

    ```c#
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. 将以下三种方法添加到 `TeamsController` 类，以便实现在以前的代码片段中添加的 switch 语句中的 `playGames`、`clearCache` 和 `rebuildDB` 操作类型。
   
    `PlayGames` 方法可以通过对赛季进行模拟来更新团队统计信息，将结果保存到数据库，然后从缓存中清除现已过时的数据。

    ```c#
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `RebuildDB` 方法使用默认的团队集来重新初始化数据库，为其生成统计信息，并从缓存中清除现已过时的数据。

    ```c#
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `ClearCachedTeams` 方法从缓存中删除任何已缓存的团队统计信息。

    ```c#
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. 将以下四种方法添加到 `TeamsController` 类，以便通过不同的方式从缓存和数据库检索团队统计信息。 这些方法均会返回 `List<Team>`，然后通过视图将其显示出来。
   
    `GetFromDB` 方法从数据库读取团队统计信息。
   
    ```c#
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    `GetFromList` 方法以序列化 `List<Team>` 的形式从缓存读取团队统计信息。 如果缓存未命中，则会从数据库读取团队统计信息，然后将该信息存储在缓存中留待下次使用。 在此示例中，我们将通过 JSON.NET 序列化来序列化进出缓存的 .NET 对象。 有关详细信息，请参阅 [如何处理 Azure Redis 缓存中的 .NET 对象](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)。

    ```c#
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    `GetFromSortedSet` 方法从缓存的排序集读取团队统计信息。 如果缓存未命中，则会从数据库读取团队统计信息，然后将该信息以排序集的形式存储在缓存中。

    ```c#
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    `GetFromSortedSetTop5` 方法从缓存的排序集中读取排名前 5 的团队。 该方法首先会检查缓存中是否存在 `teamsSortedSet` 键。 如果该键不存在，则会调用 `GetFromSortedSet` 方法以读取团队统计信息并将其存储在缓存中。 接下来会对缓存的排序集进行查询，以便返回排名前 5 的团队。

    ```c#
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>更新用于缓存的 Create、Edit 和 Delete 方法
作为此示例一部分生成的基架代码包括用于添加、编辑和删除团队的方法。 只要添加、编辑或删除了团队，缓存中的数据就变成了过时的数据。 在本部分，需要修改这三种清除缓存团队的方法，避免出现缓存与数据库不同步的情况。

1. 浏览到 `TeamsController` 类中的 `Create(Team team)` 方法。 添加对 `ClearCachedTeams` 方法的调用，如以下示例所示。

    ```c#
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. 浏览到 `TeamsController` 类中的 `Edit(Team team)` 方法。 添加对 `ClearCachedTeams` 方法的调用，如以下示例所示。

    ```c#
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. 浏览到 `TeamsController` 类中的 `DeleteConfirmed(int id)` 方法。 添加对 `ClearCachedTeams` 方法的调用，如以下示例所示。

    ```c#
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>更新用于缓存的“团队索引”视图
1. 在“解决方案资源管理器”中，先展开 **Views** 文件夹，再展开 **Teams** 文件夹，然后双击“Index.cshtml”。
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. 在该文件顶部附近查找以下段落元素。
   
    ![操作表][cache-teams-index-table]
   
    这是创建新团队的链接。 将段落元素替换为下表。 该表的操作链接可用于创建新的团队、举行新赛季的比赛、清除缓存、以多种格式从缓存中检索团队、从数据库检索团队，以及使用最新的示例数据重新构建数据库。

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. 滚动到 **Index.cshtml** 文件底部，并添加下面的 `tr` 元素，使之成为文件中最后一个表的最后一行。
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    此行返回值 `ViewBag.Msg`，其中包含有关当前操作的状态报告。 单击上一步中的任何操作链接即可设置 `ViewBag.Msg`。   
   
    ![状态消息][cache-status-message]
2. 按“F6”  生成项目。

## <a name="provision-the-azure-resources"></a>预配 Azure 资源
若要在 Azure 中托管应用程序，必须先预配应用程序所需的 Azure 服务。 本教程中的示例应用程序使用以下 Azure 服务。

* Azure Redis 缓存
* 应用服务 Web 应用
* SQL 数据库

要将这些服务部署到新建的或现有资源组，请单击下面的“ **部署到 Azure** ”按钮。

[![部署到 Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

此“部署到 Azure”按钮将使用[创建 Web 应用 + Redis 缓存 + SQL 数据库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure 快速入门](https://github.com/Azure/azure-quickstart-templates)模板来预配这些服务，并设置 SQL 数据库的连接字符串和 Azure Redis 缓存连接字符串的应用程序设置。

> [!NOTE]
> 如果没有 Azure 帐户，只需几分钟的时间就能 [创建一个免费的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)。
> 
> 

单击“ **部署到 Azure** ”按钮，会转到 Azure 门户并开始创建模板所述的资源。

![部署到 Azure][cache-deploy-to-azure-step-1]

1. 在“基本情况”部分，选择要使用的 Azure 订阅，选择现有资源组或新建一个并指定资源组位置。
2. 在“设备”部分，指定“管理员登录名”（不使用“admin”）、“管理员登录密码”和“数据库名称”。 为免费应用服务托管计划配置其他参数，为 SQL 数据库和 Azure Redis 缓存配置较低的成本选项，其中无免费层。

    ![“部署到 Azure”][cache-deploy-to-azure-step-2]

3. 配置所需设置后，滚动到页底部，阅读条款和条件，并选中“我同意上述条款和条件”复选框。
4. 若要开始预配资源，请单击“购买”。

要查看部署进度，请单击通知图标，并单击“ **已启动的部署**”。

![已启动的部署][cache-deployment-started]

可以在“ **Microsoft.Template** ”边栏选项卡上查看部署状态。

![部署到 Azure][cache-deploy-to-azure-step-3]

预配完成后，即可将应用程序从 Visual Studio 发布到 Azure。

> [!NOTE]
> 预配期间可能出现的任何错误都会显示在“ **Microsoft.Template** ”边栏选项卡上。 常见错误有：每个订阅的 SQL 服务器太多或免费应用服务托管计划太多。 解决所有错误，并单击“Microsoft.Template”边栏选项卡上的“重新部署”或本教程中的“部署到 Azure”按钮，重新启动此过程。
> 
> 

## <a name="publish-the-application-to-azure"></a>将应用程序发布到 Azure
在教程的这一步中，需要将应用程序发布到 Azure 并在云中运行。

1. 在 Visual Studio 中右键单击“ContosoTeamStats”项目，并选择“发布”。
   
    ![发布][cache-publish-app]
2. 单击“Microsoft Azure 应用服务”，选择“选择现有项”，并单击“发布”。
   
    ![发布][cache-publish-to-app-service]
3. 选择在创建 Azure 资源时使用的订阅，展开包含资源的资源组，并选择所需的 Web 应用。 如果使用的是“部署到 Azure”按钮，则 Web 应用名称会以 **webSite** 开头，后跟某些其他字符。
   
    ![选择 Web 应用][cache-select-web-app]
4. 单击“确定”开始发布过程。 几分钟后，发布过程完成，运行的示例应用程序会启动浏览器。 如果在验证或发布时出现 DNS 错误，而应用程序的 Azure 资源的预配过程才刚刚完成，则请稍等片刻，并重试。
   
    ![添加的缓存][cache-added-to-application]

下表描述了示例应用程序中的每个操作链接。

| 操作 | 说明 |
| --- | --- |
| 新建 |创建新的团队。 |
| 举行赛季的比赛 |举行一赛季的比赛、更新团队统计信息，以及从缓存中清除任何过时的团队数据。 |
| 清除缓存 |清除缓存中的团队统计信息。 |
| 缓存中的列表 |检索缓存中的团队统计信息。 如果缓存未命中，则从数据库加载统计信息，并将其保存到缓存中以便下次使用。 |
| 缓存中的排序集 |使用排序集检索缓存中的团队统计信息。 如果缓存未命中，则从数据库加载统计信息，并使用排序集将其保存到缓存中。 |
| 缓存中排名前 5 的团队 |使用排序集检索缓存中排名前 5 的团队。 如果缓存未命中，则从数据库加载统计信息，并使用排序集将其保存到缓存中。 |
| 从数据库加载 |检索数据库中的团队统计信息。 |
| 重新生成数据库 |重新生成数据库并使用示例团队数据重新加载它。 |
| 编辑/详细信息/删除 |编辑团队、查看团队详细信息、删除团队。 |

单击部分操作，试验如何从不同的源检索数据。 请注意通过不同方式从数据库和缓存检索数据所存在的时间差异。

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>完成应用程序的操作以后，删除相关资源
完成示例性的教程应用程序以后，即可删除所用的 Azure 资源，以便节省成本和资源。 如果使用[预配 Azure 资源](#provision-the-azure-resources)部分的“部署到 Azure”按钮，并且所有资源都包含在同一资源组中，则可通过删除资源组这一个操作来删除所有资源。

1. 登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。
2. 在“筛选项目...”  文本框中键入资源组的名称。
3. 单击资源组右侧的“...”  。
4. 单击“删除” 。
   
    ![删除][cache-delete-resource-group]
5. 键入资源组的名称，并单击“删除” 。
   
    ![确认删除][cache-delete-confirm]

几分钟后，资源组及其包含的所有资源就会被删除。

> [!IMPORTANT]
> 请注意，删除资源组这一操作是不可逆的，资源组以及其中的所有资源会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果是在现有的资源组中为托管此示例而创建了相关资源，则可从各自的边栏选项卡逐个删除这些资源。
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>在本地计算机上运行示例应用程序
若要在计算机上本地运行应用程序，则需在 Azure Redis 缓存实例中缓存数据。 

* 如果已根据上一部分所述将应用程序发布到 Azure，则可使用在该步骤中预配的 Azure Redis 缓存实例。
* 如果有另一个现成的 Azure Redis 缓存实例，则可使用该实例在本地运行此示例。
* 如果需要创建 Azure Redis 缓存实例，则可按 [创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)中的步骤来执行操作。

选择或创建要使用的缓存后，在 Azure 门户中浏览到该缓存，并检索缓存的[主机名](cache-configure.md#properties)和[访问密钥](cache-configure.md#access-keys)。 有关说明，请参阅 [配置 Redis 缓存设置](cache-configure.md#configure-redis-cache-settings)。

1. 使用所选编辑器打开在本教程的[配置应用程序以使用 Redis 缓存](#configure-the-application-to-use-redis-cache)步骤中创建的 `WebAppPlusCacheAppSecrets.config` 文件。
2. 编辑 `value` 属性，将 `MyCache.redis.cache.windows.net` 替换为缓存的[主机名](cache-configure.md#properties)，并指定缓存的[主密钥或辅助密钥](cache-configure.md#access-keys)作为密码。

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. 按“Ctrl+F5”  运行应用程序。

> [!NOTE]
> 请注意，由于应用程序（包括数据库）是在本地运行，而 Redis 缓存是托管在 Azure 中，因此从表现上看缓存可能不如数据库。 为了获得最佳性能，应让客户端应用程序和 Azure Redis 缓存实例位于同一位置。 
> 
> 

## <a name="next-steps"></a>后续步骤
* 在 [ASP.NET](http://asp.net/) 站点上了解有关 [ASP.NET MVC 5 入门](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started)的详细信息。
* 有关在应用服务中创建 ASP.NET Web 应用的更多示例，请从 [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [演示](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)参阅[在 Azure 应用服务中创建和部署 ASP.NET Web 应用](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service)。
  * 有关 HealthClinic.biz 演示的多个快速入门，请参阅 [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)（Azure 开发人员工具快速入门）。
* 详细了解 [对新数据库使用 Code First](https://msdn.microsoft.com/data/jj193542) 中介绍的本教程所用实体框架需要的方法。
* 详细了解 [Azure 应用服务中的 Web 应用](../app-service/app-service-web-overview.md)。
* 了解如何在 Azure 门户中 [监视](cache-how-to-monitor.md) 缓存。
* 了解 Azure Redis 缓存高级功能
  
  * [如何为高级 Azure Redis 缓存配置暂留](cache-how-to-premium-persistence.md)
  * [如何为高级 Azure Redis 缓存配置群集功能](cache-how-to-premium-clustering.md)
  * [如何为高级 Azure Redis 缓存配置虚拟网络支持](cache-how-to-premium-vnet.md)
  * 有关高级缓存大小、吞吐量和带宽的更多详细信息，请参阅 [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) （Azure Redis 缓存常见问题解答）。

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

