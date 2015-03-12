<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="数据处理入门 (Android) | 移动开发人员中心" metaKeywords="Azure android data, Azure mobile services data, windows droid, windows android, microsoft droid, microsoft android" description="了解如何开始使用移动服务来利用 Android 应用程序中的数据。" metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="" editor="" />



# 将移动服务添加到现有应用程序

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主题说明如何通过 Azure 移动服务来利用 Android 应用程序中的数据。在本教程中，你将要下载一个可在内存中存储数据的应用程序，创建一个新的移动服务，将该移动服务与该应用程序相集成，然后登录到 Azure 管理门户以查看运行该应用程序时对数据所做的更改。</p>

</div>
<div class="dev-onpage-video-wrapper" style="display:none"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">观看教程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放视频</span></a><span class="time">15:32</span></div>
</div>

<div class="dev-callout"><b>注意</b>
<p>本教程旨在帮助你更好地了解如何使用移动服务并通过 Azure 来存储数据以及从 Android 应用程序检索数据。因此，本主题指导你完成的许多步骤已在移动服务快速入门中代你完成。如果这是你第一次体验移动服务，请考虑首先完成<a href="/zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-android">移动服务入门</a>教程。</p>
<p>如果要查看已完成应用程序的源代码，请转到<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData">此处</a>。
</p>
</div>

本教程将指导你完成以下基本步骤：

1. [下载 Android 应用程序项目]
2. [创建移动服务]
3. [添加用于存储的数据表]
4. [更新应用程序以使用移动服务]
5. [针对移动服务测试应用程序]

<div class="dev-callout"><strong>注意</strong> <p>若要完成本教程，你需要一个 Azure 帐户。如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。有关详细信息，请参阅 <a href="http://www.windowsazure.cn/zh-cn/pricing/1rmb-trial/?WT.mc_id=AED8DE357" target="_blank">Azure 免费试用</a>。</p></div> 

本教程需要安装[移动服务 Android SDK]；<a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android SDK</a>，其中包含 Eclipse 集成开发环境 (IDE) 和 Android 开发人员工具 (ADT) 插件；以及 Android 4.2 或更高版本。 

<div class="dev-callout"><b>注意</b>
<p>本教程提供了 Android SDK 和移动服务 Android SDK 的安装说明。下载的 GetStartedWithData 项目需要 Android 4.2 或更高版本。但是，移动服务 SDK 只需要 Android 2.2 或更高版本。</p>
</div>

>[AZURE.NOTE] 本教程使用最新版本的移动服务 SDK。你可以在<a href="http://go.microsoft.com/fwlink/p/?LinkID=280126">此处</a>找到旨在向后兼容的早期版本，但这些教程中包含的代码并不适用于这些版本。

<h2><a name="download-app"></a>下载 GetStartedWithData 项目</h2>

### 获取示例代码

[WACOM.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

### 验证 Android SDK 版本

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]


### 检查并运行示例代码

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="create-service"></a>在管理门户中创建新的移动服务</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>将新表添加到移动服务</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>更新应用程序以使用移动服务进行数据访问</h2>

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]


<h2><a name="test-app"></a>针对新移动服务测试应用程序</h2>

更新应用程序以使用移动服务作为后端存储后，便可以使用 Android 模拟器或 Android 手机针对移动服务测试该应用程序。

1. 在"运行"菜单中，单击"运行"以启动项目。

	这将执行使用 Android SDK 构建的应用，该应用使用客户端库发送一个查询，该查询从你的移动服务返回项目。

5. 和前面一样，输入有意义的文本，然后单击"添加"。

   	此时会将一个新项作为 insert 发送到移动服务。

3. 在管理门户中单击"移动服务"，然后单击你的移动服务[]。

4. 单击"数据"选项卡，然后单击"浏览"。

   	![][9]
  
   	请注意，**TodoItem** 表现在包含了数据以及由移动服务生成的某些值，并且已在该表中自动添加了列，以匹配应用程序中的 TodoItem 类。

针对 Android 的**数据处理入门**教程到此结束。

## <a name="next-steps"> </a>后续步骤

本教程演示了有关如何使 Android 应用程序处理移动服务中的数据的基础知识。 

接下来，建议你完成下列教程之一，这些教程是基于本教程中创建的 GetStartedWithData 应用程序制作的：

* [使用脚本验证和修改数据]
  <br/>了解更多有关使用移动服务中的服务器脚本验证和更改从应用程序发送的数据的信息。

* [使用分页优化查询]
  <br/>了解如何使用查询中的分页控制单个请求中处理的数据量。

完成数据处理系列教程后，请尝试学习以下其他 Android 教程：

* [身份验证入门]
	<br/>了解如何对应用程序用户进行身份验证。

* [推送通知入门]
  <br/>了解如何使用移动服务将非常基本的推送通知发送到应用程序。

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[使用脚本验证和修改数据]: /zh-cn/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts
[使用分页优化查询]: /zh-cn/documentation/articles/mobile-services-android-add-paging-data
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-android
[数据处理入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-with-data-android
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-with-users-android
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-with-push-android

[Azure 管理门户]: https://manage.windowsazure.cn/
[管理门户]: https://manage.windowsazure.cn/
[移动服务 Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
