---
title: 通过机器学习进行电子商务目录审查和使用 Azure 内容审查器实现 AI | Microsoft Docs
description: 通过机器学习和 AI 自动审查电子商务目录
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 6177758eaa3e611ad67da0778d889df48b052d90
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095745"
---
# <a name="ecommerce-catalog-moderation-with-machine-learning"></a>通过机器学习进行电子商务目录审查

在本教程中，我们学习如何通过将计算机辅助 AI 技术与人工审查相结合实现基于机器学习的智能电子商务目录审查以提供智能目录系统。

![分类产品图像](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>业务场景

使用计算机辅助技术产品图像分为以下几类并进行审查：

1. 成人（裸露）
2. 不雅（暗示性）
3. 名人
4. 美国国旗
5. 玩具
6. 笔

## <a name="tutorial-steps"></a>教程步骤

教程通过以下步骤引导你完成学习：

1. 注册和创建内容审查器团队。
2. 为可能出现的名人和国旗内容配置审查标记（标签）。
3. 使用内容审查器的图像 API 扫描可能出现的成人和不雅内容。
4. 使用计算机视觉 API 扫描可能出现的名人。
5. 使用自定义影像服务扫描可能出现的国旗。
6. 提供细致的扫描结果，供人工审查和最终决策。

## <a name="create-a-team"></a>创建团队

请参阅[快速入门](quick-start.md)页面来注册内容审查器和创建团队。 请注意，“团队 ID”来自“凭据”页面。


## <a name="define-custom-tags"></a>定义自定义标记

请参阅[标记](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags)文章来添加自定义标记。 除了内置的“成人”和“不雅”标记外，新标记允许审查工具显示标记的描述性名称。

在本例中，我们定义如下标记（“名人”、“国旗”、“美国”、“玩具”、“笔”）：

![配置自定义标记](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>列举 API 密钥和终结点

1. 本教程使用三个 API 和相应的密钥和 API 终结点。
2. API 终结点基于订阅区域和内容审查器审查团队 ID 有所不同。

> [!NOTE]
> 本教程是为在下列终结点中可见的区域中使用订阅密钥专门设计的。 请务必匹配 API 密钥与地区 URL，否则密钥可能无法用于下列终结点：

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>扫描成人和不雅内容

1. 此函数将图像 URL 和键值对数组作为参数。
2. 它调用内容审查器的图像 API 以获取成人和不雅分数。
3. 如果分数大于 0.4（范围从 0 到 1），则将“ReviewTags”数组设为“True”。
4. “ReviewTags”数组用于突出显示审查工具中的相应标记。

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>扫描名人

1. 注册[计算机视觉 API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) 的[免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)。
2. 单击“获取 API 密钥”按钮。
3. 接受条款。
4. 要登录，从可用的 Internet 帐户列表中选择。
5. 请注意，API 密钥已显示在服务页面上。
    
   ![计算机视觉 API 密钥](images/tutorial-computer-vision-keys.PNG)
    
6. 请参阅项目源代码，以了解使用计算机视觉 API 扫描图像的函数。

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>分类为国旗、玩具和笔

1. [登录](https://azure.microsoft.com/en-us/services/cognitive-services/custom-vision-service/)到[自定义视觉 API 预览](https://www.customvision.ai/)。
2. 使用[快速入门](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)构建自定义分类器来检测是否可能存在国旗、玩具和笔。
   ![自定义视觉训练图像](images/tutorial-ecommerce-custom-vision.PNG)
3. 为自定义分类器[获取预测终结点 URL](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api)。
4. 请参阅项目源代码，以查看调用分类器预测终结点扫描图像的函数。

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>“人工干预”审查

1. 在上述部分中，已经扫描了成人和不雅（内容审查器）、名人（计算机视觉）和国旗（自定义视觉）的传入图像。
2. 基于每次扫描的匹配阈值，将细致的案例提供给审查工具中的人工审查。
        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>批量提交图像

1. 本教程假设一个“C:Test”目录，其中有一个提供图像 Url 列表的文本文件。
2. 下列代码检查文件是否存在，然后将所有 Url 读入内存。
            // Check for a test directory for a text file with the list of Image URLs to scan var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>启动全部扫描

1. 此顶级函数遍历之前提及的文本文件中的所有图像 URL。
2. 它使用每个 API 进行扫描，如果匹配置信度分数低于标准，则为人工审查器创建审查。
             // for each image URL in the file... foreach (var Url in Urls) { // Initiatize a new review tags array ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>许可证

所有 Microsoft 认知服务 SDK 和示例均获得 MIT 许可证的许可。 有关详细信息，请参阅[许可证](https://microsoft.mit-license.org/)。

## <a name="developer-code-of-conduct"></a>开发人员行为准则

使用认知服务（包括此客户端库和示例）的开发人员应遵循“面向 Microsoft 认知服务的开发人员行为准则”（参见 http://go.microsoft.com/fwlink/?LinkId=698895）。

## <a name="next-steps"></a>后续步骤

通过使用 Github 上的[项目源文件](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)构建和扩展教程。
