<properties pageTitle="服务端授权 (iOS) | 移动开发人员中心" metaKeywords="" description="了解如何在 Azure 移动服务的 .NET 后端中为用户授权。" metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

# 移动服务用户的服务端授权

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

本主题说明如何为已经过身份验证的用户授权，使其能够从 iOS 应用程序访问 Azure 移动服务中的数据。在本教程中，你将要在控制器中添加数据访问方法的代码，用于根据已经过身份验证的用户的 userId 筛选查询，确保每个用户只会看到自己的数据。

本教程是在移动服务快速入门和前面的[身份验证入门]教程的基础上制作的。在开始本教程之前，必须先完成[身份验证入门]。  

## <a name="register-scripts"></a>修改数据访问方法

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]


## 测试应用程序

1. 在 Xcode 中，打开你在完成[身份验证入门]教程后修改的项目。

2. 按"运行"按钮以生成项目，在 iPhone 模拟器中启动应用，然后使用你选择的标识提供程序登录。

   	你会发现，尽管 TodoItem 表中已存在完成前面的教程后创建的项，但并没有返回任何项。发生此情况的原因是，以前插入项时并未使用 userId 列，而现在这些列的值为 null。

3. 在应用程序中的"插入 TodoItem"内输入文本，然后单击"保存"。

   	![][3]

   	这将会在移动服务的 TodoItem 表中插入文本和 userId。由于新项包含了正确的 userId 值，因此移动服务会返回该项，并在第二列显示它。

5. 返回到[管理门户][Azure Management Portal]中的"todoitem"表，单击"浏览"，并检查每个新增的项现在是否具有关联的 userId 值。

6. （可选）如果你有其他登录帐户，可以通过关闭然后重新运行应用程序，来验证用户是否只能看到他们自己的数据。显示登录凭据对话框时，请输入一个不同的登录名，然后检查在前一帐户下输入的项是否未显示。

<!--## Next steps

This concludes the tutorials that demonstrate the basics of working with authentication. Consider finding out more about the following Mobile Services topics:

* [Get started with data]
  <br/>Learn more about storing and querying data using Mobile Services.

* [Get started with push notifications]
  <br/>Learn how to send a very basic push notification to your app.

* [Mobile Services .NET How-to Conceptual Reference]
  <br/>Learn more about how to use Mobile Services with .NET.
-->

<!-- Anchors. -->
[注册服务器脚本]: #register-scripts
[后续步骤]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[数据处理入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[移动服务 .NET 操作方法概念性参考]: /zh-cn/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
