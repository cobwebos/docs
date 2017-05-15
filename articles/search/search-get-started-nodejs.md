---
title: "Node.js 中的 Azure 搜索入门 | Microsoft Docs"
description: "介绍使用 Node.js 作为编程语言，在 Azure 的托管云搜索服务上生成搜索应用程序的方法。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 32865ed986f5eea961ef2c3813dcc6531498c90a
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---
# <a name="get-started-with-azure-search-in-nodejs"></a>Node.js 中的 Azure 搜索入门
> [!div class="op_single_selector"]
> * [门户](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

了解如何生成使用 Azure 搜索作为搜索体验的自定义 Node.js 搜索应用程序。 本教程使用 [Azure 搜索服务 REST API](https://msdn.microsoft.com/library/dn798935.aspx) 构造用于此练习的对象和操作。

在 Windows 8.1 上使用 [Node.js](https://Nodejs.org)、NPM、[Sublime Text 3](http://www.sublimetext.com/3) 和 Windows PowerShell 开发和测试此代码。

若要运行此示例，必须具有 Azure 搜索服务，可在 [Azure 门户](https://portal.azure.com)注册此服务。 有关分步说明，请参阅 [在门户中创建 Azure 搜索服务](search-create-service-portal.md) 。

## <a name="about-the-data"></a>关于数据
此示例应用程序使用的数据来自 [美国地质调查局 (USGS)](http://geonames.usgs.gov/domestic/download_data.htm)，对罗得岛州进行了筛选以减小数据集大小。 使用此数据生成搜索应用程序，该应用程序返回医院、学校等地标性建筑，以及河流、湖泊和山峰等地质特征。

在此应用程序中， **DataIndexer** 程序通过使用 [索引器](https://msdn.microsoft.com/library/azure/dn798918.aspx) 构造，并从公共 Azure SQL 数据库检索筛选的 USGS 数据集，生成并加载索引。 程序代码中提供了联机数据源的凭据和连接信息。 无需进一步配置。

> [!NOTE]
> 此数据集应用了筛选器，使其保持在免费定价层限定的 10,000 个文档以下。 若使用标准层，则此限制不适用。 有关每个定价层容量的详细信息，请参阅 [搜索服务限制](search-limits-quotas-capacity.md)。
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>查找 Azure 搜索服务的服务名称和 API 密钥
创建服务后，请返回到门户，获取 URL 或 `api-key`。 搜索服务的连接要求具有 URL 和 `api-key` 对调用进行身份验证。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在跳转栏中，单击“搜索服务”，列出为订阅预配的所有 Azure 搜索服务。
3. 选择要使用的服务。
4. 在服务仪表板中会看到基本信息的磁贴，例如用于访问管理密钥的密钥图标。
5. 复制服务 URL、管理密钥和查询密钥。 之后将其添加到 config.js 文件时，需要这三项内容。

## <a name="download-the-sample-files"></a>下载示例文件
使用以下方法之一下载该示例。

1. 转到 [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodejsIndexerDemo)。
2. 单击“下载 ZIP” ，保存该 .zip 文件，然后解压缩其包含的所有文件。

将对此文件夹中的文件进行所有后续文件修改并运行语句。

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>使用搜索服务 URL 和 API 密钥 更新 config.js。
使用之前复制的 URL 和 API 密钥，指定配置文件中的 URL、管理密钥和查询密钥。

管理密钥授予对所有服务操作的完全控制权限，包括创建或删除索引，以及加载文档。 与此相反，查询密钥用于只读操作，通常由连接到 Azure 搜索的客户端应用程序所使用。

在此示例中，将查询密钥包含在内，以帮助增强在客户端应用程序中使用查询密钥的最佳实践。

以下屏幕截图显示在文本编辑器中打开的 **config.js** ，并界定相关项，以便查看在何处可以使用搜索服务的有效值更新文件。

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>托管示例的运行时环境
该示例需要 HTTP 服务器，可以使用 npm 进行全局安装。

使用 PowerShell 窗口运行以下命令。

1. 导航到包含 **package.json** 文件的文件夹。
2. 键入 `npm install`。
3. 键入 `npm install -g http-server`。

## <a name="build-the-index-and-run-the-application"></a>生成索引并运行应用程序
1. 键入 `npm run indexDocuments`。
2. 键入 `npm run build`。
3. 键入 `npm run start_server`。
4. 将浏览器指向 `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>搜索 USGS 数据
USGS 数据集包含罗得岛州的相关记录。 如果在空白搜索框中单击“搜索”，按照默认设置，将获得前 50 个条目。

输入搜索词，为搜索引擎提供可搜索的内容。 尝试输入区域名称。 “Roger Williams”是罗得岛的第一任州长。 许多公园、建筑和学校都以他的名字命名。

![][9]

还可以尝试以下任一词语：

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>后续步骤
这是第一个基于 Node.js 和 USGS 数据集的 Azure 搜索教程。 日后，本教程将进一步扩展，演示自定义解决方案中可能想要使用的其他搜索功能。

如果已对 Azure 搜索有一定的了解，可以将此教程用作尝试使用建议器（提前键入或自动完成查询）、筛选器和分面导航的跳板。 还可以通过添加计数以及批处理文档，改进搜索结果页，以便用户可以翻阅结果。

不熟悉 Azure 搜索？ 建议尝试其他教程，深入了解可以创建哪些内容。 请访问 [文档页](https://azure.microsoft.com/documentation/services/search/) 查找更多资源。 还可以查看 [视频和教程列表](search-video-demo-tutorial-list.md) 中的链接以获取详细信息。

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png

