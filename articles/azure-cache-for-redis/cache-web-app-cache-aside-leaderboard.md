---
title: 教程：创建 Web 应用（缓存端）- Azure Cache for Redis
description: 了解如何通过 Azure Cache for Redis 创建使用缓存端模式的 Web 应用。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/30/2018
ms.openlocfilehash: e8b8feff0b66aa0b48c88b43049594003b20e5c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411944"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>教程：在 ASP.NET 中创建缓存端排行榜

在本教程中，我们将更新在[用于 Redis 的 Azure 缓存的 ASP.NET 快速入门](cache-web-app-howto.md)中创建的 *ContosoTeamStats* ASP.NET Web 应用，以包括将[缓存端模式](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)与用于 Redis 的 Azure 缓存配合使用的排行榜。 该示例应用程序显示数据库中的团队统计信息列表，并演示如何通过不同的方法使用用于 Redis 的 Azure 缓存在缓存中存储和检索数据，以提高性能。 完成本教程后，将有一个运行的 Web 应用，该应用可以对数据库执行读写操作，已通过用于 Redis 的 Azure 缓存进行优化，并且托管在 Azure 中。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用用于 Redis 的 Azure 缓存来存储和检索数据，以提高数据吞吐量并降低数据库负载。
> * 使用 Redis 排序的集来检索排名前五的团队。
> * 使用资源管理器模板为应用程序预配 Azure 资源。
> * 使用 Visual Studio 将应用程序发布到 Azure。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须满意以下先决条件：

* 本教程是[用于 Redis 的 Azure 缓存的 ASP.NET 快速入门](cache-web-app-howto.md)的延续。 如果尚未学习此快速入门，请先学习它。
* 使用以下工作负荷安装 [Visual Studio 2019](https://www.visualstudio.com/downloads/)：
    * ASP.NET 和 Web 开发
    * Azure 开发
    * 使用 SQL Server Express LocalDB 或 [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express) 进行 .NET 桌面开发。

## <a name="add-a-leaderboard-to-the-project"></a>将排行榜添加到项目

在本教程的本部分，我们将配置 *ContosoTeamStats* 项目，该项目中的排行榜报告虚构团队列表的赢局、输局和平局统计信息。

### <a name="add-the-entity-framework-to-the-project"></a>将实体框架添加到项目

1. 在 Visual Studio 中，打开在[用于 Redis 的 Azure 缓存的 ASP.NET 快速入门](cache-web-app-howto.md)中创建的 *ContosoTeamStats* 解决方案。
2. 单击“工具”>“NuGet 包管理器”>“包管理器控制台”。 
3. 在“包管理器控制台”窗口中，运行以下命令安装 EntityFramework： 

    ```powershell
    Install-Package EntityFramework
    ```

有关此包的详细信息，请参阅 [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet 页。

### <a name="add-the-team-model"></a>添加团队模型

1. 右键单击“解决方案资源管理器”中的“模型”，并选择“添加”>“类”。    

1. 输入 `Team` 作为类名，并单击“添加”。 

    ![添加模型类](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. 将 *Team.cs* 文件顶部的 `using` 语句替换为以下 `using` 语句：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. 将 `Team` 类的定义替换为以下代码片段，其中包含更新的 `Team` 类定义以及某些其他实体框架帮助器类。 本教程对实体框架使用代码优先方法。 此方法可让实体框架通过代码创建数据库。 有关本教程中使用的实体框架 Code First 方法的详细信息，请参阅 [对新数据库使用 Code First](/ef/ef6/modeling/code-first/workflows/new-database)。

    ```csharp
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

1. 在“解决方案资源管理器”中，双击“Web.config”将其打开。  

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. 将下面的 `connectionStrings` 节添加到 `configuration` 节中： 连接字符串的名称必须与实体框架数据库上下文类（即 `TeamContext`）的名称相匹配。

    此连接字符串假设已满足[先决条件](#prerequisites)，并已安装 SQL Server Express LocalDB（属于连同 Visual Studio 2019 一起安装的 *.NET 桌面开发*工作负荷的一部分）。

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    以下示例显示新的 `connectionStrings` 节跟随 `configSections` 一起位于 `configuration` 节中：

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>添加 TeamsController 和视图

1. 在 Visual Studio 中生成项目。 

1. 在“解决方案资源管理器”中，右键单击“Controllers”文件夹，然后选择“添加”，再选择“控制器”。    

1. 选择“使用实体框架的包含视图的 MVC 5 控制器”并单击“添加”。   如果在单击“添加”  后出现错误，请确保已先生成该项目。

    ![添加控制器类](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. 从“模型类”下拉列表中选择“Team (ContosoTeamStats.Models)”。   从“数据上下文类”下拉列表中选择“TeamContext (ContosoTeamStats.Models)”。   在“控制器”名称文本框中键入 `TeamsController`（如果尚未自动填充）。  单击“添加”  创建控制器类并添加默认视图。

    ![配置控制器](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. 在“解决方案资源管理器”中展开“Global.asax”，然后双击“Global.asax.cs”将其打开。   

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. 将以下两个 `using` 语句添加到文件顶部的其他 `using` 语句下方：

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. 在 `Application_Start` 方法的末尾添加以下代码行：

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. 在“解决方案资源管理器”中展开 `App_Start`，并双击 `RouteConfig.cs`。 

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. 在 `RegisterRoutes` 方法中，将 `Default` 路由中的 `controller = "Home"` 替换为 `controller = "Teams"`，如以下示例所示：

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>配置布局视图

1. 在“解决方案资源管理器”中，先展开 **Views** 文件夹，再展开 **Shared** 文件夹，然后双击 **_Layout.cshtml**。  

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. 更改 `title` 元素的内容，将 `My ASP.NET Application` 替换为 `Contoso Team Stats`，如以下示例所示：

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. 在 `body` 节中，紧靠在 *Azure Cache for Redis Test* 链接的下方为 *Contoso Team Stats* 添加以下新的 `Html.ActionLink` 语句。

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![代码更改](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. 按“Ctrl+F5”  生成并运行应用程序。 此版本的应用程序直接从数据库读取结果。 请注意已通过“使用实体框架的包含视图的 MVC 5 控制器”基架自动添加到应用程序的“新建”、“编辑”、“详细信息”和“删除”操作。      在本教程的下一部分中，你将添加用于 Redis 的 Azure 缓存来优化数据访问，并向应用程序提供其他功能。

    ![入门应用程序](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>为用于 Redis 的 Azure 缓存配置应用

在本教程的此部分，请对示例应用程序进行配置，以便使用 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 缓存客户端通过用于 Redis 的 Azure 缓存实例来存储和检索 Contoso 团队统计信息。

### <a name="add-a-cache-connection-to-the-teams-controller"></a>将缓存连接添加到团队控制器

在快速入门中，我们已安装 *StackExchange.Redis* 客户端库包。 我们还配置了要在本地使用的 *CacheConnection* 应用设置，以及发布的应用服务。 在本教程中请使用相同的客户端库以及 *TeamsController* 中的 *CacheConnection* 信息。

1. 在“解决方案资源管理器”中，展开“Controllers”文件夹，然后双击“TeamsController.cs”将其打开。   

    ![团队控制器](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. 将以下两个 `using` 语句添加到 **TeamsController.cs**：

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. 将以下两个属性添加到 `TeamsController` 类：

    ```csharp
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

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>更新 TeamsController，以从缓存或数据库读取数据

在此示例中，团队统计信息可以从数据库或缓存中检索。 团队统计信息以序列化 `List<Team>`的形式存储在缓存中，同时以排序集的形式按 Redis 数据类型存储。 从排序集检索项目时，可以检索部分项目或所有项目，也可以查询特定项目。 本示例将按赢局数在排序集中查询排名前 5 的团队。

不需在缓存中以多种格式存储团队统计信息即可使用用于 Redis 的 Azure 缓存。 本教程以多种格式演示了缓存数据时可以使用的不同方法和不同数据类型的一部分。

1. 将以下 `using` 语句添加到 `TeamsController.cs` 文件顶部，与其他 `using` 语句放置在一起：

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. 将当前的 `public ActionResult Index()` 方法实现替换为以下实现：

    ```csharp
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

    ```csharp
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

    ```csharp
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

    ```csharp
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
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    `GetFromList` 方法以序列化 `List<Team>` 的形式从缓存读取团队统计信息。 如果缓存中没有统计信息，则会发生缓存未命中。 如果缓存未命中，则从数据库读取团队统计信息，然后将该信息存储在缓存中，供下一次请求使用。 本示例中使用 JSON.NET 序列化来序列化传入和传出缓存的 .NET 对象。 有关详细信息，请参阅[如何处理用于 Redis 的 Azure 缓存中的 .NET 对象](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)。

    ```csharp
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

    ```csharp
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

    ```csharp
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

作为此示例一部分生成的基架代码包括用于添加、编辑和删除团队的方法。 只要添加、编辑或删除了团队，缓存中的数据就变成了过时的数据。 在本部分，我们将修改这三个清除缓存团队的方法，以便刷新缓存。

1. 浏览到 `TeamsController` 类中的 `Create(Team team)` 方法。 添加对 `ClearCachedTeams` 方法的调用，如以下示例所示：

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
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

2. 浏览到 `TeamsController` 类中的 `Edit(Team team)` 方法。 添加对 `ClearCachedTeams` 方法的调用，如以下示例所示：

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
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

3. 浏览到 `TeamsController` 类中的 `DeleteConfirmed(int id)` 方法。 添加对 `ClearCachedTeams` 方法的调用，如以下示例所示：

    ```csharp
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

### <a name="add-caching-methods-to-the-teams-index-view"></a>将缓存方法添加到“团队索引”视图

1. 在“解决方案资源管理器”中，先展开 **Views** 文件夹，再展开 **Teams** 文件夹，然后双击“Index.cshtml”。  

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. 在该文件顶部附近查找以下段落元素：

    ![操作表](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    此链接创建新团队。 将段落元素替换为下表。 该表的操作链接可用于创建新的团队、举行新赛季的比赛、清除缓存、以多种格式从缓存中检索团队、从数据库检索团队，以及使用最新的示例数据重新构建数据库。

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

1. 滚动到 **Index.cshtml** 文件底部，并添加下面的 `tr` 元素，使之成为文件中最后一个表的最后一行：

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    此行返回值 `ViewBag.Msg`，其中包含有关当前操作的状态报告。 单击上一步中的任何操作链接即可设置 `ViewBag.Msg`。

    ![状态消息](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. 按“F6”  生成项目。

## <a name="run-the-app-locally"></a>在本地运行应用

在计算机本地运行应用程序，验证是否已添加相应的功能来支持团队。

在此测试中，应用程序和数据库都在本地运行。 但是，用于 Redis 的 Azure 缓存远程托管在 Azure 中。 因此，缓存的性能可能比数据库略低。 为了获得最佳性能，应让客户端应用程序和用于 Redis 的 Azure 缓存实例位于同一位置。 在下一部分，我们要将所有资源部署到 Azure，以查看在使用缓存后性能是否得到提升。

在本地运行应用：

1. 按“Ctrl+F5”  运行应用程序。

    ![本地运行的应用](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. 测试已添加到视图的每个新方法。 由于这些测试中的缓存在远程位置，因此数据库的性能应该比缓存略高。

## <a name="publish-and-run-in-azure"></a>在 Azure 中发布和运行

### <a name="provision-a-sql-azure-database-for-the-app"></a>为应用预配 SQL Azure 数据库

在本部分，我们将为应用预配新的 SQL Azure 数据库，以便在 Azure 中托管时使用。

1. 在 [Azure 门户](https://portal.azure.com/)中，单击左上角的“创建资源”。 

1. 在“新建”页上，单击“数据库” > “SQL 数据库”。   

1. 对新 SQL 数据库使用以下设置：

   | 设置       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **数据库名称** | *ContosoTeamsDatabase* | 如需有效的数据库名称，请参阅 [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)（数据库标识符）。 |
   | **订阅** | 用户的订阅   | 选择用于创建缓存和托管应用服务的同一订阅。 |
   | **资源组**  | *TestResourceGroup* | 单击“使用现有项”，并使用缓存和应用服务所在的同一资源组。  |
   | **选择源** | **空白数据库** | 从空白数据库开始。 |

1. 在“服务器”下，单击“配置所需的设置” > “创建新服务器”并提供以下信息，然后单击“选择”按钮：    

   | 设置       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **服务器名称** | 任何全局唯一名称 | 如需有效的服务器名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming)（命名规则和限制）。 |
   | 服务器管理员登录名  | 任何有效的名称 | 如需有效的登录名，请参阅 [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)（数据库标识符）。 |
   | **密码** | 任何有效的密码 | 密码必须至少有 8 个字符，且必须包含以下类别中的三个类别的字符：大写字符、小写字符、数字以及非字母数字字符。 |
   | **位置** | *美国东部* | 选择创建缓存和应用服务的同一区域。 |

1. 单击“固定到仪表板”，然后单击“创建”以创建新数据库和服务器。  

1. 创建新数据库后，单击“显示数据库连接字符串”，并复制 **ADO.NET** 连接字符串。 

    ![显示连接字符串](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. 在 Azure 门户中，导航到应用服务并单击“应用程序设置”，然后在“连接字符串”部分下单击“添加新的连接字符串”。  

1. 添加名为 *TeamContext* 的新连接字符串以匹配实体框架数据库上下文类。 粘贴新数据库的连接字符串作为值。 请务必替换连接字符串中的以下占位符，然后单击“保存”： 

    | 占位符 | 建议的值 |
    | --- | --- |
    | *{your_username}* | 使用刚刚创建的数据库服务器的**服务器管理员登录名**。 |
    | *{your_password}* | 使用刚刚创建的数据库服务器的密码。 |

    将用户名和密码添加为应用程序设置后，你的用户名和密码将不会包含在代码中。 这种做法有助于保护这些凭据。

### <a name="publish-the-application-updates-to-azure"></a>将应用程序更新发布到 Azure

在本教程的此步骤，我们将应用程序更新发布到 Azure 并在云中运行应用程序。

1. 在 Visual Studio 中右键单击“ContosoTeamStats”项目，并选择“发布”。  

    ![发布](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. 单击“发布”，使用快速入门中创建的相同发布配置文件。 

3. 完成发布后，Visual Studio 会在默认的 Web 浏览器中启动应用。

    ![添加的缓存](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    下表描述了示例应用程序中的每个操作链接：

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

## <a name="clean-up-resources"></a>清理资源

完成示例性的教程应用程序以后，即可删除所用的 Azure 资源，以便节省成本和资源。 所有资源应包含在同一资源组中。可以通过一个操作将这些资源一并删除：删除该资源组即可。 本主题的说明使用了名为 *TestResources* 的资源组。

> [!IMPORTANT]
> 删除资源组的操作不可逆，资源组以及其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有资源组（其中包含要保留的资源）中为托管此示例而创建了相关资源，则可从各自的边栏选项卡逐个删除这些资源。
>

1. 登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。 
2. 在“筛选项目...”  文本框中键入资源组的名称。
3. 单击资源组右侧的“...”，然后单击“删除资源组”。  

    ![删除](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. 系统会要求确认是否删除资源组。 键入资源组的名称进行确认，然后单击“删除”  。

    片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何缩放用于 Redis 的 Azure 缓存](./cache-how-to-scale.md)
