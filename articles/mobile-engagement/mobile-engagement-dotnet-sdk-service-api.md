---
title: "使用.NET SDK 访问 Azure Mobile Engagement 服务 API"
description: "描述如何使用 Mobile Engagement .NET SDK 访问 Azure Mobile Engagement 服务 API"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 57b2abc37551a782cb7106b9fc4540ce2ba37732


---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>使用.NET SDK 访问 Azure Mobile Engagement 服务 API
Azure Mobile Engagement 公开了一组 API，供你管理设备、推送/市场宣传营销活动等。若要与这些 API 进行交互，我们还为你提供了一个 [Swagger 文件](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)，可以与为你的首选语言生成 SDK 的工具一起使用。 我们建议使用 [AutoRest](https://github.com/Azure/AutoRest) 工具从我们提供的 Swagger 文件生成你的 SDK。 

我们以类似的方式创建了一个 .NET SDK，以允许你使用一个 C# 包装与这些 API 进行交互，并且你不需要执行身份验证令牌协商，也不必自动刷新。  

此示例通过遵循下面这组步骤来使用.NET SDK：

1. 首先，需要使用 Azure Active Directory 为你的 API 设置身份验证，如[此处](mobile-engagement-api-authentication.md#authentication)所述。 在完成这些步骤之后，你应具有一个有效 **SubscriptionId**、**TenantId**、**ApplicationId** 和**密钥**。 
2. 我们将使用一个简单的 Windows 控制台应用来演示如何借助创建公告营销活动的方案使用 .NET SDK。 先打开 Visual Studio，创建一个**控制台应用程序**。   
3. 接下来你需要下载 .NET SDK，[此处](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).NET SDK 可用作 Nuget 库中的 **Microsoft Azure Engagement 管理库**。
   如果从 Visual Studio 安装 Nuget，你需要确保在搜索软件包时已选中**包含预发行版**选项的复选标记：
   
    ![][1]
4. 在 `Program.cs` 文件中，添加以下命名空间：
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. 接下来，你需要定义以下常量，我们会将其用于身份验证以及与用以创建公告营销活动的 Mobile Engagement 应用进行交互︰
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. 定义 `EngagementManagementClient` 变量，我们将用该变量来调用 Mobile Engagement SDK 方法︰
   
        static EngagementManagementClient engagementClient; 
7. 将以下内容添加到 `Main` 方法：
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. 定义下面的方法，该方法负责初始化 `EngagementManagementClient`（首先进行身份验证，然后将自身与计划用以创建公告营销活动的 Mobile Engagement 应用相关联）︰
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > 请注意，你需要使用在 Azure 管理门户中为 AppName 参数定义的**应用资源名称**。 
   > 
   > 
9. 最后，定义 CreateCampaign 方法，该方法负责使用先前初始化的 EngagementClient 创建一个简单的 **AnyTime** & **Notification-only** 营销活动（具有一个标题和消息）︰ 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. 运行控制台应用，在成功创建营销活动时，你将看到以下内容︰
    
    **Campaign Id '159' was created successfully and it is in 'draft' state**（营销活动 Id '159' 已成功创建，并且它处于 'draft' 状态）

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png



<!--HONumber=Nov16_HO3-->


