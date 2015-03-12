<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="使用 .Net 后端验证和修改数据（Windows 应用商店）| 移动开发人员中心" metaKeywords="" description="了解如何使用 .Net 后端 Microsoft Azure 移动服务验证、修改和补充 Javascript Windows 应用商店应用程序的数据。" metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />



# 使用 .NET 后端在移动服务中验证和修改数据

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

本主题说明如何在 .Net 后端 Azure 移动服务中使用代码来验证和修改数据。.NET 后端服务是使用 Web API 框架生成的 HTTP 服务。如果你熟悉通过 Web API 框架定义的 `ApiController` 类的话，就会发现移动服务提供的 `TableController` 类非常直观。 `TableController` 派生自 `ApiController` 类，它提供与数据库表进行连接的附加功能。你可以使用它对插入和更新的数据执行操作，包括本教程中演示的验证和数据修改。 

本教程将指导你完成以下基本步骤：

1. [添加字符串长度验证]
2. [更新客户端以支持验证]
3. [测试长度验证]
4. [为 CompleteDate 添加时间戳字段]
5. [更新客户端以显示 CompleteDate]

本教程建立在前面的[入门]或[数据处理入门](/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/)教程中的步骤和示例应用程序的基础之上。在开始学习本教程之前，必须先完成[入门]或[数据处理入门](/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/)教程。  

## <a name="string-length-validation"></a>向移动服务添加验证代码

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>更新客户端

移动服务已设置为验证数据并针对无效的文本长度发送错误响应，因此你需要更新你的应用程序，使之能够处理验证后生成的错误响应。客户端应用程序将通过调用 `IMobileServiceTable<TodoItem].InsertAsync()` 捕获该错误。

1. 在 Visual Studio 的"解决方案资源管理器"窗口中，导航到 JavaScript 客户端项目并展开 **js** 文件夹。然后打开 default.js 文件

2. 在 default.js 中，将现有的 **insertTodoItem** 函数替换为以下函数定义：


        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoItems.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

   	此版本的函数包括错误处理功能并显示一个 `MessageDialog`，其中包含来自响应的错误消息、状态文本和状态代码。

## <a name="test-length-validation"></a>测试长度验证

1. 在 Visual Studio 的解决方案资源管理器中，右键单击 JavaScript 客户端应用程序项目，然后依次单击"调试"和"启动新实例"。

2. 为新的 todo 项输入长度超过 10 个字符的文本，然后单击"保存"。

    ![][1]

3. 此时将出现一个类似于下面的消息对话框，以响应无效文本。

    ![][2]

## <a name="add-timestamp"></a>为 CompleteDate 添加时间戳字段


[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>更新客户端以显示 CompleteDate

最后一步是更新客户端以显示新的 **CompleteDate** 数据。 


1. 在 Visual Studio 的解决方案资源管理器中，在 JavaScript 客户端项目中打开 default.html 文件。将绑定模板 `div` 标记元素替换为以下定义。然后保存该文件。这将会添加一个 `div` 标记，其 innerText 属性已绑定到 **completeDate**。
	      
        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: 3">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
              dataContext: this" />
            <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
              data-win-bind="innerText: text">
            </div>
            <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
              data-win-bind="innerText: completeDate">
            </div>
          </div>
        </div>



2. 在 default.js 中，删除现有 **refreshTodoItems** 函数中的 `.Where` 子句函数，以便在结果中包含已完成的 todoitem。

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };


3. 在 default.js 中，按如下所示更新 **updateCheckedTodoItem** 函数，以便在更新后刷新项，并且不会从列表中删除已完成的项。然后保存该文件。	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. 在 Visual Studio 的"解决方案资源管理器"窗口中，右键单击"解决方案"，然后单击"重新生成解决方案"以重新生成客户端和 .NET 后端服务。验证是否生成了这两个项目且未出错。

    ![][3]
	
5. 按 **F5** 键在本地运行客户端应用程序和服务。添加一些新项，并通过单击将某些项标记为已完成，以查看是否会更新 **CompleteDate** 时间戳。

    ![][4]

6. 在 Visual Studio 的解决方案资源管理器中，右键单击 todolist 服务项目并单击"发布"。使用你从 Azure 门户下载的发布设置文件将 .NET 后端服务发布到 Microsoft Azure。

7. 取消注释与移动服务地址建立的连接，以更新客户端项目的 default.js 文件。针对 Azure 帐户中托管的 .NET 后端测试该应用程序。




## <a name="next-steps"> </a>后续步骤

现在你已完成本教程，建议你继续学习数据系列中的最后一篇教程：[使用分页优化查询]。

在为用户授权以及发送推送通知时，也可以使用服务器脚本。有关详细信息，请参阅以下教程：

* [用户的服务端授权]
  <br/>了解如何基于某个已经过身份验证的用户的 ID 筛选数据。

* [推送通知入门]
  <br/>了解如何向应用程序发送一条非常简单的推送通知。

* [移动服务 .NET 操作方法概念性参考]
  <br/>了解有关如何将移动服务与 .NET 一起使用的详细信息。

<!-- Anchors. -->
[添加字符串长度验证]: #string-length-validation
[更新客户端以支持验证]: #update-client-validation
[测试长度验证]: #test-length-validation
[为 CompleteDate 添加时间戳字段]: #add-timestamp
[更新客户端以显示 CompleteDate]: #update-client-timestamp
[后续步骤]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/#create-new-service
[用户的服务端授权]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[使用分页优化查询]: /zh-cn/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data
[入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[开始使用数据]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript 和 HTML]: /zh-cn/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts

[管理门户]: https://manage.windowsazure.cn/
[Azure 管理门户]: https://manage.windowsazure.cn/
[移动服务 .NET 操作方法概念性参考]: /zh-cn/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
