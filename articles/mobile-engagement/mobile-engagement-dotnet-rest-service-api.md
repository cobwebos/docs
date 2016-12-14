---
title: "使用 REST API 访问 Azure Mobile Engagement 服务 API"
description: "描述如何使用 Mobile Engagement REST API 访问 Azure Mobile Engagement 服务 API"
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 555342e88c912a3f43c578a40dc34933996ade4c
ms.openlocfilehash: 512276d151833ab5c65b663d8f2af43153e1d55b


---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>使用 REST 访问 Azure Mobile Engagement 服务 API
Azure Mobile Engagement 提供 [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx)，让你可管理设备、市场宣传/推送市场活动等。此示例将使用 REST API 直接创建公告市场活动，然后将其激活并推送到一组设备。 

如果你不想直接使用 REST API，我们还提供了 [Swagger 文件](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)，可以与工具搭配使用来针对你的首选语言生成 SDK。 我们建议使用 [AutoRest](https://github.com/Azure/AutoRest) 工具从我们提供的 Swagger 文件生成你的 SDK。 我们以类似的方式创建了一个 .NET SDK，以允许你使用一个 C# 包装与这些 API 进行交互，并且你不需要执行身份验证令牌协商，也不必自动刷新。 如果你想要逐步了解使用此包装的类似示例，请参阅[服务 API.NET SDK 示例](mobile-engagement-dotnet-sdk-service-api.md)

此示例将使用 REST API 直接来创建并激活公告市场活动。 

## <a name="adding-a-username-appinfo-to-the-mobile-engagement-app"></a>将 user_name 应用信息添加到 Mobile Engagement 应用
此示例需要将应用信息标记添加到 Mobile Engagement 应用。 在应用的 Engagement 门户中，你可以通过单击“**设置**” > “**标记(应用信息)**” > “**新标记(应用信息)**”来添加标记。 添加名为 **user_name** 的新标记作为“**字符串**”类型。

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)

如果你遵循[适用于 Windows 通用应用的 Azure Mobile Engagement 入门](mobile-engagement-windows-store-dotnet-get-started.md)，则可以通过仅重写 `MainPage` 类中的 `OnNavigatedTo()` 标记来测试发送 **user_name** 标记，以发送类似以下代码的应用信息标记：

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }



## <a name="creating-the-service-api-app"></a>创建服务 API 应用
1. 首先，你将需要用于此示例的四个身份验证参数。 这些参数是 **SubscriptionId**、**TenantId**、**ApplicationId** 和 **Secret**。 若要获取这些身份验证参数，建议你使用[身份验证](mobile-engagement-api-authentication.md#authentication)教程中的*一次性设置（使用脚本）*部分讲述的 PowerShell 脚本方法。 
2. 我们将使用一个简单的 Windows 控制台应用，演示如何使用 REST Service API 来创建并激活新的公告市场活动。 打开 Visual Studio，创建一个新的**控制台应用程序**。   
3. 接下来，将 **Newtonsoft.Json** NuGet 包添加到你的项目中。
4. 在 `Program.cs` 文件中，为下列命名空间添加以下 `using` 语句：
   
        using System.IO;
        using System.Net;
        using Newtonsoft.Json.Linq;
        using Newtonsoft.Json;
5. 接下来，你需要在 `Program` 类中定义以下常量。 这些常量将用于对你正在创建公告市场活动的 Mobile Engagement 应用进行身份验证，并与之进行交互：

        // Parameters needed for authentication of API calls.
        // These are returned from the PowerShell script in the authentication tutorial. 
        // https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

        // The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
        // Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

        // New campaign id returned from creating the new campaign and used to activate and push the campaign
        // a set of devices.
        static String NewCampaignID = null;

        // This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


1. 将以下两个方法添加到 `Program` 类。 这两种方法将用于执行 REST API，并显示对控制台的响应。
   
        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;
   
            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;
   
            if (authToken != null)
                request.Headers.Add("Authorization", authToken);
   
            if (headers != null)
            {
                request.Headers.Add(headers);
            }
   
            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);
   
                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }
   
            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
   
            return response;
        }

        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

1. 将以下代码添加到 `Main` 方法，以生成具有你收集的身份验证参数的身份验证令牌：
   
        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************
   
        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);
   
        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }
2. 至此，我们已有一个有效的身份验证令牌，接下来就可以使用[创建市场活动](https://msdn.microsoft.com/library/azure/mt683742.aspx) REST API 创建新的市场宣传活动。 新的市场活动将是一个简单的**随时** & **仅通知**公告市场活动，带有标题和消息。 这将是一个手动推送市场活动，如以下屏幕快照中所示。 这意味着将只使用 API 进行推送。

    ![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

    将以下代码添加到 `Main` 方法，以创建公告市场活动： 

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ \"name\": \"BirthdayCoupon\", " +
                                        "\"type\": \"only_notif\", " +
                                        "\"deliveryTime\": \"any\", " +
                                        "\"notificationType\": \"popup\", " +
                                        "\"pushMode\":\"manual\", " +
                                        "\"notificationTitle\": \"Happy Birthday ${user_name}\", " +
                                        "\"notificationMessage\": \"Take extra 10% off on your orders today!\"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


1. 必须激活市场活动，才能将其推送到任何设备。 我们在 `NewCampaignID` 变量中保存新市场活动的 ID。 我们将使用此变量作为 URI 路径参数，以使用[激活市场活动](https://msdn.microsoft.com/library/azure/mt683745.aspx) REST API 来激活市场活动。 这样应可以将市场活动的状态更改为“**已计划**”，即使该活动将仅使用 API 进行手动推送。
   
    将以下代码添加到 `Main` 方法，以激活公告市场活动： 
   
        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************
   
        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";
   
        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);
   
        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }
2. 若要推送市场活动，我们需要为希望其接收通知的用户提供的设备 ID。 我们将使用[查询设备](https://msdn.microsoft.com/library/azure/mt683826.aspx) REST API 来获取所有设备 ID。 我们会将具有关联的 **user_name** 应用信息的每个设备 ID 添加到列表。
   
   将以下代码添加到 `Main` 方法，以获取所有设备 ID 并填充 deviceList：
   
       //************************************************************************
       //*** Now that the manualPush campaign is activated, get the deviceIds ***
       //*** that you want to trigger the push campaign on.                   ***
       //************************************************************************
   
       String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                 "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                  Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";
   
       Console.WriteLine("Getting device IDs...\n");
       HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
       Stream devicesStream = devicesResponse.GetResponseStream();
       StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
       dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
       devicesReader.Close();
       devicesStream.Close();
       DisplayResponse(devicesData, devicesResponse);
   
       if (devicesResponse.StatusCode == HttpStatusCode.OK)
       {
           Console.WriteLine("Got devices.");
           foreach (dynamic device in devicesData.value)
           {
               // Just adding all in this example
               if (device.appInfo.user_name != null)
               {
                   Console.WriteLine("Adding device for push campaign...");
                   Console.WriteLine("Device ID    : " + device.deviceId);
                   Console.WriteLine("user_name    : " + device.appInfo.user_name);
                   deviceList.Add((String)device.deviceId);
               }
           }
           Console.WriteLine();
       }
       else
       {
           Console.WriteLine("*** Failed to get devices.\n");
           return;
       }
3. 最后，我们将使用[推送市场活动](https://msdn.microsoft.com/library/azure/mt683734.aspx) REST API 将市场活动推送到我们列表中的所有设备 ID。 这是一个**应用内**通知。 因此，应用必须在设备上运行才能让用户收到通知。
   
   将以下代码添加到 `Main` 方法，以将市场活动推送到 deviceList 中的设备：
   
       //**************************************************************
       //*** Trigger the manualPush campaign on the desired devices ***
       //**************************************************************
   
       String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                 "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                  Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                  "/push?api-version=2014-12-01";
   
       Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
       String deviceIds = "{\"deviceIds\":" + JsonConvert.SerializeObject(deviceList) + "}";
       Console.WriteLine("\n" + deviceIds + "\n");
       HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
       Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
       StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
       dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
       pushDevicesReader.Close();
       pushDevicesStream.Close();
       DisplayResponse(pushDevicesData, pushDevicesResponse);
   
       if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
       {
           Console.WriteLine("Triggered push on new campaign.\n");
       }
       else
       {
           Console.WriteLine("*** Failed to push campaign to the device list.\n");
       }
4. 生成并运行控制台应用。 输出应类似如下所示：

        C:\Users\Wesley\AzME_Service_API_Rest\test.exe

        Getting authorization token...

        HTTP Status 200 : OK
        {
          "token_type": "Bearer",
          "expires_in": "3599",
          "expires_on": "1458085162",
          "not_before": "1458081262",
          "resource": "https://management.core.windows.net/",
          "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
        b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
        NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
        MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
        F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
        hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
        }

        Got authorization token...
        Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
        aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
        Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
        OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
        iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
        vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H

        Creating new campaign...

        HTTP Status 201 : Created
        {
          "id": 24,
          "state": "draft"
        }

        Created new campaign...
        New Campaign Id    : 24

        Activating new campaign (ID : 24)...

        HTTP Status 200 : OK
        {
          "id": 24,
          "state": "scheduled"
        }

        Activated new campaign...
        New Campaign State : scheduled

        Getting device IDs...

        HTTP Status 200 : OK
        {
          "value": [
            {
              "meta": {
                "lastSeen": 1458080534371,
                "firstSeen": 1458080534371,
                "lastLocation": 1458081389617,
                "lastInfo": 1458080571603
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
            },
            {
              "meta": {
                "lastSeen": 1457990584698,
                "firstSeen": 1457949384025,
                "lastLocation": 1457990914895,
                "lastInfo": 1457990584597
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "302486644890e26045884ee5aa0619ec"
            }
          ]
        }

        Got devices.
        Adding device for push campaign...
        Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
        user_name    : Wesley
        Adding device for push campaign...
        Device ID    : 302486644890e26045884ee5aa0619ec
        user_name    : Wesley

        Triggering push for new campaign (ID : 24)...
        
        {"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}

        HTTP Status 200 : OK
        {
          "invalidDeviceIds": []
        }

        Triggered push on new campaign.



<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png



<!--HONumber=Nov16_HO3-->


