---
title: "Azure 搜索管理 REST API 入门 | Microsoft Docs"
description: "使用管理 REST API 管理托管的云 Azure 搜索服务"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: cd4c41d8-81bd-4609-9a37-e112ddf1f21f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3154c35b5c25aaa0f53cc66e2d49028e57bfb1e1


---
# <a name="get-started-with-azure-search-management-rest-api"></a>Azure 搜索管理 REST API 入门
> [!div class="op_single_selector"]
> * [门户](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](search-get-started-management-api.md)
> 
> 

Azure 搜索 REST 管理 API 是用于在门户中执行管理任务的一种编程替代方法。 服务管理操作包括创建或删除服务、调用服务和管理密钥。 本教程随附用于演示服务管理 API 的示例客户端应用程序。 它还包括在本地开发环境中运行示例所需的配置步骤。

若要完成本教程，你需要：

* Visual Studio 2012 或 2013
* 示例客户端应用程序下载

完成本教程期间将预配两个服务：Azure 搜索和 Azure Active Directory (AD)。 此外，你将在 Azure 中创建可在客户端应用程序和资源管理器终结点之间建立信任的 AD 应用程序。

需要使用 Azure 帐户才能完成本教程。

## <a name="download-the-sample-application"></a>下载示例应用程序
本教程基于采用 C# 编写的 Windows 控制台应用程序，你可以在 Visual Studio 2012 或 2013 中编辑和运行它

可通过 [Azure 搜索 .NET 管理 API 演示](https://github.com/Azure-Samples/search-dotnet-management-api/)在 Github 上找到客户端应用程序。

## <a name="configure-the-application"></a>配置应用程序
必须启用身份验证，以便可以接受从客户端应用程序发送到资源管理器终结点的请求，然后才能运行示例应用程序。 身份验证要求源自 [Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx)，这是通过 API 请求的所有与门户相关操作的基础，包括与搜索服务管理相关的操作。 Azure 搜索的服务管理 API 只是 Azure Resource Manager 的一项扩展，因此它继承其依赖项。  

Azure Resource Manager 需要使用 Azure Active Directory 服务作为其标识提供者。 

为了获取将允许请求访问资源管理器的访问令牌，客户端应用程序包含调用 Active Directory 的代码段。 代码段以及使用代码段的先决条件步骤引用自本文：[对 Azure Resource Manager 请求进行身份验证](http://msdn.microsoft.com/library/azure/dn790557.aspx)。

你可以按照上面链接中的说明，或使用本文档中的步骤（如果想要分步完成本教程）。

在本部分中，你将执行以下任务：

1. 创建 AD 服务
2. 创建 AD 应用程序
3. 通过注册有关你下载的示例客户端应用程序的详细信息，配置 AD 应用程序
4. 使用它将用于获取其请求的相关授权的值加载示例客户端应用程序

> [!NOTE]
> 这些链接提供有关使用 Azure Active Directory 对资源管理器的客户端请求进行身份验证的背景知识：[Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx)、[对 Azure Resource Manager 请求进行身份验证](http://msdn.microsoft.com/library/azure/dn790557.aspx)和 [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)。
> 
> 

### <a name="create-an-active-directory-service"></a>创建 Active Directory 服务
1. 登录到 [Azure 门户](https://manage.windowsazure.com)。
2. 向下滚动左侧导航窗格，然后单击“Active Directory”。
3. 单击“新建”，依次打开“应用程序服务” | “Active Directory”。 在此步骤中，将创建新的 Active Directory 服务。 此服务将托管 AD 应用程序，现在可通过执行几个步骤定义该应用程序。 创建新的服务有助于将该教程与你可能已托管在 Azure 中的其他应用程序隔离开。
4. 依次单击“目录” | “自定义创建”。
5. 输入服务名称、域和地理位置。 域必须是唯一的。 单击复选标记以创建服务。
   
     ![][5]

### <a name="create-a-new-ad-application-for-this-service"></a>为此服务创建新的 AD 应用程序
1. 选择刚刚创建的“SearchTutorial”Active Directory 服务。
2. 在顶部菜单中单击“应用程序”。 
3. 单击“添加应用程序”。 AD 应用程序存储有关将用作标识提供者的客户端应用程序的信息。  
4. 选择“添加我的组织正在开发的应用程序”。 此选项为不会发布到应用程序库的应用程序提供注册设置。 由于客户端应用程序不是应用程序库的一部分，所以此选项适用于本教程。
   
     ![][6]
5. 输入名称，例如“Azure-Search-Manager”。
6. 为应用程序类型选择“本机客户端应用程序”。 这适用于示例应用程序；它恰好是 Windows 客户端（控制台）应用程序，而不是 Web 应用程序。
   
     ![][7]
7. 在重定向 URI 中，输入“http://localhost/Azure-Search-Manager-App”。 这是 Azure Active Directory 在响应 OAuth 2.0 授权请求时将用户代理重定向到的 URI。 该值不需要是物理终结点，但必须是有效的 URI。 
   
    为实现本教程的目的，该值可以是任意值，但你输入的所有内容都会变为示例应用程序中管理连接所需的输入。 
8. 单击复选标记，创建 Active Directory 应用程序。 你应在左侧导航窗格中看到“Azure-Search-Manager-App”。

### <a name="configure-the-ad-application"></a>配置 AD 应用程序
1. 单击刚刚创建的 AD 应用程序“Azure-Search-Manager-App”。 此时应看到它在左侧导航窗格中列出。
2. 单击顶部菜单中的“配置”。
3. 向下滚动到权限，然后选择“Azure 管理 API”。 在此步骤中，指定客户端应用程序需要访问的 API（在此情况下，为 Azure Resource Manager API）以及所需的访问级别。
4. 在“委派权限”中，单击下拉列表并选择“访问 Azure 服务管理(预览版)”。
   
    ![][8]
5. 保存更改。 

使应用程序配置页保持打开。 在下一步中，将复制此页面中的值并将它们输入到示例应用程序。

### <a name="load-the-sample-application-program-with-registration-and-subscription-values"></a>通过注册和订阅值加载示例应用程序
在此部分中，将在 Visual Studio 中编辑解决方案，替换从门户中获取的有效值。
将要添加的值显示在 Program.cs 顶部附近：

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

如果你还没有[从 GitHub 下载示例应用程序](https://github.com/Azure-Samples/search-dotnet-management-api/)，在执行此步骤时将需要它。

1. 在 Visual Studio 中打开“ManagementAPI.sln”。
2. 打开 Program.cs。
3. 提供 `ClientId`。 从在上一步中保持打开的 AD 应用程序配置页中，复制门户中 AD 应用程序配置页中的客户端 ID，然后将其粘贴到 Program.cs。
4. 提供 `RedirectUrl`。 复制同一门户页面中的重定向 URI，然后将其粘贴到 Program.cs。
   
    ![][9]
5. 提供 `TenantID.` 
   
   * 返回到 Active Directory | SearchTutorial（服务）。 
   * 单击顶部栏中的“应用程序”。 
   * 单击页面底部的“查看终结点”。 
   * 复制列表底部的 OAuth 2.0 授权终结点。 
   * 将该终结点粘贴到 TenantID，从而剪裁租户 ID 以外的所有 URI 参数的值。
     
     如果为“https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0”，则删除“55e324c7-1656-4afe-8dc3-43efcd4ffa50”以外的所有内容。
     
     ![][10]
6. 提供 `SubscriptionID`。
   
   * 转到门户主页。
   * 单击左侧导航窗格底部的“设置”。
   * 从“订阅”选项卡中，复制订阅 ID 并将其粘贴到 Program.cs。
7. 进行保存，然后生成解决方案。

## <a name="explore-the-application"></a>了解应用程序
在第一个方法调用上添加断点，以便可以单步执行该程序。 按“F5”运行该应用程序，然后按“F11”单步执行代码。

示例应用程序为现有 Azure 订阅创建免费的 Azure 搜索服务。 如果你的订阅中已有免费服务，该示例应用程序将失败。 每个订阅中只允许一个免费搜索服务。

1. 从解决方案资源管理器中打开 Program.cs，然后转到 Main(string[] void) 函数。 
2. 请注意，**ExecuteArmRequest** 用于针对 Azure Resource Manager 终结点执行请求；有关指定的 `subscriptionID`，请参阅 `https://management.azure.com/subscriptions`。 此方法适用于整个程序，可使用 Azure Resource Manager API 或搜索管理 API 执行操作。
3. 必须对 Azure Resource Manager 请求进行身份验证和授权。 这通过使用 **GetAuthorizationHeader** 方法完成、通过 **ExecuteArmRequest** 方法调用、引用自[对 Azure Resource Manager 请求进行身份验证](http://msdn.microsoft.com/library/azure/dn790557.aspx)。 请注意，**GetAuthorizationHeader** 调用 `https://management.core.windows.net` 获取访问令牌。
4. 系统会提示你使用对于你的订阅有效的用户名和密码登录。
5. 接下来，向 Azure Resource Manager 提供程序注册新的 Azure 搜索服务。 同样，这是 **ExecuteArmRequest** 方法，当前使用目的是通过 `providers/Microsoft.Search/register` 在 Azure 上为你的订阅创建搜索服务。 
6. 该程序的剩余部分使用 [Azure 搜索管理 REST API](http://msdn.microsoft.com/library/dn832684.aspx)。 请注意，此 API 的 `api-version` 不同于 Azure Resource Manager API 版本。 例如，`/listAdminKeys?api-version=2014-07-31-Preview` 显示 Azure 搜索管理 REST API 的 `api-version`。
   
    后续操作将检索你刚刚创建的服务定义、管理员 API 密钥：重新生成并检索密钥、更改副本和分区，最终删除服务。
   
    更改服务副本或分区计数时，如果你使用的是免费版本，此操作将会失败。 仅标准版本可以使用其他分区和副本。
   
    最后一个操作是删除服务。

## <a name="next-steps"></a>后续步骤
完成本教程后，你可能希望了解有关服务管理或 Active Directory 服务身份验证的详细信息：

* 了解有关将客户端应用程序与 Active Directory 集成的详细信息。 请参阅[将应用程序集成到 Azure Active Directory 中](http://msdn.microsoft.com/library/azure/dn151122.aspx)。
* 了解有关 Azure 中的其他服务管理操作的信息。 请参阅[管理你的服务](http://msdn.microsoft.com/library/azure/dn578292.aspx)。

<!--Anchors-->
[下载示例应用程序]: #Download
[配置应用程序]: #config
[了解应用程序]: #explore
[后续步骤]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[在 Microsoft Azure 中管理你的搜索解决方案]: search-manage.md
[Azure 搜索开发工作流]: search-workflow.md
[创建你的第一个 Azure 搜索解决方案]: search-create-first-solution.md
[使用 Azure 搜索创建地理空间搜索应用]: search-create-geospatial.md






<!--HONumber=Nov16_HO3-->


