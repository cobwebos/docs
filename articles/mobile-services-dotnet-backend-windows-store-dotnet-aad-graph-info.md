<properties urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="访问 Azure Active Directory Graph 信息（Windows 应用商店）| 移动开发人员中心" metaKeywords="" description="了解如何使用 Windows 应用商店应用程序中的 Graph API 访问 Azure Active Directory 信息。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# 访问 Azure Active Directory Graph 信息

[WACOM.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]



与移动服务随附的其他标识提供程序一样，Azure Active Directory (AAD) 提供程序也支持丰富的 [Graph 客户端库]，可用于以编程方式访问目录。在本教程中，你会根据使用 [Graph 客户端库]从目录中检索的其他用户信息来更新 ToDoList 应用程序，以个人化经过身份验证的用户的应用程序体验。

>[AZURE.NOTE] 本教程旨在扩充使用 Azure Active Directory 进行身份验证的知识。你应该已使用 Azure Active Directory 身份验证提供程序完成[向应用程序添加身份验证]教程。本教程将继续更新[向应用程序添加身份验证]教程中使用的 TodoItem 应用程序。 



本教程将指导你完成以下步骤：


1. [在 AAD 中为应用程序注册生成访问密钥]
2. [创建 GetUserInfo 自定义 API]
3. [更新应用程序以使用自定义 API]
4. [测试应用程序]

## 先决条件 

在开始本教程之前，必须已完成以下移动服务教程：

+ [身份验证入门]<br/>向 TodoList 示例应用程序添加登录要求。

+ [自定义 API 教程]<br/>演示如何调用自定义 API。 



## <a name="generate-key"></a>在 AAD 中为应用程序注册生成访问密钥


在学习[向应用程序添加身份验证]教程的过程中，你在完成[注册以使用 Azure Active Directory 登录名]步骤时为集成的应用程序创建了注册。在本部分中，你将生成在使用该集成应用程序客户端 ID 读取目录信息时所用的密钥。 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>创建 GetUserInfo 自定义 API

在此部分中，你将要创建 GetUserInfo 自定义 API，该 API 使用 [Graph 客户端库]从 AAD 中检索有关用户的其他信息。

如果你从未将自定义 API 与移动服务结合使用过，请在完成本部分之前，参考[自定义 API 教程]。

1. 在 Visual Studio 中，右键单击移动服务 .NET 后端项目，然后单击"管理 NuGet 包"。
2. 在"NuGet Package Manager"对话框中的搜索条件内，输入 **ADAL** 以查找并安装移动服务的 **Active Directory 身份验证库**。
3. 此外，在 NuGet Package Manager 中，安装移动服务的 **Microsoft Azure Active Directory Graph 客户端库**。

4. 在 Visual Studio 中，右键单击移动服务项目的 **Controllers** 文件夹，然后单击"添加"以添加名为 `GetUserInfoController` 的新 **Microsoft Azure 移动服务自定义控制器**。客户端将调用此 API，以从 Active Directory 中获取用户信息。

5. 在新的 GetUserInfoController.cs 文件中，添加以下 `using` 语句。

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. 在 GetUserInfoController.cs 中，将以下 `GetAADToken` 方法添加到类。

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    此方法使用你在 [Azure 管理门户]中的移动服务上配置的应用程序设置来获取用于访问 Active Directory 的令牌。

7. 在 GetUserInfoController.cs 中，将以下 `GetAADUser` 方法添加到类。

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    此方法将获取已授权用户的 Active Directory 对象 ID，然后使用 Graph 客户端库从 Active Diretory 中获取用户的信息。


8. 在 GetUserInfoController.cs 中，将  `Get` 方法替换为以下方法。此方法将返回 Graph 客户端库的  `User` 对象，并要求已授权的用户调用 API。

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. 保存更改并生成服务，以验证是否没有语法错误。
10. 将移动服务项目发布到 Azure 帐户。 


## <a name="update-app"></a>将应用程序更新为使用 GetUserInfo

在本部分中，你将更新你在[向应用程序添加身份验证]教程中实现的 `AuthenticateAsync` 方法，以调用自定义 API 并从 AAD 返回有关用户的其他信息。 

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>测试应用

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





## <a name="next-steps"></a>后续步骤

在下一教程[在移动服务中配合 AAD 进行基于角色的访问控制]中，你将结合使用基于角色的访问控制与 Azure Active Directory (AAD) 来检查组成员资格，然后允许访问。 



<!-- Anchors. -->
[在 AAD 中为应用程序注册生成访问密钥]: #generate-key
[创建 GetUserInfo 自定义 API]: #create-api
[更新应用程序以使用自定义 API]: #update-app
[测试应用程序]: #test-app
[后续步骤]:#next-steps

<!-- Images -->


<!-- URLs. -->
[向应用程序添加身份验证]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[如何向 Azure Active Directory 注册]: /zh-cn/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure 管理门户]: https://manage.windowsazure.cn/
[自定义 API 教程]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[存储服务器脚本]: /zh-cn/documentation/articles/mobile-services-store-scripts-source-control/
[注册以使用 Azure Active Directory 登录名]: /zh-cn/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph 客户端库]: http://go.microsoft.com/fwlink/?LinkId=510536
[获取用户]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[在移动服务中配合 AAD 进行基于角色的访问控制]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
