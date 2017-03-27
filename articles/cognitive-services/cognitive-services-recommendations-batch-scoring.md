---
title: "分批获取建议：机器学习建议 API | Microsoft Docs"
description: "Azure 机器学习建议 - 分批获取建议"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 325d4922-8a07-4e67-99e0-f513201f14f7
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: e63218d9c882d84342a3992f05e0a8c9f306d9c6


---
# <a name="get-recommendations-in-batches"></a>批量获取建议
> [!NOTE]
> 批量获取建议比一次获取一条建议更为复杂。 请查看 API，了解有关如何获取单个请求的建议信息：
> 
> [项到项的建议](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [用户到项的建议](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
> 
> 批处理计分仅适用于 2016 年 7 月 21 日之后创建的生成。
> 
> 

有时候您需要一次性获取多个项的建议。 例如，您可能对建议缓存，甚至分析所获取的建议类型感兴趣。

批处理计分操作我们又称之为异步操作。 提交请求后，等待操作完成，然后收集结果。  

具体请遵照以下步骤：

1. 如果还没有 Azure 存储空间容器，创建一个。
2. 将描述每个建议请求的输入文件上传到 Azure Blob 存储。
3. 开始对批处理作业进行计分。
4. 等待异步操作完成。
5. 完成后，从 Blob 存储中收集结果。

我们来看看上述每个步骤。

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>如果还没有存储空间容器，创建一个。
转到 [Azure 门户](https://portal.azure.com)，如果没有存储帐户，新建一个。 为此，请导航到“**新建**”“ > **数据** + ”“**存储**”“ > **存储帐户**”。

拥有存储帐户后，需要创建 blob 容器，以便存储批处理执行的输入和输出。

将描述每个建议请求的输入文件上传到 Blob 存储（这里我们将该文件称为 input.json）。
拥有容器后，需要上传描述每个请求的文件，这些请求需要通过建议服务执行。

一个批处理只能执行特定生成中的一个请求类型。 我们将在下一节中介绍如何定义此信息。 现在，假设要执行特定生成外的项建议。 输入文件则包含每个请求的输入信息（在本例中为种子项）。

input.json 文件的外观如此示例所示：

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

如您所见，该文件是一个 JSON 文件，其中每个请求都具有发送建议请求所必需的信息。 为需要完成的请求创建相似的 JSON 文件，并将其复制到刚才在 Blob 存储中创建的容器中。

## <a name="kick-start-the-batch-job"></a>开始批处理作业
下一步是提交新的批处理作业。 有关详细信息，请查看 [API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/)。

API 的请求正文需要定义输入、输出和错误文件的存储位置。 还需要定义访问这些位置所必需的凭据。 此外，您还需要指定可应用于整个批处理的一些参数（要请求的建议类型、要使用的模型/生成、每个调用的结果数量等。）

请求正文的外观如以下示例所示：

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

此处需要注意几个要点：

* 当前，**authenticationType** 应始终设置为 **PublicOrSas**。
* 您需要获取共享访问签名 (SAS) 令牌，以允许建议 API 读取和写入 Blob 存储帐户或该帐户进行读取和写入。 有关如何生成 SAS 令牌的详细信息，可查看[建议 API 页面](../storage/storage-dotnet-shared-access-signature-part-1.md)。
* 当前支持的唯一 **apiName** 是 **ItemRecommend**，可用于项到项的建议。 批处理当前不支持用户到项的建议。

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>等待异步操作完成
启动批处理操作时，响应将返回“操作位置”标头，为您提供跟踪操作所必需的信息。
可以使用[检索操作状态 API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da) 跟踪操作，方法与跟踪生成操作的方法相似。

## <a name="get-the-results"></a>获取结果
操作完成后，如果没有发生错误，便可以从输出 Blob 存储收集结果。

输出的外观如以下示例所示。 为简洁起见，此示例介绍了仅有两个请求的批处理结果。

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>了解相关限制
* 每个订阅每次只能调用一个批处理作业。
* 批处理作业输入文件不能超过 2 MB。




<!--HONumber=Dec16_HO2-->


