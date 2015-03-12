<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="将分页添加到数据 (Android) | 移动开发人员中心" metaKeywords="" description="了解如何使用分页来管理从移动服务返回到 Android 应用程序的数据量。" metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="ricksal" solutions="" manager="" editor="" />



# 使用分页优化移动服务查询

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

本主题说明如何使用分页来管理从 Azure 移动服务返回给 Android 应用程序的数据量。在本教程中，你将在客户端上使用 **top** 和 **skip** 查询方法来请求特定的数据"页"。

<div class="dev-callout"><b>注意</b>
<p>为了防止移动设备客户端上发生数据溢出，移动服务实施了自动页限制，该限制默认为每个响应中最多 50 个项。通过指定页大小，你最多可以在响应中显式请求 1,000 个项。</p>
</div>

本教程以前一教程[数据处理入门]中的步骤和示例应用程序为基础。在开始学习本教程之前，最起码需要先完成数据处理系列中的第一篇教程，即[数据处理入门]。 

1. 在 Eclipse 中，打开你在完成[数据处理入门]教程后创建的项目。

2. 在"运行"菜单中单击"运行"以启动应用程序，在文本框中键入文本，然后单击"添加"按钮。

3. 重复以上步骤至少三次，因此你将在 TodoItem 表中存储三个以上的项。 

4. 在 ToDoActivity.java 文件中，将 **RefreshTodoItems** 方法替换为以下代码：

		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

  	此查询将返回未标记为已完成的前面三个项。

5. 重新生成并启动应用程序。 
   
    请注意，仅显示 TodoItem 表的前三个结果。 

6. （可选）使用消息检查软件（例如浏览器开发人员工具或 [Fiddler]）来查看发送到移动服务的请求的 URI。 

   	请注意， `top(3)` 方法已转换成查询 URI 中的查询选项"$top=3"。

7. 使用以下代码，再一次更新 **RefreshTodoItems** 方法：
            
		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).skip(3).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

   	此查询将跳过前三个结果，返回其后的三个结果。实际上这是数据的第二"页"，其页大小为三个项。

    <div class="dev-callout"><b>注意</b>
    <p>本教程将硬编码分页值传递给 <strong>top</strong> 和 <strong>skip</strong> 方法，因此使用的是简化的方案。在实际应用中，你可以对页导航控件或类似的 UI 使用类似于上面的查询，让用户导航到上一页和下一页。你还可以调用 <strong>includeInlineCount</strong> 方法，以获取服务器上的可用项总数，以及分页的数据。</p>
    </div>

8. （可选）再次查看发送到移动服务的请求的 URI。 

   	请注意， `skip(3)` 方法已转换成查询 URI 中的查询选项 `$skip=3`。

## <a name="next-steps"> </a>后续步骤

演示移动服务中数据处理基础知识的系列教程到此结束。建议你了解有关以下移动服务主题的详细信息：

* [身份验证入门]
  <br/>了解如何使用 Windows 帐户对应用程序用户进行身份验证。
 
* [推送通知入门]
  <br/>了解如何向应用程序发送一条非常简单的推送通知。

<!-- Anchors. -->

[Next Steps]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-android
[数据处理入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-with-data-android
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-with-users-android
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-with-push-android

[管理门户]: https://manage.windowsazure.cn/

