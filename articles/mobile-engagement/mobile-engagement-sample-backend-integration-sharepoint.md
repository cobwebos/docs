---
title: "Azure Mobile Engagement - 后端集成"
description: "连接 Azure Mobile Engagement 与 SharePoint 后端，以从 SharePoint 中创建市场活动"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: d49f1094f4c3f170f3618f3e19e42266f9ae8858
ms.contentlocale: zh-cn
ms.lasthandoff: 03/01/2017

---
# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile Engagement - API 集成
在自动化的市场营销系统中，创建和激活市场营销活动也会自动发生。 为此，Azure Mobile Engagement 还允许使用 API 创建此类自动化的市场营销活动。 

通常，客户使用 Mobile Engagement 前端界面创建公告/投票等作为其市场营销活动的一部分。 但是，随着市场营销活动趋于成熟，就需要利用后端系统（如任何 CRM 系统或 CMS 系统，如 SharePoint）中锁定的数据，以便创建完全自动化的管道，基于来自后端系统中的数据流，在 Mobile Engagement 中动态地创建市场活动。 

![][5]

本教程会逐步讲解此类方案，其中 SharePoint 业务用户会使用市场营销数据填充 SharePoint 列表，自动化过程从该列表中选取项目，并使用可用的 REST API 连接 Mobile Engagement 系统，以基于 SharePoint 数据创建市场营销活动。 

> [!IMPORTANT]
> 一般情况下，你可以使用本示例作为起点，了解如何调用任何 Mobile Engagement REST API，因为它详细说明调用 API 的两个关键方面 - 身份验证和传递参数。 
> 
> 

## <a name="sharepoint-integration"></a>SharePoint 集成
1. SharePoint 列表示例类似如下所示。 **标题**、**类别**、**NotificationTitle**、**消息**和 **URL** 用于创建公告。 有一个名为 **IsProcessed** 的列，由示例自动化进程以控制台程序的形式使用。 你可以运行此控制台程序作为 Azure WebJob，以便你可以安排它或者可以直接使用 SharePoint 工作流，在将某个项插入到 SharePoint 列表中时创建并激活公告。 在此示例中，我们使用控制台程序逐一完成 SharePoint 列表中的项，并在 Azure Mobile Engagement 中为每个项创建公告，最后在公告创建成功时将 **IsProcessed** 标志标记为 true。
   
    ![][1]
2. 我们是在使用[此处](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c)的*使用客户端对象模型在 SharePoint Online 中进行远程身份验证*示例中的代码，以使用 SharePoint 列表进行身份验证。
3. 身份验证之后，我们会循环遍历列表项，以找出任何新创建的项（其 **IsProcessed** = false）。 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Mobile Engagement 集成
1. 我们找到需要处理的项后，将提取必要的信息以从列表项中创建公告，并调用 `CreateAzMECampaign` 来创建它，随后调用 `ActivateAzMECampaign` 来激活公告。 实质上，这些是调用到 Mobile Engagement 后端的 REST API 调用。 
2. Mobile Engagement REST API 需要一个**基本身份验证方案授权 HTTP 标头**，该标头由 `ApplicationId` 和 `ApiKey` 组成，你可以从 Azure 门户中获取。 请确保你正在使用的密钥是来自 **API 密钥**部分，而*不是*来自 **SDK 密钥** 部分。 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. 有关创建公告类型市场活动的信息，请参阅[文档](https://msdn.microsoft.com/library/azure/mt683750.aspx)。 你需要确保你指定市场活动 `kind` 作为*公告*和[负载](https://msdn.microsoft.com/library/azure/mt683751.aspx)，并将其作为 FormUrlEncodedContent 进行传递。 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. 创建公告后，你将在 Mobile Engagement 门户上看到如下所示的内容（请注意，State=Draft，Activated = N/A）
   
    ![][3]
5. `CreateAzMECampaign` 会创建公告市场活动并将其 ID 返回给调用方。 `ActivateAzMECampaign` 需要此 ID 作为参数，以激活该市场活动。 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. 激活公告后，你会在 Mobile Engagement 门户上看到如下所示的内容：
   
    ![][4]
7. 激活该市场活动后，满足该市场活动上的条件的任何设备就会开始看到通知。 
8. 你还会注意到创建公告市场活动后，标有 IsProcessed = false 的列表项已设置为 True。  

此示例创建了一个简单的公告市场活动，其中指定了大部分必需的属性。 你可以使用[此处](https://msdn.microsoft.com/library/azure/mt683751.aspx)的信息，从门户中根据需要对其进行自定义。 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png




