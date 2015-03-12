<properties urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="移动服务和 Azure Active Directory 中基于角色的访问控制（Windows 应用商店）| 移动开发人员中心" metaKeywords="" description="了解如何基于 Windows 应用商店应用程序中的 Azure Active Directory 角色控制访问。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# 移动服务和 Azure Active Directory 中基于角色的访问控制

[WACOM.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]

基于角色的访问控制 (RBAC) 是对用户可充当的角色分配权限的做法，明确定义特定类别的用户可以或者不可以执行哪些操作。本教程将指导你了解如何将基本 RBAC 添加到 Azure 移动服务。

本教程将演示基于角色的访问控制，检查每个用户在 Azure Active Directory (AAD) 中定义的"销售"组的成员资格。访问检查将在移动服务后端中使用 Azure Active Directory 的 [Graph 客户端库]来完成。只有属于"销售"组的用户才能查询数据。


>[AZURE.NOTE] 本教程旨在扩充身份验证知识以加入授权实践。你应该先使用 Azure Active Directory 身份验证提供程序完成[向应用程序添加身份验证]教程。本教程将继续更新[向应用程序添加身份验证]教程中使用的 TodoItem 应用程序。

本教程将指导你完成以下步骤：

1. [创建具有成员资格的销售组]
2. [为集成的应用程序生成密钥]
3. [创建自定义授权属性]
4. [将基于角色的访问检查添加到数据库操作]
5. [测试客户端访问]

本教程需要的内容如下：

* 在 Windows 8.1 上运行的 Visual Studio 2013。
* 使用 Azure Active Directory 身份验证提供程序完成[向应用程序添加身份验证]教程。
* 完成[存储服务器脚本]教程，以熟悉如何使用 Git 存储库来存储服务器脚本。
 


## <a name="create-group"></a>创建具有成员资格的销售组

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]


## <a name="generate-key"></a>为集成的应用程序生成密钥


在学习[向应用程序添加身份验证]教程的过程中，你在完成[注册以使用 Azure Active Directory 登录名]步骤时为集成的应用程序创建了注册。在本部分中，你将生成在使用该集成应用程序客户端 ID 读取目录信息时所用的密钥。 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-custom-authorization-attribute"></a>在移动服务上创建自定义授权属性 

在本部分中，你将创建一个可用来针对移动服务操作执行访问权限检查的新自定义授权属性。该属性将基于传递给它的角色名称查找 Active Directory 组。然后，将基于该组的成员资格执行访问权限检查。

1. 在 Visual Studio 中，右键单击移动服务 .NET 后端项目，然后单击"管理 NuGet 包"。

2. 在"NuGet Package Manager"对话框中的搜索条件内，输入 **ADAL** 以查找并安装移动服务的 **Active Directory 身份验证库**。

3. 此外，在 NuGet Package Manager 中，安装移动服务的 **Microsoft Azure Active Directory Graph 客户端库**。


4. 在 Visual Studio 中，右键单击你的移动服务项目，然后依次单击"添加"和"新建文件夹"。将新文件夹命名为 **Utilities**。

5. 在 Visual Studio 中，右键单击新的 **Utilities** 文件夹，并添加名为 **AuthorizeAadRole.cs** 的新类文件。

    ![][0]

6. 在 AuthorizeAadRole.cs 文件的顶部添加以下 `using` 语句。 

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7. 在 AuthorizeAadRole.cs 中，将以下枚举类型添加到 Utilities 命名空间。在此示例中，我们只需处理 **Sales** 角色。其他各项只是你可能要使用的组的示例。

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8. 在 AuthorizeAadRole.cs 中，将以下 `AuthorizeAadRole` 类定义添加到 Utilities 命名空间。

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9. 在 AuthorizeAadRole.cs 中，将以下 `GetAADToken` 方法添加到 `AuthorizeAadRole` 类。

    >[AZURE.NOTE] 你应该缓存令牌，而不是每次执行访问权限检查时都创建一个新令牌。接着，请在尝试使用令牌根据 [Graph 客户端库]中的说明引发 AccessTokenExpiredException 时刷新缓存。为简单起见，以下代码中并未演示此操作，但这可以减轻 Active Directory 的额外网络流量。  

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;
            
            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
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

10. 在 AuthorizeAadRole.cs 中，使用以下代码更新 `AuthorizeAadRole` 类中的 `OnAuthorization` 方法。此代码使用 [Graph 客户端库]查找对应于角色的 Active Directory 组。然后，它会检查用户在该组中的成员资格，以授权给用户。

    >[AZURE.NOTE] 此代码将按名称查找 Active Directory 组。在许多情况下，将组 ID 存储为移动服务应用程序设置是较好的做法。这是因为组名称可能会更改，而 ID 会保持相同。但是，当组名称更改时，角色的作用域中通常至少有一项更改可能也需要移动服务代码的更新。  

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
			// through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
						"User is not logged in or not a member of the required group");
            }
        }

8. 保存对 AuthorizeAadRole.cs 所做的更改。

## <a name="add-access-checking"></a>将基于角色的访问检查添加到数据库操作

1. 在 Visual Studio 中，展开移动服务项目中的 **Controllers** 文件夹。打开 TodoItemController.cs。

2. 在 TodoItemController.cs 中，为包含自定义授权属性的 utilities 命名空间添加 `using` 语句。 

        using todolistService.Utilities;

3. 在 TodoItemController.cs 中，可以根据检查访问权限的方式，将属性添加到控制器类或单个方法。如果你要让所有控制器操作根据相同的角色来检查访问权限，只需将属性添加到类。请按如下所示将属性添加到类，以测试本教程。

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    如果你只想要检查插入、更新和删除操作的访问权限，则应使用以下方式仅设置这些方法的属性。

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. 保存 TodoItemController.cs 并生成移动服务，以验证是否没有语法错误。
5. 将移动服务发布到 Azure 帐户。


## <a name="test-client"></a>测试客户端的访问

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]





<!-- Anchors. -->
[创建具有成员资格的销售组]: #create-group
[为集成的应用程序生成密钥]: #generate-key
[创建自定义授权属性]: #create-custom-authorization-attribute
[将基于角色的访问检查添加到数据库操作]: #add-access-checking
[测试客户端访问]: #test-client



<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[向应用程序添加身份验证]: /zh-cn/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[如何向 Azure Active Directory 注册]: /zh-cn/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure 管理门户]: https://manage.windowsazure.cn/
[目录同步方案]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[存储服务器脚本]: /zh-cn/documentation/articles/mobile-services-store-scripts-source-control/
[注册以使用 Azure Active Directory 登录名]: /zh-cn/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph 客户端库]: http://go.microsoft.com/fwlink/?LinkId=510536
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
