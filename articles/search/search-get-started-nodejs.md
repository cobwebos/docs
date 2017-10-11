---
title: "在 Node.js 的 Azure 搜索入门 |Microsoft 文档"
description: "指导你完成为您的编程语言使用 Node.js 的 Azure 上构建上托管的云搜索服务的搜索应用程序。"
services: search
documentationcenter: 
author: EvanBoyle
manager: pablocas
editor: v-lincan
ms.assetid: 0625dc1b-9db6-40d5-ba9a-4738b75cbe19
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 04/26/2017
ms.author: evboyle
ms.openlocfilehash: 32865ed986f5eea961ef2c3813dcc6531498c90a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-search-in-nodejs"></a>在 Node.js 的 Azure 搜索入门
> [!div class="op_single_selector"]
> * [门户](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

了解如何构建的自定义 Node.js 搜索应用程序使用 Azure 搜索其搜索体验。 本教程使用[Azure 搜索服务 REST API](https://msdn.microsoft.com/library/dn798935.aspx)构造的对象和在本练习中使用的操作。

我们使用[Node.js](https://Nodejs.org)和 NPM， [Sublime 文本 3](http://www.sublimetext.com/3)，并在开发和测试此代码的 Windows 8.1 上的 Windows PowerShell。

若要运行此示例，你必须具有 Azure 搜索服务，你可以注册在[Azure 门户](https://portal.azure.com)。 请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)有关分步说明。

## <a name="about-the-data"></a>有关的数据
此示例应用程序使用来自数据[美国地质服务 (USG)](http://geonames.usgs.gov/domestic/download_data.htm)、 筛选上状态的罗德岛来减小数据集大小。 我们将使用此数据来生成返回如医院和学校，以及流、 湖泊和峰等的地质功能的路标栋建筑物的搜索应用程序。

在此应用程序， **DataIndexer**程序生成并加载索引使用[索引器](https://msdn.microsoft.com/library/azure/dn798918.aspx)构造，从公共 Azure SQL 数据库中检索筛选的 USG 数据集。 凭据和连接到联机数据源的信息提供在程序代码中。 任何进一步的配置不是必需的。

> [!NOTE]
> 我们在此数据集，以随时个免费定价层的 10,000 文档限制上应用筛选器。 如果使用标准层，此限制不适用于。 有关每个定价层的容量的详细信息，请参阅[搜索服务限制](search-limits-quotas-capacity.md)。
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>查找服务名称和你的 Azure 搜索服务的 api 密钥
创建服务后，返回到门户获取的 URL 或`api-key`。 连接到你的搜索服务要求您具有两个 URL 和`api-key`进行身份验证调用。

1. 登录到[Azure 门户](https://portal.azure.com)。
2. 在跳转栏中，单击**搜索服务**若要列出针对你的订阅设置的所有 Azure 搜索服务。
3. 选择你想要使用的服务。
4. 在服务仪表板，你会看到有关基本信息，例如用于访问管理密钥的密钥图标的磁贴。
5. 复制服务 URL、 管理密钥，以及是查询密钥。 稍后你需要所有三个时将它们添加到 config.js 文件。

## <a name="download-the-sample-files"></a>下载示例文件
使用下列方法之一其中任意一个，若要下载示例。

1. 转到[AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodejsIndexerDemo)。
2. 单击**下载 ZIP**和保存该.zip 文件，然后将它包含的所有文件都解压缩。

对此文件夹中的文件进行所有后续文件修改和运行的语句。

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>更新 config.js。 使用你的搜索服务 URL 和 api 密钥
在配置文件中使用的 URL 和更早版本，复制的 api 密钥指定管理密钥，并查询密钥的 URL。

管理密钥授予对服务操作，包括创建或删除索引和加载文档的完全控制。 与此相反，查询密钥适用于只读操作，通常由客户端应用程序连接到 Azure Search。

在此示例中，我们包括查询密钥，以帮助您进一步使用客户端应用程序中的查询密钥的最佳做法。

以下屏幕快照显示**config.js**打开在文本编辑器中，使用方式，这样您可以看到使用你的搜索服务对有效的值更新文件的位置来划分的相关条目。

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>主机运行时环境的示例
该示例需要 HTTP 服务器，可以全局使用 npm 进行安装。

使用下列命令的 PowerShell 窗口。

1. 导航到包含的文件夹**package.json**文件。
2. 键入 `npm install`。
3. 键入 `npm install -g http-server`。

## <a name="build-the-index-and-run-the-application"></a>生成索引并运行应用程序
1. 键入 `npm run indexDocuments`。
2. 键入 `npm run build`。
3. 键入 `npm run start_server`。
4. 直接在浏览器`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>搜索 USG 数据
USG 数据集包括到状态的罗德岛相关的记录。 如果你单击**搜索**上一个空的搜索框中，你获得的前 50 个条目，这是默认设置。

输入的搜索词使搜索引擎转的内容。 请尝试输入区域的名称。 "Roger 威廉"已罗德岛的第一个调控器。 许多停止、 建筑物和学校命名他。

![][9]

你可以尝试的任一这些项：

* Pawtucket
* Pembroke
* goose + 佛得角

## <a name="next-steps"></a>后续步骤
这是基于 Node.js 和 USG 数据集的第一个 Azure Search 教程。 随着时间推移，我们将扩展在本教程演示了可能想要使用自定义解决方案中的其他搜索功能。

如果你已有 Azure 搜索中的一些背景知识，可用于试用建议器 （提前键入或自动完成查询）、 筛选和分面导航 springboard 作为使用此示例。 你还可以通过添加计数和批处理文档，以便用户可以逐页查看结果改进了搜索结果页。

新 Azure 搜索？ 我们建议尝试其他教程以了解你可以创建。 请访问我们[文档页](https://azure.microsoft.com/documentation/services/search/)若要查找更多资源。 你还可以查看中的链接我们[视频和教程列表](search-video-demo-tutorial-list.md)访问的详细信息。

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
