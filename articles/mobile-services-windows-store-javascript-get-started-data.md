<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="数据处理入门" pageTitle="数据处理入门（Windows 应用商店 JavaScript）| 移动开发人员中心" metaKeywords="" description="了解如何开始使用移动服务来利用 Windows 应用商店应用程序中的数据。" metaCanonical="https://www.windowsazure.cn/zh-cn/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />


# 将移动服务添加到现有应用程序

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend">.NET 后端</a> | 
	<a href="/zh-cn/documentation/articles/mobile-services-windows-store-javascript-get-started-data/"  title="JavaScript backend" class="current">JavaScript 后端</a>
</div>


此主题说明如何通过 Azure 移动服务来利用 Windows 应用商店应用程序中的数据。在本教程中，你将要下载一个可在内存中存储数据的 Visual Studio 2013 应用程序项目，创建一个新的移动服务，将该移动服务与该应用程序相集成，然后登录到 Azure 管理门户以查看运行该应用程序时对数据所做的更改。

>[AZURE.NOTE]本主题演示如何使用 Visual Studio 2013 将 Azure 移动服务添加到 Windows 应用商店项目。你可以向一个通用的 Windows 应用程序项目中添加相同的 JavaScript 后端移动服务。有关详细信息，请参阅本教程的[通用 Windows 应用程序版本](/zh-cn/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data)。

本教程将指导你完成以下基本步骤：

1. [下载 Windows 应用商店应用程序项目][获取 Windows 应用商店应用程序] 
2. [创建移动服务]
3. [添加用于存储的数据表]
4. [更新应用程序以使用移动服务]
5. [针对移动服务测试应用程序]

若要完成本教程，你需要以下各项：

* 有效的 Azure 帐户。如果你没有帐户，可以创建一个免费的试用帐户，只需几分钟即可完成。有关详细信息，请参阅 [Azure 免费试用](/zh-cn/pricing/1rmb-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.cn%2Fzh-cn%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F)。
* Visual Studio 2013，使用它可以轻松地将 Windows 应用商店应用程序连接到移动服务。若要使用 Visual Studio 2012 完成相同的基本过程，请遵循本主题中的步骤 <a href="/zh-cn/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012">使用 Visual Studio 2012 的移动服务中的数据处理入门</a>。 

<h2><a name="download-app"></a>下载 GetStartedWithData 项目</h2>

本教程基于 [GetStartedWithMobileServices 应用程序][开发人员代码示例网站]，后者是 Visual Studio 2013 中的 Windows 应用商店应用程序项目。此应用程序的 UI 与移动服务快速入门中生成的应用程序相同，不过，前者的一些新增项本地存储在内存中。  

1. 从[开发人员代码示例站点][开发人员代码示例网站]下载 GetStartedWithData 示例应用程序的 JavaScript 版本。 

2. 在 Visual Studio 2012 Express for Windows 8 中打开下载的项目，然后展开 **js** 文件夹并检查 default.js 文件。

   	请注意添加的 **TodoItem** 对象存储在内存中的 **List** 对象中。

3. 按 **F5** 键重新生成项目并启动应用程序。

4. 在应用的"插入 TodoItem"中键入一些文本，然后单击"保存"。

   	![][0]  

   	可以看到，保存的文本已显示在"查询和更新数据"下的第二列中。

## <a name="create-service"></a>从 Visual Studio 中创建新的移动服务

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>在解决方案资源管理器中，展开 **services**、**mobile services**、**&lt;<your_service>&gt;** 文件夹，打开 service.js 脚本文件，并留意新的全局变量，如以下示例所示：</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.cn/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>此代码通过使用一个全局变量提供对应用程序中新移动服务的访问权限。客户端是通过提供新移动服务的 URI 和应用程序密钥来创建的。由于对此脚本的引用已添加到 default.html 文件中，因此该变量可用于也从此页引用的所有脚本文件。</p>
</li>
</ol>

## <a name="add-table"></a>添加数据存储的新表

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[WACOM.NOTE]将使用 Id、__createdAt、__updatedAt 和 __version 列创建新表。启用动态架构后，移动服务将基于插入或更新请求中的 JSON 对象自动生成新列。有关详细信息，请参阅[动态架构](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx)。

# <a name="update-app"></a>更新应用程序以使用移动服务

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

## <a name="test-app"></a>针对新移动服务测试应用程序

1. 在 Visual Studio 中，按 F5 键运行应用。

2. 如前所述，在"插入 TodoItem"中键入文本，然后单击"保存"。

   	此时会将一个新项作为 insert 发送到移动服务。

3. 在[管理门户]中单击"移动服务"，然后单击你的移动服务。

4. 单击"数据"选项卡，然后单击"浏览"。

   	![][9]
  
   	可以看到，**TodoItem** 表现在包含了数据以及移动服务生成的 ID 值，并且已在该表中自动添加了列，以匹配应用程序中的 TodoItem 类。

5. 在应用程序中，选中列表中的某个项，然后返回到门户中的"浏览"选项卡并单击"刷新"。 

  	可以看到，整个值已从 **false** 更改为 **true**。

6. 在 default.js 项目文件中，将现有 **RefreshTodoItems** 函数替换为用于筛选出已完成项目的以下代码：

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. 在应用程序中，选中列表中的另一个项目，然后单击"刷新"按钮。

   	可以看到，选中的项现已从列表中消失。每次刷新都会导致往返访问移动服务，随后返回筛选的数据。

**数据处理入门**教程到此结束。

## <a name="next-steps"> </a>后续步骤

本教程演示了有关如何使 Windows 应用商店应用程序处理移动服务中的数据的基础知识。接下来，建议你完成下列教程之一，这些教程是基于本教程中创建的 GetStartedWithData 应用程序制作的：

* [使用脚本验证和修改数据]
  <br/>了解更多有关使用移动服务中的服务器脚本验证和更改从应用程序发送的数据的信息。

* [使用分页优化查询]
  <br/>了解如何使用查询中的分页控制单个请求中处理的数据量。

完成数据系列教程后，请试着学习下列教程之一：

* [身份验证入门]
  <br/>了解如何对应用程序用户进行身份验证。

* [推送通知入门] 
  <br/>了解如何向应用程序发送一条非常简单的推送通知。

* [移动服务 HTML/JavaScript 操作方法概念性参考]
  <br/>了解有关如何将移动服务与 HTML 和 JavaScript 一起使用的详细信息。

<!-- Anchors. -->

[获取 Windows 应用商店应用程序]: #download-app
[创建移动服务]: #create-service
[添加用于存储的数据表]: #add-table
[更新应用程序以使用移动服务]: #update-app
[针对移动服务测试应用程序]: #test-app
[后续步骤]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[使用脚本验证和修改数据]: /zh-cn/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[使用分页优化查询]: /zh-cn/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[数据处理入门]: /zh-cn/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Azure 管理门户]: https://manage.windowsazure.cn/
[管理门户]: https://manage.windowsazure.cn/
[移动服务 SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[开发人员代码示例网站]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[移动服务 HTML/JavaScript 操作方法概念性参考]: /zh-cn/documentation/articles/mobile-services-html-how-to-use-client-library/
