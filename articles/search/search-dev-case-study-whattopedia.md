---
title: "Azure 搜索开发人员案例研究：WhatToPedia 如何在 Microsoft Azure 上打造信息媒体门户 | Microsoft Docs"
description: "了解如何使用 Azure 搜索（一个面向开发人员的云托管搜索服务）打造信息门户和元搜索引擎。"
services: search, sql-database,  storage, web-sites
documentationcenter: 
author: HeidiSteen
manager: jhubbard
ms.assetid: fbecea71-7534-43ac-8bca-22ef6a19232f
ms.service: search
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 08/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 378ed0fdffcfab016af371ae47a99bd126895993


---
# <a name="azure-search-developer-case-study"></a>Azure 搜索开发人员案例研究
## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>[WhatToPedia.com](http://whattopedia.com/) 如何在 Microsoft Azure 上打造信息媒体门户
 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">绝妙的想法</font> 

我们的想法是构建一个通过高度相关的范围搜索体验将消费者与零售商联系在一起的信息门户，类似于旅游门户如何将处于陌生地区的旅行者与酒店、餐馆和娱乐匹配在一起。 

我们构想的门户将通过给定市场中的零售商数据提供极高质量的搜索体验，帮助购物者根据位置和零售商提供的其他便利设施查找商店。 我们的搜索引擎始于一个初始数据库，但随着时间的推移，在发布其业务相关信息的零售商订户的帮助下，将产生更深层次的价值。 促销、新商品、流行品牌、内部专门服务，这些全部都是为我们的站点增添价值的数据示例。 一旦零售商注册为订户后，此数据将自行报告，并且集成到搜索语料库中。 广告加订阅模式为我们的新业务提供收入流。

在纯云平台上，搜索将是主要的用户交互模型。 出于扩展和低成本的目的，我们所做的一切（从门户体验到源控制）几乎都将通过在线服务完成。 使用为我们提供开箱即得功能的搜索引擎，我们可以快速创建搜索应用程序，而无需自行生成和管理搜索引擎。

## <a name="what-we-built"></a>我们打造了什么
WhatToPedia 是一家信息媒体创业公司。 我们打造了 [WhatToPedia.com](http://whattopedia.com/) - 当前在北欧进行试销，上线日期为 2015 年 2 月 2 日。 我们的客户群主要是实体商店，它们需要一种易于管理和维护且经济实惠的在线存在方式。

我们的任务是通过强大的在线搜索体验吸引购物者，根据城市或社区、品牌、商店名称或商店类型提升结果。 吸引购物者具有连锁反应，推动零售商订阅我们的门户站点。 订阅基于收费制，费率公道合理。

 ![][7] 

注册订阅后，零售商接管其现有配置文件（最初由我们从购买的数据创建），使用关于促销、精选品牌或公告的其他数据更新它。 内部功能（如所说的语言、接受的货币、免税购物）可自行报告，以便更好地吸引正在寻找这些便利设施的购物者。

## <a name="who-we-are"></a>我们是谁
我们的名字叫 Thomas Segato（Microsoft 咨询），我与 WhatToPedia 的首席开发开发人员 Jesper Boelling 合作设计解决方案。 

WhatToPedia 是一家创业公司，正在瑞典试销其新的门户业务，其中 60000 个零售商中大部分都是实体 SME（小型和中型企业）。 我们知道，欧洲的购物者说多种语言并且携带多种货币，因此我们生成了适应多语言购物者的解决方案。 我们在 Azure 搜索中找到了我们所需的支持多语言要求的搜索引擎。

Azure 搜索彻底改变了我们的项目。 在 Azure 搜索发布之前，我们花费了相当多的精力，走了不少弯路，因为要试图打造我们自己的搜索引擎。 使用 Azure 搜索作为在线服务消除了解决方案中最大的技术和管理障碍，这意味着更快地进入市场，并提供更可靠的搜索体验。  

## <a name="how-we-did-it"></a>我们如何做到
我们的愿景是生成仅基于云服务的完整基础结构。 Microsoft 被选为战略平台，因为它是提供了必要服务（对于协作和开发）、按需扩展和实惠价格的提供商。

### <a name="high-level-components"></a>高级组件
我们构建一项业务，而不仅是一个网站。 支持整个事业需要全套的工具和应用程序。 我们采用了 Visual Studio 和 Visual Studio Team Services 用于开发、Team Foundation Service (TFS) Online 用于源代码管理和 scrum 管理、Office 365 用于通信和协作，当然还有 Microsoft Azure 用于所有与站点相关的操作和存储。 借助 Visual Studio，IDE 通过与 TFS Online 的集成提供了到 Azure 的直接预配，从而带来额外的生产力提升。

下图演示 WhatToPedia 基础结构中使用的高级组件。

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>我们如何使用 Microsoft Azure
查看上图中的绿色框，你将看到 WhatToPedia 解决方案基于以下服务生成：

* [Azure 搜索](https://azure.microsoft.com/services/search/)
* [使用 MVC 4 的 Azure 网站](https://azure.microsoft.com/services/websites/)
* [用于计划任务的 Azure WebJobs](../app-service-web/websites-webjobs-resources.md)
* [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)
* [Azure BLOB 存储](https://azure.microsoft.com/services/storage/)
* [SendGrid 电子邮件发送](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

解决方案的核心是数据和搜索。 下面演示了从经销商提供商到最终用户的数据流：

  ![][9]

主要数据存储是 Azure SQL 数据库中的经销商和会计数据。 这包括初始数据库，加上随着时间的推移而添加的特定于零售商的数据。 我们将使用 Azure WebJob 从 SQL 数据库向 Azure 搜索中的搜索语料库发布更新。

### <a name="presentation-layer"></a>表示层
门户是 Azure 网站，在 MVC 4 和 [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29) 中实现。 我们选择 MVC 是因为相比基于 ASP.NET 形式的开发，它提供更干净的 HTML 处理方法。 为了避免为多种设备创建应用并维护多个移动平台，我们选择 Twitter Bootstrap 来支持所有设备和平台。

### <a name="authentication-permissions-and-sensitive-data"></a>身份验证、权限和敏感数据
购物者匿名浏览站点。 因此，对购物者没有登录要求，我们也不存储任何消费者数据。 

零售商则是另外一回事。 我们将存储面向公众的零售商资料信息、帐单信息和他们希望在站点公开的媒体内容。 订阅站点的每个零售商都将获得一个用户登录信息，用于在对订户的配置文件进行更新之前对用户进行身份验证。  我们将所有订户数据安全存储在 Azure SQL 数据库和 Azure BLOB 存储中。
我们选择基于 .NET 形式的身份验证的身份验证模型。 我们选择此方法是因为它的简洁性；我们无需角色、UI 支持和其他方法附带的其他无关功能。 

为了确保零售商仅看到属于他们的数据，我们为每个零售商创建了零售商 ID，此 ID 随后用于涉及到特定于零售商的数据的所有读取和写入操作。 通过此方法，我们发现，我们无需向个别零售商授予数据库权限。 使用零售商 ID 作为数据隔离技术，所有零售商都在单个数据库角色下与系统交互。

由于我们业务的关键是下游效应（推动更多业务流向零售商，创造广告和订阅的动力），因此我们可以不处理网上购物。 因此，你不会在我们的站点上找到购物车，这简化了安全要求。 

我们采用的另一项简化措施是外包帐单和应付账款操作。 通过将客户支付信息直接转给第三方 ([SveaWebPay](http://www.sveawebpay.se/))，我们降低了在数据存储中存储和保护敏感数据的相关风险。 

### <a name="search-engine"></a>搜索引擎
解决方案的核心是基于 Azure 搜索服务生成的搜索引擎。 最初，我们生成了自定义搜索引擎，但在此过程中，我们意识到复杂性和工作量非常高，这促使我们考虑其他备用方案。 

对我们最重要的基本功能包括：

* 筛选器
* 多面导航
* 提升结果
* 通过 AJAX 分页

通过 Internet 搜索，将我们找到了以下视频，启发我们尝试 Azure 搜索：[深入了解 TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

观看此视频后，我们准备好基于我们看到的内容生成一个原型。 由于我们已经在 MVC 中有一个数据模型，因此创建原型很简单，因为数据包含可搜索字词，并且我们已经就如何排序、分面和筛选数据制定了要求。 

以下是我们生成原型的方法。

**配置 Azure 搜索服务**

1. 登录 Azure 经典门户，并将搜索门户添加到我们的订阅。 我们使用了共享版本（对我们的订阅免费）。
2. 创建索引。 对于原型，我们使用门户 UI 定义搜索字段并创建计分概要文件。 我们的计分概要文件基于位置数据：国家/地区 |城市 | 地址（请参阅：添加计分配置文件）。
3. 将服务 URL 和管理员 API 密钥复制到配置文件。 此密钥位于门户中的搜索服务页面上，它用于在登录服务时进行身份验证。

**开发搜索索引器作业 - Windows 控制台**

1. 从数据库读取所有经销商。
2. 调用 Azure 搜索服务 API 来逐个上传经销商（请参阅：http://msdn.microsoft.com/library/azure/dn798930.aspx）。
3. 在数据库中设置一个经销商已编入索引的属性来实现增量索引。 我们通过添加一个存储每个配置文件的索引状态（已或未编入索引）的 ‘indexer’ 字段来实现此目的。 

有关生成索引器作业的代码片段，请参阅附录。

**开发搜索 Web 门户 - MVC**

1. 调用 Azure 搜索服务从搜索中获取所有文档（请参阅：http://msdn.microsoft.com/library/azure/dn798927.aspx）
2. 从搜索服务响应中提取以下内容（通过使用 json.net http://james.newtonking.com/json）
   * 结果
   * 方面
   * 结果计数
   * 开发用于显示搜索结果、分面和计数的用户界面（我们已有）。

这是用于从 Azure 搜索获取结果的代码：

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**按位置提升**

在原型中需要验证的最重要的要求可能包括向查询添加位置搜索关键字。 对门户很重要的一点是，如果用户在搜索查询中输入城市名称，给定城市中的经销商排名将高于在描述中具有城市关键字的经销商。 对于此要求，我们使用计分概要文件使城市字段的排名高于其他字段。

**支持多种语言**

我们需要使用正确的语言显示正确的搜索结果，并提供使用不同的语言查找相同结果的选项。 这个问题有两个方面： 

* 使用多种语言搜索字词
* 使用正确的语言显示搜索结果

我们通过使用本地化文本为每种语言添加文档并使用该语言添加属性解决了展示方面的问题。 当用户输入搜索词时，我们使用 `$filter` 表达式筛选用户所选的语言。

每个文档都在集合类型上生成了名为“cities”的隐藏属性。 此属性使用所有语言存储城市名称，使用户能够使用多种语言搜索。

### <a name="data-storage"></a>数据存储
所有数据（配置文件、订阅和会计）都存储在 SQL 数据库中。 所有媒体文件都存储在 Azure BLOB 存储中，包括零售商提供的图像和视频。 使用单独的 BLOB 存储可隔离上传文件的效果；文件从不与网站混合，因此我们无需每次在添加文件时都重新生成站点。

我们的存储设计的一个重要优势是，多个开发人员可以共享单个开发存储。 WhatToPedia 项目的要求之一是能够在 15 分钟内创建开发环境，包括经销商数据、图像和视频。 通过从 TFS Online 获取最新数据、运行 SQL 脚本和运行导入作业，可以立即建立完整的环境。 此做法还改进了临时过程。

### <a name="webjobs"></a>Web 作业
我们使用 Azure WebJobs 将数据更新到索引。 通过创建搜索索引器作业，索引部分非常易于集成到解决方案中。 为了使索引器作业适应，我们所作的唯一代码更改是将 `Indexed` 字段添加到数据模型以指示索引状态。 每当添加或更新新配置文件时，`Indexed` 字段设置为 false。 这同样适用于零售商通过门户更改他或她的配置文件数据的情况。  

作业查找将 `Indexed` 设置为 false 的所有行。 当它找到行时，文档发布到 Azure 搜索，然后 `Indexed` 字段设置为 true。 我们无需计划添加和更新数据，因为 Azure 搜索服务实际上负责这些操作。 如果添加已经存在的文档，服务将自动进行更新。

所有 Web 作业都已开发为控制台应用程序，这些应用程序可作为 ZIP 文件上传到 Azure 网站、解压缩，然后进行计划。

作业计划作为计划 Web 任务每 5 分钟运行。 我们计算出，服务需要大约 3 分钟来上传 3000 个文档，这在我们的要求内。 

> [!NOTE]
> Azure 搜索中最近引入了原型索引器功能。 此功能来得太晚，我们无法在第一个版本中使用它，但它看起来可以解决我们使用索引器作业解决的相同问题，即自动执行数据加载操作。
> 
> 

### <a name="backup-strategy"></a>备份策略
我们设计了多层备份策略以便从多种场景中恢复，从灾难性故障到个别事务的恢复。 要保护的资产包括三种类型的数据（网站、订户数据和媒体文件）。 

首先，通过将网站源代码保留在 TFS Online 中，我们知道，如果站点出现故障，可通过从 TFS 重新发布来重新生成它。 

Azure SQL 数据库中的订户数据是最敏感的资产。 我们使用内置功能备份它（请参阅 [Azure SQL 数据库备份和还原](http://msdn.microsoft.com/library/azure/jj650016.aspx)）。 备份计划是每周一次完整数据库备份、每天一次差异数据库备份、每 5 分钟进行事务日志备份。  在给定数据大小的情况下，此解决方案非常适合我们的即时和预计数据卷。

第三，我们将图像和视频文件存储在 Azure BLOB 存储中。 我们仍然在评估此数据的最终备份计划，并将 Cloudberry Explorer for Azure 作为潜在的解决方案来考虑。 暂时，我们使用 WebJob 将图像和视频复制到其他位置。

## <a name="what-we-learned"></a>我们学到了什么
由于我们已经有数据，因此建立概念证明很容易。 在数小时内，我们有了一个带有分面和计数器、分页、排名配置文件和搜索结果的原型。 搜索结果如此精确，以至于我们决定删除一些向最终用户显示的筛选器。 

对我们来说最大的惊喜是，我们可以如此快速地学会 Azure 搜索，并且回报如此之大。 在字面意义上，我们在几个小时内建立了概念证明（请参阅下面的备注）、在前端应用程序中用 3 行代码替换了 500 行代码（加上新的 WebJob）并得到更好的结果。 

之前，我们的代码实现了分页、计数和其他搜索的标准行为。 使用 Azure 搜索，我们收获的成果包括搜索词、分面、分页数据、计数，我们本来必须自己提供的所有必需的内容。 它还包括提升和内置分面导航，这些原本不在原始的解决方案中。

在实现过程中最大的挑战是，它是一个预览版，查找信息和分享经验很困难。 理清一些头绪后，我们发现使用 Azure 搜索非常简单，因为它采用 REST API 和 JSON 数据格式。 我们可以从最开源的插件（如 JQuery JSON.Net）直接调用框架，并且可以使用 Fiddler 之类的工具进行快速实验和调试。 

> [!NOTE]
> 除了准备数据，我们的成员已经了解搜索技术的工作原理，这一点很有帮助，这使我们更高效，并且更感激内置功能。 如果你需要逐步了解搜索查询构造、分面导航、筛选器等，则应该预期原型制作花费更长时间。 
> 
> 

### <a name="controlling-facets-in-the-search-presentation-page"></a>控制搜索演示页中的分面
在概念证明过程中我们学到的经验之一是，在前端仔细规划分面。 将大量数据加载到解决方案后，我们看到分面的量过大，无法向用户显示。 

我们通过约束分面计数参数解决了此问题。 计数参数对返回给用户的分面数施加硬限制。 可在[此处](search-faceted-navigation.md)找到包含计数参数讨论的链接。

### <a name="webjobs-for-scheduling-tasks"></a>用于计划任务的 WebJobs
Azure 搜索不是唯一的惊喜。 我们发现，使用 WebJobs 自行执行对 Azure 搜索的数据加载操作大大优于我们之前的方法，之前需要使用运行 Windows 计划程序的专用 VM，通过计划任务更新搜索索引。 WebJobs 易于配置和调试，并且相比必须为专用 VM 付费，它当然也更便宜。

### <a name="azure-blob-storage-explorer-for-updating-images"></a>用于更新图像的 Azure BLOB 存储资源管理器
我们发现，使用 [Azure BLOB 存储资源管理器](https://azurestorageexplorer.codeplex.com/)（在 codeplex 上提供）在管理对站点的图像和视频更新方面非常有帮助。 我们将它用作手动更新主站点上的图像和视频的工具。 我们发现它比部署对门户的更改更灵活，并且无需在每次需要更新图像时都进行完整的测试迭代。 

## <a name="a-few-final-words"></a>最后的一些话
感谢 WhatToPedia 的好伙计允许我们分享他们的故事！  

我们希望你觉得这个案例研究有用。 如果你继续使用 Azure 搜索，我推荐一些资源来加快你的速度：

* [专用于 Azure 搜索的 MSDN 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
* [StackOverflow 也有标记](http://stackoverflow.com/questions/tagged/azure-search)
* [Azure.com 上的文档页面](https://azure.microsoft.com/documentation/services/search/)
* [MSDN 上 Azure 搜索文档](http://msdn.microsoft.com/library/azure/dn798933.aspx)

## <a name="appendix-search-indexer-webjob"></a>附录：搜索索引器 WebJob
以下代码生成有关生成原型的部分中提到的索引器。

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            

            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }

        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }

                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            



<!--Anchors-->
[副标题 1]: #subheading-1
[副标题 2]: #subheading-2
[副标题 3]: #subheading-3
[副标题 4]: #subheading-4
[副标题 5]: #subheading-5
[后续步骤]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[链接 1 指向另一个 azure.microsoft.com 文档主题]: ../virtual-machines-windows-hero-tutorial.md
[链接 2 指向另一个 azure.microsoft.com 文档主题]: ../web-sites-custom-domain-name.md
[链接 3 指向另一个 azure.microsoft.com 文档主题]: ../storage-whatis-account.md




<!--HONumber=Nov16_HO3-->


